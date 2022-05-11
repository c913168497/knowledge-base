### 执行器
- Executor 执行者根接口
- ExecutorService 执行者接口
- Executors 执行者工具类

### 得到异步执行结果
- Future 异步返回值 -> FutureTask
- Callable 返回值的任务接口
- CompletionService 管理已完成任务的执行者

### 重复执行与延期执行
- ScheduledExecutorService 可以调度任务延迟或返回执行的执行者
- ScheduleFuture 
- ScheduledThreadPoolExecutor

### 线程池
- newSingleThreadExecutor 单线程池 它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。
- newFixedThreadPool 固定线程池 最多n个线程运行 超出的线程会在队列中等待。
- newCachedThreadPool 缓存线程池 最多 Integer.MAX_VALUE 个线程运行 如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。
- newScheduledThreadPool 调度线程池 支持定时及周期性任务执行

### fork-join 框架
- ForkJoinPool
- ForkJoinTask<V>
