#### 1.什么是GC担保

```
在进行Minor GC (YGC)的之前，虚拟机会去检查老年代的最大连续可用空间是否大于新生代的总空间。
如果大于，则表示这次Minor GC是安全的。
如果小于，则回去检查HandlePromotionFailure是否允许担保失败。
    如果HandlePromotionFailure允许，那么会检查老年代的最大连续可用空间是否大于历次晋升老年代对象的平均大小
        如果对象平均大小比可用空间小，则尝试进行一次Minor GC；Minor GC失败时还是会进行 Full GC
        如果对象平均大小比可用空间大，则进行一次Full GC
    如果HandlePromotionFailure不允许，则进行Full GC
```

#### 2.什么时候会发生YGC

```
新生代空间不足时会发生YGC
```

#### 3.什么时候会发生FGC

```
有5种情况会发生FGC
1.在代码中调用System.gc()
2.年老代可用连续内存空间不足，如分配了一个很大的byte[]
3.永久代内存不足
4.GC担保失败
5.CMS进行GC操作时，如果没有留够足够的内存给应用程序会发生"Concurrent Mode Failure"导致GC收集失败。然后会临时启动另外的收集器进行一次Full GC
```

#### 4.堆内存如何分区

```
堆分为Young Generation(年青代) 和Old Generation(年老代)。方法区中还有一个Permanent Generation(永久代)。
Young Generation还分为Eden区、Survivor区。
Survivor区还分为Survivor1和Survivor2区，Eden:Survivor1:Survivor2 = 8:1:1
Survivor1在GC日志中称为"from"
Survivor2在GC日志中称为"to"
GC发生后，"from"和"to"的名称会发生互换
```

[http://ifeve.com/jvm-yong-generation/](http://ifeve.com/jvm-yong-generation/)

#### 5.发生YGC时，内存中对象是如何变化的；YGC的大概过程

```
1.发生GC时，首先会在Eden区和"from"区根据GC Roots节点开始进行可达性分析，不可达的对象将被标记为死亡对象，可达对象标记为存活对象。
2.存活时间没有达到阈值的对象被移动到"to"区，达到阈值的对象被移动到年老代，死亡对象将被清除。
3."to"区和"from"区名称互换。

当某个对象的年龄到达一定程度（默认15），则会被晋升到年老代。阈值通过-XX:MaxTenuringThreshold设置。
当分配的一个对象过大时，会被直接分配到年老代。对象大小阈值设置-XX:PretenureSizeThreshold。
```

6.为什么要将Survivor空间分成两份

```

```

7.Java7与Java8在JVM上的一些差别

```
1.Java移除了永久代(PermGen)，取而代之的是元空间(metaspace)。两者的本质类似，都是对JVM规范中方法区的实现。
不过元空间与永久代之间最大的区别在于：元空间并不在虚拟机中，而是使用本地内存。
```

8.Java7中，hotspot就已经将String常量池从永久代移动到了堆，static final 的也在堆。证明如下:

```
/**
 * 虚拟机参数 -XX:PermSize=40M -XX:MaxPermSize=40M -XX:MaxHeapSize=1024M -XX:InitialHeapSize=1024M 
 * Java7 用jstat -gcutil 进程号 100 可以看到Eden区和Old区一直在增长，Perm代不动
 * Java6 会出现 java.lang.OutOfMemoryError: PermGen space，jstat看到Perm代飙涨
 */
public class StringHeap {
    public static String base = "String";
    public static void main(String[] args) {
        List<String> list = new ArrayList<String>();
        for (int i = 0; ;i++) {
            String s = base + base;
            base = s;
            list.add(s.intern());
            System.out.println(i);
        }
    }
}
```

9.关于new 对象或者数组的理解

```
在我们new一个对象的时候，无论这个对象处于什么位置(静态块，静态域，常量，方法内)，jvm总是在堆中进行内存分配。相关域保存的仅是一个
引用。
```

10.Full GC比Young GC慢是为什么

```
这是由算法决定的。
YGC使用复制算法，在搜索的过程中即对对象进行了标记-复制动作。
FGC则需要对对象进行标记，然后进行压缩操作，将存活的对象移动到内存一端，再把另一端的对象清除。压缩工作进行时还要暂停虚拟机。所以这是一个
非常耗费时间的工作。
```



