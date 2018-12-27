#### **concurrentHashMap的基本结构**

concurrentHashMap的结构是由Node数组组成，Node数组中的每一个Node都是链表的头元素。当Node链表的长度大于阈值的时候，会用红黑树代替链表。

#### **concurrentHashMap如何实现的分段加锁**

首先，concurrentHashMap会使用一个Unsafe对象的本地方法获取到某个槽的Node头元素，同时使用synchronized锁住这个对象。接下来分两步

1.如果这个Node对象为null，即这个槽没有放入任何元素，则使用Unsafe的cas将新建的Node对象放到该槽中，作为链表头元素。

2.如果这个Node对象不为null，则说明产生了hash冲突，将这个Node放到链表尾部。当链表的长度阈值大于时，将链表转换为红黑树。

[http://www.importnew.com/22007.html](http://www.importnew.com/22007.html)

#### **关于concurrentHashMap的size**

1.size\(int类型\)与mappingCount\(long类型\)方法得到的结果基本一致，只是size会限制一个最大值Integer.MAX\_VALUE。官方建议使用mappingCount代替size

2.mappingCount得到的值不是一个准确的数值，而是一个近似值。因为在多线程情况下，如果其他的线程正在插入或者删除元素，这时mappingCount的值会与真实值有所差异。如果需要十分准确的size值，建议使用hashtable或者SynchronizedMap。

3.mappingCount是由baseCount+countCells得到。baseCount会在put之后通过CAS快速计算得到。但是在多线程环境下CAS有可能失败，此时会将值保存在countCells中。countCells的创建是单线程的，通过countBusy控制。

[http://www.jianshu.com/p/e694f1e868ec](http://www.jianshu.com/p/e694f1e868ec)

#### concurrentHashMap比hashtable牛逼的地方

大量使用CAS替换了synchronized。

相比于Hashtable以及Collections.synchronizedMap\(\)，ConcurrentHashMap在线程安全的基础上提供了更好的写并发能力，但同时降低了对读一致性的要求



