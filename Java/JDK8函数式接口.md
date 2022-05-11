### 内置4大核心函数式接口

#### 一、Consumer<T>：消费型接口  void accept(T t)
描述： 有参数，无返回值类型的接口
```
    @Test
    public void test1 () {
        consumo(500, (x) -> System.out.println(x));
    }
    
    public void consumo (double money, Consumer<Double> c) {
        c.accept(money);
    }
```
#### 二、Supplier<T>：供给型接口 T get（） 
描述： 供给类型得接口，只有产出，没人输入，就是只有返回值，没有入参
```
    @Test
    public void test2 () {
        Random ran = new Random();
        List<Integer> list = supplier(10, () -> ran.nextInt(10));
        
        for (Integer i : list) {
            System.out.println(i);
        }
    }
    
    /**
     * 随机产生sum个数量得集合
     * @param sum 集合内元素个数
     * @param sup
     * @return
     */
    public List<Integer> supplier(int sum, Supplier<Integer> sup){
        List<Integer> list = new ArrayList<Integer>();
        for (int i = 0; i < sum; i++) {
            list.add(sup.get());
        }
        return list;
    }
```
#### 三、Function<T, R>：函数型接口 R apply（T t）
描述：函数型接口，输入一个类型得参数，输出一个类型得参数，当然两种类型可以一致。
```
    @Test
    public void test3 () {
        String s = strOperar(" asdf ", x -> x.substring(0, 2));
        System.out.println(s);
        String s1 = strOperar(" asdf ", x -> x.trim());
        System.out.println(s1);
    }
    
    /**
     * 字符串操作
     * @param str 需要处理得字符串
     * @param fun Function接口
     * @return 处理之后得字符传
     */
    public String strOperar(String str, Function<String, String> fun) {
        return fun.apply(str);
    }
```
#### 四、Predicate<T>：断言型接口 boolean test（T t）
描述：断言型接口，输入一个参数，输出一个boolean类型得返回值。
```
    @Test
    public void test4 () {
        List<Integer> l = new ArrayList<>();
        l.add(102);
        l.add(172);
        l.add(13);
        l.add(82);
        l.add(109);
        List<Integer> list = filterInt(l, x -> (x > 100));
        for (Integer integer : list) {
            System.out.println(integer);
        }
    }
    
    /**
     * 过滤集合
     * @param list
     * @param pre
     * @return
     */
    public List<Integer> filterInt(List<Integer> list, Predicate<Integer> pre){
        List<Integer> l = new ArrayList<>();
        for (Integer integer : list) {
            if (pre.test(integer))
                l.add(integer);
        }
        return l;
    }

```