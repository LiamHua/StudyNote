## 一、String

### 1. String的不可变性

​		String 定义了之后值是不可变的，通过查看String类源码可以发现：1. String类是被final修饰的； 2. 成员字段value也是用final修饰的。我们知道，fianl修饰类代表类不可以被继承，可以防止破坏，而fianl修饰变量代表变量不可以被改变，当然这里fianl修饰的是数组，数组存放在堆中，所以这里是value的引用地址不可变，堆中的value是可以改变的。

![image-20200719153006010](https://pictures.huazai.vip/uPic/image-20200719153006010.png)

​		但是因为字符串常量池的存在，无论我们怎么改变字符串的值，最终改变的都只是引用，并不会真正的改变常量池中的值。无论是new String 还是 直接赋值 String，最终的字符串都是存在于字符串常量池中，不过 new String的方式会在堆中创建一个String对象，该对象指向常量池中的的字符串。

​		无论用什么方式创建字符串，都会先字符串常量池中该字符串是否存在，如果存在就直接指向该字符串，不存在则创建新的字符串。

​		下面我们来操作一波：

![image-20200719163437291](https://pictures.huazai.vip/uPic/image-20200719163437291.png)

​		我们可以看到 s1 == s2， 因为它们都指向了字符串常量池中的同一个hello。而 s3 != S4，因为它们在堆中创建了两个String对象，这两个对象是不等同的，但是这两个对象都指向了常量池中的同一个hello。而 s1 != s3，是因为s1直接指向常量池中的hello，而s3指向堆中的String对象，两者自然就不同了。

![image-20200719165422852](https://pictures.huazai.vip/uPic/image-20200719165422852.png)

​		当然我们可以使用String类的intern() 方法直接来获取字符串常量池的指向，intern方法是一个native方法。如图，此时 s2.intern() 直接指向了常量池中的hello，所以 s1 就等于 s2 了。

![image-20200719165940768](https://pictures.huazai.vip/uPic/image-20200719165940768.png)





## 二、StringBuilder与StringBuffer

### 1. StringBuilder

​		我们知道String类型在改变值的时候并不会改变原有的值，而是会创建一个新值并指向它，那么此时就出现了一个问题，当大量使用String的时候就会浪费空间。于是StringBuilder就出现了，对比String，StringBuilder是在原有的值上进行改动而不是创建新的值。

![image-20200719172701648](https://pictures.huazai.vip/uPic/image-20200719172701648.png)

![image-20200719172811109](https://pictures.huazai.vip/uPic/image-20200719172811109.png)

​		如图是一个循环和它的字节码指令，我们可以看到str在连续改变的时候被JVM自动优化成了StringBuilder以避免字符串常量池产生大量无用的值，但是这段代码会产生大量的StringBuilder对象，经测试在默认情况下堆会溢出，所以我们需要直接使用StringBuilder类，这样就可以只产生一个StringBuilder类。

![image-20200719174339506](https://pictures.huazai.vip/uPic/image-20200719174339506.png)



### 2. StringBuffer

​		StringBuilder是线程不安全的，而StringBuffer是线程安全的，这就是它们的区别。

![image-20200719174900856](https://pictures.huazai.vip/uPic/image-20200719174900856.png)





## 三、String常见面试题补充

1. 

   ```java
   String s1 = "hello2";
   String s2 = "hello" + 2;
   System.out.println(s1 == s2);
   // 结果：true
   ```

   ​		"hello"+2在编译期间就已经被优化成"hello2"，因此在运行期间，变量a和变量b指向的是同一个对象。

2. 

   ```java
   String s1 = "hello2";
   String s2 = "hello";
   String s3 = s2 + 2;
   System.out.println(s1 == s3);
   // 结果：false
   System.out.println(s1 == s3.intern());
   // 结果：true
   ```

   ​		由于有符号引用的存在，所以 String s3 = s2 + 2;不会在编译期间被优化，不会把 s3 + 2 当做字面常量来处理的，因此这种方式生成的对象事实上是保存在堆上的。因此 s1 和 s3 指向的并不是同一个对象。

3. 

   ```java
   String s1 = "hello2";
   final String s2 = "hello";
   String s3 = s2 + 2;
   System.out.println(s1 == s3);
   // 结果：true
   ```
   
   ​		对于被final修饰的变量，会在class文件常量池中保存一个副本，也就是说不会通过连接而进行访问，对final变量的访问在编译期间都会直接被替代为真实的值。

4. 

   ```java
   public static void main(String[] args) {
       String s1 = "hello2";
       final String s2 = getHello();
       String s3 = s2 + 2;
       System.out.println(s1 == s3);
       // 结果：false
   }
   
   private static String getHello() {
       return "hello";
   }
   ```
```

   ​		这里面虽然将b用final修饰了，但是由于其赋值是通过方法调用返回的，那么它的值只能在运行期间确定，因此 s1 和 s3 指向的不是同一个对象。

   

5. String str = new String("abc")创建了多少个对象？

   ​		这个问题很多说法答案都是创建 2 个对象，其实不然，这段代码在运行时只创建了 1 个对象，就是在堆上的 new String()。那为什么很多答案都说 2 个对象呢，问题的关键在于创建时间，这段代码在运行时只在堆上创建了一个String对象，然后指向常量池中的 abc对象，那么常量池中的对象哪来的呢？答案是在类加载过程中创建的。因此，这个问题换成 String str = new String("abc")涉及到几个String对象？合理的解释是2个。

   

6. 下面这段代码第 2 行和第 3 行的区别是什么？

   ```java
   String s1 = "hello";
   s1 += "，" + "world";
   s1 = s1 + "，" + "world";
```

   ​		在 jdk 1.7 中 2 比 3 效率更高，2 执行了 2 次 append 而 3 执行了 3 次 append，但是在 jdk 1.8 中这个情况已经做了优化，两种情况都是执行 2 次 append。



参考文章：https://www.cnblogs.com/dolphin0520/p/3778589.html

