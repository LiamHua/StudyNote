## 一、HashMap

​		HashMap 是一个非常重要的类，在面试中百问不爽，下面我们就来捋一捋关于HashMap的知识点，以下讲述主要基于Java8。



### 1. 底层结构

​		在 Java7 中，HashMap 的底层结构是数组 + 链表，但是在Java8 后，这个结构被稍微优化了一些，变成了 数 + 链表/红黑树。如图，HashMap最基本的就是 数组结构，根据索引来查找元素，所以 HashMap 查找的时间复杂度为 O(1)，但这个时间复杂度也不是完全精准的，只有在只是一个数组的情况下才是 O(1)。但是HashMap 还有一个结构就是链表。

​		那么这个链表是怎么形成的呢？答案就是哈希碰撞，HashMap 会根据键值对的 key 来计算出索引，然后放入数组中对应的位置，也就是说它并不是按顺序放入的。那么问题就来了，从 key 到 索引，这个过程并不是独一无二的，不同的 key 可能会计算出相同的索引，这种情况就叫做哈希碰撞，那么当两个键值对计算出相同的索引该怎么办呢？这时候链表就登场了，计算出相同索引的键值对会形成一个链表结构，这就解决了哈希碰撞的问题。

​		但是解决问题的同时又引入了一个新的问题，那就是时间复杂度被改变了。单纯的数组可以计算索引然后直接取值，所以是 O(1)，但是这里多出了一个链表。此时HashMap 的做法是先计算索引，找到对应的哈希桶，也就是链表，然后会遍历链表上的元素，将参数 key 与 链表上每个节点的 key 依次比较，最终得到对应的值，这个过程的时间复杂度为 O(n)，n 为链表的长度。也就是说，最终的时间复杂度是在 O(1) 的基础上加上 O(n)，n 是个不确定的值，取决于哈希碰撞的次数，即一旦发生了哈希碰撞，平均时间复杂度肯定是比 O(1) 要大的。

​		那么新的问题又产生了，一旦发生大量的哈希碰撞，导致链表长度过长，此时查找的时间复杂度就会大大增加，严重影响了性能。为了解决这个问题，Java8 引入了红黑树，红黑树是一颗自平衡的二叉查找树，其查找的时间复杂度为 O(logn)，这里不做关于红黑树的过多概述。从 n 到 logn ，降低了多少查找时间就不用多说了吧，这就是为什么HashMap要引入红黑树。

