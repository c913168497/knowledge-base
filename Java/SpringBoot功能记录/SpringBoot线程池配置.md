```java
@EnableAsync
@Configuration
public class TaskPoolConfig {

    @Bean("taskExecutor")
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5); // 核心线程数，线程池创建时候初始化的线程数
        executor.setMaxPoolSize(50); // 最大线程数
        executor.setQueueCapacity(10); // 缓冲队列
        executor.setThreadNamePrefix("test-thread-prefix"); //线程名前缀
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy()); // 线程池策略
        return executor;
    }

}

```