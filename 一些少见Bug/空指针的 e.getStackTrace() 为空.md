空指针的 e.getStackTrace() 为空
一般造成这种情况是 jvm 优化的结果
在接近执行两万次时，确实看到异常堆栈信息就没有了：
```
 public static void main(String[] args) { 
        int i = 0;
        String x= null;
        while (i < 100000000) {
            try {
                System.out.println("当前执行次数为："+i);
                getNPE(x);
            } catch (Exception e) {
                int lth = e.getStackTrace().length;
                System.out.println("length："+lth);
                e.printStackTrace();
                if(lth==0){
                    return;
                }
            }
            i++;
        }
    } 
        
    private static void getNPE(String x){
        System.out.println("当前字母为："+x.toString());
    }
```
这是因为jvm虚拟机优化

java version "1.7.0_71"
Java(TM) SE Runtime Environment (build 1.7.0_71-b14)
Java HotSpot(TM) 64-Bit Server VM (build 24.71-b01, mixed mode)
当增加虚拟机参数-XX:-OmitStackTraceInFastThrow后，执行了100w次以上，也不见异常堆栈信息丢失。

The compiler in the server VM now provides correct stack backtraces for all "cold" built-in exceptions. For performance purposes, when such an exception is thrown a few times, the method may be recompiled. After recompilation, the compiler may choose a faster tactic using preallocated exceptions that do not provide a stack trace. To disable completely the use of preallocated exceptions, use this new flag: -XX:-OmitStackTraceInFastThrow.

这里的"cold"，个人以为是与hotspot VM中hot相对的意思，意思是非热点内置异常。如果异常被抛出数次，就变成”hot“了，这时就会丢失异常信息，因为这时的异常是预先分配的。
在查找资料的时候，发现淘宝定制的vm对这个功能有个开关，可以动态切换是否禁用此项优化。