![image-20200720094531468](https://pictures.huazai.vip/uPic/image-20200720094531468.png) 



### 2. 细节面试问题

1. ##### HashMap 的初始参数

```java
// 默认初始容量为 2的4次方，即16
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;
// 最大容量为 2的30次方
static final int MAXIMUM_CAPACITY = 1 << 30;
// 负载因子为 0.75
static final float DEFAULT_LOAD_FACTOR = 0.75f;
// 从链表变为红黑树的阀值为 8
static final int TREEIFY_THRESHOLD = 8;
// 从红黑树退化为链表的阀值
static final int UNTREEIFY_THRESHOLD = 6;
// 当容量到达 64 后才会进行树化，否则会以扩容来缩短链表的长度
static final int MIN_TREEIFY_CAPACITY = 64;
```

​			HashMap的默认初始容量为16，负载因子为 0.75 ，这个在new HashMap 的时候是可以指定的，这两个默认参数是经过权衡考虑的。当 HashMap 的实际长度大于 初始容量 * 负载因子时，就会触发 HashMap 的扩容机制，这个扩容是非常耗时耗空间的操作，因此我们一般避免它的扩容，在平常情况下容量 16 是足够的，一般不会出发扩容，但在实际生产中，我们一般会根据实际情况指定容量 来避免发生扩容。负载因子 0.75  似乎一般不轻易改动，设置太小了会浪费空间，太大了可能影响时间（hash冲突变多）。

​			TREEIFY_THRESHOLD 是从链表结构转变为红黑树结构的阀值，一旦某个哈希桶中的链表节点个数大于 8 时，该链表就会重构为一颗红黑树（前提条件是容量大于等于64，否则会扩容而不是树化）。8 这个数字来源于柏松分布，这是一个概率学问题，因为树节点占用空间约为普通节点的两倍，所以我们不能一开始就直接使用红黑树结构，这样会浪费空间，最终用 8 作为阀值是权衡时间和空间的结果，在源码中有关于柏松分布的详细概率，一个链表长度达到 9 的概率小于千万分之一，几乎是不可能事件，所以我们平常在使用时基本不会有红黑树的转变。UNTREEIFY_THRESHOLD 是从红黑树退化为链表的阀值，HashMap中的元素是可以移除的，当红黑树的节点个数小于 6 时，树就会退化为链表以节省空间。



2. ##### 关于索引的计算

```java
# 以下代码摘自 HashMap 源码（jdk1.8）
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}

hash = hash(key);
n = (tab = resize()).length;
index = (n - 1) & hash;
```

​		从源码中我们可以看到，在第4行首先是通过 key 计算 hashCode 并且进行异或远算、移位运算，这里的hash算法（混合了高位和低位）主要是为了保证最终 hash 值的散列性，以保证在向 HashMap 中添加数据时尽可能均匀分布。我们继续往下看，tab 的意思就是数组，n 的值为数组的长度，也就是 HashMap 的容量，最后一步计算就是 n -1 & hash，将 n - 1 与 hash 进行与运算得到最后的索引，这是为什么呢？ 这里就牵扯到一个必问面试题，为什么 HashMap 是按 2次幂扩容？别的数值不行吗？这一步运算就是关键。我们想想，我们在向 HashMap 中添加数据时，索引是计算出来的，那么我们怎么保证计算出来的索引在当前数组的范围内呢？关键就在这里。首先是 & 运算（与运算），这是二进制层面的运算，1&1=1 , ==1&0=0 , 0&0=0==，0 & 任何一个数都是 0，也就是说，与运算的结果的最大值取决于两个数中哪个数的位数更短。例如，当 n 为16 时，n - 1 转换成二进制( 1111)则位数为 4，那么进行与运算最终的结果不可能大于 15，这样就保证了计算出的索引一定在当前数组范围内。那么为什么是 n - 1 而不是 n - 2 等等 呢？这里就涉及到刚才的面试题了。n 始终是 2 的幂次方，那么 n - 1 转换成二进制表示就始终是 1111......了，那么在与 hash进行与运算的时候就能完美的保证没有引入外来变量影响 hash 的散列性，因为 1 & 任何数，都不会产生干扰，这就是为啥是 n - 1 而不是可能是其它数 的原因了。 总结来说，n - 1 在这里有两个作用，一是保证索引在范围内（还必须是与运算），二是保证不影响 hash 值的散列性。



3. ##### 扩容机制

   我们首先来看看什么时候会扩容，如图，从源码中很容易就得知当元素个数大于当前容量 * 负载因子时就会扩容，也就是说默认情况下第一次扩容发生在插入第 13 个元素后，第二次扩容发生在插入第 25 个元素后。

```java
// threshold = 当前容量 * 负载因子 
if (++size > threshold)
    resize();
```

​			扩容机制在Java7 与 Java8 之间发生了变化，Java7 使用了头插法，而Java8 使用了尾插法，我们先来看看 Java8 的部分源码：

```java
// 如果数组不为空
if (oldTab != null) {
    // 遍历数组 
    for (int j = 0; j < oldCap; ++j) {
        Node<K,V> e;
        // 如果哈希桶不为空
        if ((e = oldTab[j]) != null) {
            oldTab[j] = null;
            // 如果哈希桶中只有一个元素，就直接在新的数组中创建该元素
            if (e.next == null)
              	newTab[e.hash & (newCap - 1)] = e;
            // 如果哈希桶中是树结构
            else if (e instanceof TreeNode)
              	((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
            // 如果哈希桶中是链表结构
            else { // preserve order
                // 扩容后将原本的位置对应新数组区域作为低位节点区，新的位置作为高位节点区
                // 扩容后会将原本低位节点区链表分裂，一部分分裂到高位节点区
                // 低位节点
              	Node<K,V> loHead = null, loTail = null;
                // 高位节点
              	Node<K,V> hiHead = null, hiTail = null;
              	Node<K,V> next;
                // 开始循环分裂
              	do {
                  // 保存下一个节点
                	next = e.next;
                  // hash 值具有散列性，所以链表的分裂是比较均匀的
                  // oldCap 转为二进制后进行与运算不会影响 hash 的散列性
                  // 分裂出低位节点链表
                	if ((e.hash & oldCap) == 0) {
                  	if (loTail == null)
                    	loHead = e;
                  	else
                    	loTail.next = e;
                  	loTail = e;
                	}
                  // 分裂出高位节点链表
                	else {
                  	if (hiTail == null)
                   	 hiHead = e;
                  	else
                   	 hiTail.next = e;
                  	hiTail = e;
                	}
              } while ((e = next) != null);
              // 分裂出的低位节点区链表不为空就将其赋值到新数组上
              if (loTail != null) {
                	loTail.next = null;
               	 	newTab[j] = loHead;
              }
              // 分裂出的高位节点区链表不为空就将其赋值到新数组上
              if (hiTail != null) {
                	hiTail.next = null;
                	newTab[j + oldCap] = hiHead;
              	}
            }
        }
    }
}
```

​			从上面我们可以看出来Java8 的扩容代码还是很好理解的，也没发现任何毛病。但是Java7 的扩容代码却是有相对上的瑕疵，在多线程情况下可能会发生死锁，但是设计者也并没有将HashMap设计为多线程可用，多线程使用ConcurrentHashMap就好。具体死锁分析请参考：https://juejin.im/post/5a66a08d5188253dc3321da0

```java
// 将旧数组中所有元素转移到新数组
void transfer(Entry<?,?>[] newTable, boolean rehash) {
    // 旧数组
    Entry<?,?>[] src = table;
    int newCapacity = newTable.length;
    // 开始遍历
    for (int j = 0; j < src.length; j++) {
        Entry<K,V> e = (Entry<K,V>)src[j];
        while(null != e) {
            // 保存后一个元素
          	Entry<K,V> next = e.next;
          	if (rehash) {
            		e.hash = null == e.key ? 0 : hash(e.key);
          	}
            // 计算在新数组中的索引
          	int i = indexFor(e.hash, newCapacity);
            // 头插法衔接
          	e.next = (Entry<K,V>)newTable[i];
            // 插入
          	newTable[i] = e;
            // 将 e 指针再次指向旧数组，准备新一轮循环
          	e = next;
        }
    }
}
```



4. ##### HashMap 线程不安全

​		我们通常听说 HashMap是线程不安全的，现在我们从源码来分析一下为什么不安全。首先看看 put 方法的部分源码：

```java
// 某个哈希桶中没有元素，创建第一个节点
if ((p = tab[i = (n - 1) & hash]) == null)
    tab[i] = newNode(hash, key, value, null);
```

```java
// 当某个哈希桶中存在元素时，继续向其中添加元素
// 开始遍历该哈希桶中的链表
// p 是该链表的第一个节点
for (int binCount = 0; ; ++binCount) {
    // 遍历到链表尾部，开始创建新节点
  	if ((e = p.next) == null) {
        // 在链表尾部创建新的节点
    		p.next = newNode(hash, key, value, null);
        // 判断是否要树化
    		if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
      			treeifyBin(tab, hash);
    		break;
  	}
    // 如果 key 相同则替换该节点
  	if (e.hash == hash &&
      	((k = e.key) == key || (key != null && key.equals(k))))
    		break;
  	p = e;
}
```

​		第二段源码中我们重点看第 8 行，在多线程下这里就可能出现问题。例如在某个时刻线程 1 和线程 2 都各自添加一个新节点到同一个链表上，两个线程执行完第 6 行后时间片用尽，==注意此时 两个线程的 p节点都是该链表的第一个节点==，过段时间后，线程 1 继续执行，在 p 节点后创建一个新节点 m，然后线程 2 也开始了，线程 2 也在 p 节点后创建一个新节点 n，这就出大问题了啊，新来的 n 节点把 m 节点给顶没了，也就是部分数据消失了，这就是 HashMap 在多线程下 put 操作的不安全性。同理，第一段源码中也是这种情况，如果两个线程都通过了判断就也会出现数据丢失的情况。

​		还有 get 操作也可能出现问题，当一个线程 put ，另一个线程 get 时，就有可能出现 get 为 null 的情况。这是因为 put 操作触发了 resize，扩容的时候会创建新的节点数组然后将其赋值给 table 变量，这时另一个线程就 get 不到 table中的值了。

```java
// resize()
Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
table = newTab;

// get()
tab = table
```



参考文章：https://juejin.im/post/5cb163bee51d456e46603dfe

参考文章：https://juejin.im/post/5c8910286fb9a049ad77e9a3

参考文章：https://juejin.im/post/5a66a08d5188253dc3321da0

