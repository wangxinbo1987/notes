### 关于Java多线程

#### 进程 & 线程

计算机中的一个任务称为一个**进程**，进程内部的子任务称为**线程**

操作系统调度的最小任务单位是线程。Windows、Linux等操作系统都采用抢占式多任务，如何调度线程完全由操作系统决定，程序自己不能决定什么时候执行，以及执行多长时间

多任务既可以由**多进程**实现，也可以由单进程内的**多线程**实现，还可以**混合多进程＋多线程**

多线程对比多进程，优点在于

- 创建线程开销小，尤其是在Windows系统上
- 线程间通信速度更快，因为可以共享变量

也有一些缺点，比如

- 稳定性较低，任何一个线程崩溃会直接导致整个进程崩溃

---

#### Java多线程模型

Java线程内存模型中，可以将虚拟机内存划分为两部分内存：主内存和线程工作内存，主内存是多个线程共享的内存，线程工作内存是每个线程独享的内存

![假装这里有一张图片](/static/img/thread-model.png)

在上图中，方法区和堆内存就是**主内存区域**，而虚拟机栈、本地方法栈以及程序计数器则属于每个**线程独享的工作内存**

Java内存模型规定所有成员变量都需要存储在主内存中，线程会在其工作内存中保存需要使用的成员变量的拷贝，线程对成员变量的操作（读取和赋值等）都是对其工作内存中的拷贝进行操作

各个线程之间不能互相访问工作内存，线程间变量的传递需要通过主内存来完成

#### Java线程生命周期

`Thread.State`枚举类中定义了六种线程状态，分别是`NEW`，`RUNNALBE`，`BLOCKED`，`WAITING`，`TIMED_WAITING`，`TERMINATED`，其生命周期流程如下

![假装这里有一张图片](/static/img/thread-states.jpg)

---

#### 线程创建方法

方法一：从`Thread`派生一个自定义类，然后覆写`run()`方法

方法二：定义一个类，实现`Runnable`接口的`run()`方法；创建`Thread`实例时，将其传入

#### 线程启动方法

调用`Thread.start()`方法，其内部是调用了`private native void start0()`方法，该`start0()`方法是由JVM虚拟机内部的C代码实现的，不是由Java代码实现的

#### 线程设定优先级

调用`Thread.setPriority(int n)`设定优先级，可选范围1-10，默认是5。优先级高的线程被操作系统调度的优先级较高，但具体调度顺序依然由操作系统决定，我们**不能**通过设置优先级来保证某个线程一定会被先执行

#### 线程等待

`Thread.sleep(long millis)`使当前线程等待millis毫秒，等待期间线程处于`TIMED_WAITING`状态

`t.join()`使当前线程等待目标线程`t`结束后继续运行；也可以传入一个long类型的参数，`t.join(long millis)`表示等待至多millis毫秒后继续运行，超过该时间若目标线程仍未结束，则不再等待；对已经运行结束的线程调用`join()`方法会立刻返回

#### 线程中断

调用目标线程的`interrupt()`方法可以向其发送**中断请求**

- 如果目标线程处在等待状态，会抛出`InterruptedException`，需对其捕获处理
- 如果目标线程处在运行状态，代码可以通过`isInterrupted()`方法判断状态，作相应逻辑处理

或者改变自定义标志位的值，在代码中通过监测标志位的值来判断是否有中断

#### 守护线程

Java程序入口是由JVM启动main线程，main线程又可以启动其他线程。当所有线程都运行结束时，JVM退出，进程结束。可以通过调用`t.setDaemon(true)`将目标线程设置为守护线程`Daemon Thread`，JVM退出时不会考虑守护线程是否结束，即守护线程可以在JVM退出后继续存在

`t.setDaemon()`方法一定要在`t.start()`之前调用，一旦启动后，无法再更改为守护线程

> 需要注意的是，守护线程不能持有任何需要关闭的资源，例如打开文件等，因为JVM退出时，守护线程没有任何机会来关闭文件，这会导致数据丢失

---

#### 示例

```java
public class SimpleThread extends Thread {

    @Override
    public void run() {
        System.out.println("Thread " + this.getName() + " is running ...");

        int count = 0;
        int interval = 1000;

        while (!isInterrupted()) {
            try {
                Thread.sleep(interval);
                System.out.println("Thread " + this.getName() + " waited for " + ++count + " seconds");
            } catch (InterruptedException e) {
                System.out.println("Thread " + this.getName() + " interrupted");
            }
        }

        System.out.println("Thread " + this.getName() + " finished");
    }
}

public class SimpleRunnable implements Runnable {

    public volatile boolean running = true;

    @Override
    public void run() {
        System.out.println("Thread " + this.toString() + " is running ...");

        int count = 0;
        int interval = 1000;

        while (running) {
            try {
                Thread.sleep(interval);
                System.out.println("Thread " + this.toString() + " waited for " + ++count + " seconds");
            } catch (InterruptedException e) {
                System.out.println("Thread " + this.toString() + " interrupted");
            }
        }

        System.out.println("Thread " + this.toString() + " finished");
    }
}

public class ThreadLab {

    private void simpleStartThread() {
        /**
         * Option 1 : Define a thread class by extending the Thread class
         */
        Thread t1 = new SimpleThread();

        /**
         * Option 2 : Define a thread class by implementing Runnable interface and pass it to Thread class as construction parameter
         */
        SimpleRunnable simpleRunnable = new SimpleRunnable();
        Thread t2 = new Thread(simpleRunnable);

        /**
         * Start threads
         */
        t1.start();
        t2.start();

        try {
            Thread.sleep(1000);

            /**
             * Interrupt thread 1 by invoking interrupt()
             */
            t1.interrupt();
            t1.join();

            /**
             * Interrupt thread 2 by setting running flag to false
             */
            simpleRunnable.running = false;
            t2.join();
        } catch (InterruptedException e) {
            System.out.println("Sub thread interrupted");
        }

        /**
         * this is a daemon thread, which will not be terminated when JVM quit
         */
        Thread t3 = new SimpleThread();
        t3.setDaemon(true);
        t3.start();

        System.out.println("Main thread finished");
    }

    public static void main(String[] args) {
        ThreadLab threadLab = new ThreadLab();
        threadLab.simpleStartThread();
    }
}
```

