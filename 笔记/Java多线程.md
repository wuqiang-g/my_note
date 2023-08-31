## 1.Java线程创建

- 通过实现 Runnable 接口；

- 通过 Callable 和 Future 创建线程；

- 通过继承 Thread 类本身；

    不管是第一种，第二种，还是第三种，本质上都需要创建一个Thread对象，他是java的标准线程对象，因此java线程实例可以认为是一个java对象，但是与普通java对象又有点不同，他具有状态，由jvm管理和维护。

```java
1. // 创建匿名内部类实现Runnable接口，得到一个java线程对象
Thread thread = new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("匿名内部类创建的线程开始执行");
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("匿名内部类创建的线程执行完毕");
    }
});
thread.start();

2.// 创建匿名内部类实现Callable接口,创建FutureTask并执行
FutureTask<Integer> futureTask = new FutureTask<>(new Callable<Integer>() {
    @Override
    public Integer call() throws Exception {
        System.out.println("匿名内部类创建的线程开始执行");
        Thread.sleep(5000);
        System.out.println("匿名内部类创建的线程执行完毕");
        return 42;
    }
});
new Thread(futureTask).start();
try {
    // 获取Callable返回值
    int result = futureTask.get();
    System.out.println("Callable返回的结果是：" + result);
} catch (InterruptedException | ExecutionException e) {
    e.printStackTrace();
}
```



## 2.多线程实战

多线程实战场景禁止手动new Thrad的方式去创建线程，而是使用线程池去管理多线程。

```java
//java原生的线程池执行器
ThreadPoolExecutor
//Spring框架提供的一个线程池执行器(相比原生的更灵活好用)
ThreadPoolTaskExecutor
 
@Configuration
public class ExecutorConfig {
    @Bean(value = "mainExecutor")
    public ThreadPoolTaskExecutor mainExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        //核心线程数一般设置物理机cpu核心数的两倍
        int processors = Runtime.getRuntime().availableProcessors();
        executor.setCorePoolSize(processors * 2);
        executor.setMaxPoolSize(256);
        //同步队列，也就是没队列
        executor.setQueueCapacity(0);
        executor.setKeepAliveSeconds(60);
        executor.setThreadNamePrefix("main-executor");
        //拒绝策略
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        return executor;
    }
}
```



## 3.多线程使用场景与意义

## 4.CompletableFuture方法大全

![CompletableFuture](https://cdn.jsdelivr.net/gh/wuqiang-g/picture@main/img/CompletableFuture.png)

