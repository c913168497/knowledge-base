### CompletableFuture是什么?
　　CompletableFuture是JDK8的新特性之一,是异步调用相关的API,用于简化异步调用,提高异步调用的效率
###  CompletableFuture有什么用?
　　CompletableFuture 是对 Future对象的增强(JDK1.5),解决了异步调用中的很多问题:
　　例如:多个异步任务之间的结果依赖问题,获取future结果时阻塞主线程的问题,以及提供了很多丰富的API用于函数式编程
###  CompletableFuture怎么用?
#### 核心的API

- 1.supplyAsync(参数1,指定异步任务(有返回值),参数2(可选)指定特定的线城池,如果不指定则使用默认线城池ForkJoin)
```java
CompletableFuture<U> supplyAsync(Supplier<U> supplier)
CompletableFuture<U> supplyAsync(Supplier<U> supplier,Executor executor)
```
- 2.runAsync(参数1,指定异步任务(无返回值),参数2(可选)指定特定的线城池,如果不指定则使用默认线城池ForkJoin)
```java
CompletableFuture<Void> runAsync(Runnable runnable)
CompletableFuture<Void> runAsync(Runnable runnable, Executor executor)
```
备注：使用公共的 ForkJoinPool 线程池执行，这个线程池默认线程数是 CPU 的核数。
可以设置 JVM option:-Djava.util.concurrent.ForkJoinPool.common.parallelism 来设置 ForkJoinPool 线程池的线程数，使用共享线程池将会有个弊端，一旦有任务被阻塞，将会造成其他任务没机会执行。

所以强烈建议根据任务类型不同，主动创建线程池，进行资源隔离，避免互相干扰。

### Demo实例

- 适合场景，将多个串行的操作转换为并行的操作，提高接口响应速度
- 函数式编程简化多线程异步操作的代码
```java
@Override
public CustomerTargetTrendVO customerTargetTrend(TargetTrendDTO targetTrendDTO) throws Exception {
    //构造查询条件
    TrendQueryDTO queryDTO = buildTrendQueryDTO(targetTrendDTO);
    if (ObjectUtils.isEmpty(queryDTO)) {
        return new CustomerTargetTrendVO();
    }

    log.info("TrendQueryDTO : [{}]", queryDTO);

    //下单客户数趋势
    CompletableFuture<List<CustomerTargetTrendVO.OrdedCustomerItem>> ordedCustomerFuture =
            CompletableFuture.supplyAsync(() -> dmRptWgysEnterpriseSumMapper.countOrdCustomerTrend(queryDTO)).thenApply(x -> JSON.parseArray(JSON.toJSONString(x), CustomerTargetTrendVO.OrdedCustomerItem.class));

    //下单实际采购量趋势
    CompletableFuture<List<CustomerTargetTrendVO.PurchaseAreaItem>> purchaseAreaFuture =
            CompletableFuture.supplyAsync(() -> dmRptWgysCustomerOrderDetailMapper.countPurchaseAreaTrend(queryDTO)).thenApply(x -> JSON.parseArray(JSON.toJSONString(x), CustomerTargetTrendVO.PurchaseAreaItem.class));

    //等待子线程任务完成
    CompletableFuture.allOf(ordedCustomerFuture, purchaseAreaFuture).join();

    //数据组装
    List<CustomerTargetTrendVO.OrdedCustomerItem> dbOrdedCustomerList = ordedCustomerFuture.get();
    List<CustomerTargetTrendVO.PurchaseAreaItem> purchaseAreaList = purchaseAreaFuture.get();

    return CustomerTargetTrendVO.builder().ordedCustomerList(dbOrdedCustomerList).purchaseAreaList(purchaseAreaList).build();
}
```

- 示例代码2
```java
 //所有订单
    CompletableFuture<OrdersBo.Item> allOrdersFuture = fillAllOrders(pd, ordersBo);
    //及时量订单
    CompletableFuture<OrdersBo.Item> inTimeOrdersFuture = fillInTimeOrders(pd, ordersBo);
    //超时量订单
    CompletableFuture<OrdersBo.Item> timeOutFuture = fillOverTimeOrders(pd, ordersBo);
    //失败量
    CompletableFuture<OrdersBo.Item> failOrderFutrue = fillFailOrders(pd, ordersBo);
    //异常量
    CompletableFuture<OrdersBo.Item> exceptionOrderFuture = fillExceptionOrders(pd, ordersBo);

//聚合几个查询的结果返回给前端
CompletableFuture.allOf(allOrdersFuture, inTimeOrdersFuture, failOrderFutrue, timeOutFuture, exceptionOrderFuture).join();
return ordersBo;

```
 
### 异常处理：
CompletableFuture  方法执行过程若产生异常，当调用 get，join获取任务结果才会抛出异常。

使用 whenComplete / handle 可以更加优雅的处理异常，whenComplete 与 handle 方法就类似于 try..catch..finanlly 中 finally 代码块。
无论是否发生异常，都将会执行的。

这两个方法区别在于  handle  支持返回结果。
```java
 //handle 同步处理
public <U> CompletionStage<U> handle (BiFunction<? super T, Throwable, ? extends U> fn);

//handle 异步处理
public <U> CompletionStage<U> handleAsync (BiFunction<? super T, Throwable, ? extends U> fn);

//handle 异步处理
public <U> CompletionStage<U> handleAsync (BiFunction<? super T, Throwable, ? extends U> fn, Executor executor);

//whenComplete 同步处理异常
public CompletableFuture<T> whenComplete(BiConsumer<? super T, ? super Throwable> action) {
    return uniWhenCompleteStage(null, action);
}

//whenComplete 异步处理异常
public CompletableFuture<T> whenCompleteAsync(BiConsumer<? super T, ? super Throwable> action) {
    return uniWhenCompleteStage(asyncPool, action);
}

CompletableFuture<Integer> f0 = CompletableFuture.supplyAsync(() -> (7 / 0)).thenApply(r -> r * 10)
        .handle((integer, throwable) -> {
            // 如果异常存在,打印异常，并且返回默认值
            if (throwable != null) {
                throwable.printStackTrace();
                return 0;
            } else {
                // 如果
                return integer;
            }
        });
```