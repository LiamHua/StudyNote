### 1. 硬件内存模型

​		在学习Java内存模型之前，先了解一下计算机硬件内存模型。我们多知道处理器与计算机存储设备运算速度有几个数量级的差别。总不能让处理器总是等待计算机存储设备，这样就没办法显现出处理器的优势。因此，为了“压榨”处理的性能，达到“高并发”的效果，在处理器和存储设备之间加入了高速缓存（cache）来作为缓冲。

![img](https://pictures.huazai.fun/uPic/computer.jpg)

​		一般CPU都会从内存取数据到寄存器，然后进行处理，但由于内存的处理速度远远低于CPU，导致CPU在处理指令时往往花费很多时间在等待内存做准备工作，于是在寄存器和主内存间添加了CPU缓存，CPU缓存比较小，但访问速度比主内存快得多，如果CPU总是操作主内存中的同一址地的数据，很容易影响CPU执行速度，此时CPU缓存就可以把从内存提取的数据暂时保存起来，如果寄存器要取内存中同一位置的数据，直接从缓存中提取，无需直接从主内存取。需要注意的是，寄存器并不每次数据都可以从缓存中取得数据，万一不是同一个内存地址中的数据，那寄存器还必须直接绕过缓存从内存中取数据。所以并不每次都得到缓存中取数据，这种现象有个专业的名称叫做缓存的命中率，从缓存中取就命中，不从缓存中取从内存中取，就没命中，可见缓存命中率的高低也会影响CPU执行性能，这就是CPU、缓存以及主内存间的简要交互过程，总而言之当一个CPU需要访问主存时，会先读取一部分主存数据到CPU缓存(当然如果CPU缓存中存在需要的数据就会直接从缓存获取)，进而在读取CPU缓存到寄存器，当CPU需要写数据到主存时，同样会先刷新寄存器中的数据到CPU缓存，然后再把数据刷新到主内存中。



### 2. Java内存模型

​		Java内存模型即Java Memory Model，简称JMM。用来屏蔽掉各种硬件和操作系统的内存访问差异，以实现让Java程序在各平台下都能够达到一致的内存访问效果。

​		JMM定义了线程和主内存之间的抽象关系：线程之间的共享变量存储在主内存（main memory）中，==每个线程都有一个私有的本地内存（local memory），本地内存中存储了该线程以读/写共享变量的副本==。本地内存是JMM的一个抽象概念，并不真实存在。它涵盖了缓存，写缓冲区，寄存器以及其他的硬件和编译器优化。

![img](https://pictures.huazai.fun/uPic/jmm.jpg)



### 3. 论证

​		从Java内存模型中我们知道，Java线程在使用共享变量的时候都是先copy一个副本到工作内存	，然后对副本进行修改，操作完成后再写回主内存。下面我们以一个实际例子来论证：

```java
public class Demo10 {
    static boolean running = true;

    public static void main(String[] args) {
        Thread threadA = new Thread(() -> {
            System.out.println("A start...");
            while (running) {

            }
            System.out.println("A over...");
        });

        Thread threadB = new Thread(() -> {
            System.out.println("B start...");
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            running = false;
            System.out.println("B over...");
        });

        threadA.start();
        threadB.start();
    }
}
```

![image-20200921102210222](https://pictures.huazai.fun/uPic/image-20200921102210222.png)

​		从上面例子中我们可以看出，线程B将running修改为false后，线程A依旧处于死循环未能停止，说明线程A中running依旧是ture，这就论证了上面的Java内存模型，线程使用的是从共享变量拷贝的副本。



### 4. 解决

​	解决的方法就是使用volatile关键字，volatile保证变量的可见性，简单的理解就是，volatile变量在每次被线程访问时，都强迫从主内存中读该变量的值，而当该变量发生变化时，又会强迫将最新的值刷新到主内存，任何时刻，不同的线程总是能够看到该变量的最新值。

```java
public class Demo10 {
    /**
     * volatile can guarantee memory visibility
     */
    static volatile boolean running = true;

    public static void main(String[] args) {
        Thread threadA = new Thread(() -> {
            System.out.println("A start...");
            while (running) {

            }
            System.out.println("A over...");
        });

        Thread threadB = new Thread(() -> {
            System.out.println("B start...");
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            running = false;
            System.out.println("B over...");
        });

        threadA.start();
        threadB.start();
    }
}
```

![image-20200921103103661](https://pictures.huazai.fun/uPic/image-20200921103103661.png)

​		我们可以看到A、B两个线程都运行结束了，说明A线程读取到了B线程改变后的running变量，这就是volatile关键字的作用。



### 5. 原理

​		volatile能够保证变量的可见性，那么原理是什么呢？其实其底层实现主要是通过汇编lock前缀指令，它会锁定这块内存区域的缓存（缓存行锁定）并写回道主内存。对lock指令的解释主要有以下两条：

+ 会将当前处理器缓存行的数据立即写回道系统内存
+ 这个写回内存的操作会引起其它CPU里缓存了该内存地址的数据无效（MESI缓存一致性协议）

