1.HashTable和HashMap的区别

```
HashTable线程安全，HashMap线程不安全
HashTable不允许null作为key，HashMap允许null作为key

concurrentHashMap读操作是非阻塞的，不会加锁
HashTable读操作是阻塞的，会加锁
```

2.Error、Exception和RuntimeException的区别，作用又是什么？列举3个以上的RuntimeException。

```
 Error类体系描述了Java运行系统中的内部错误以及资源耗尽的情形。
 应用程序不应该抛出这种类型的对象（一般是由虚拟机抛出）。如果出现这种错误，除了尽力使程序安全退出外，在其他方面是无能为力的。

 Exception体系包括RuntimeException体系和其他非RuntimeException的体系

 RuntimeException体系包括错误的类型转换、数组越界访问和试图访问空指针等等。
 处理RuntimeException的原则是：如果出现RuntimeException，那么一定是程序员的错误。
```

```
常见RuntimeException
IndexOutOfBoundsException
NullPointerException
DateTimeParseException
ClassCastException
ConcurrentModifyException
NumberFormatException
```

```
常见Error
OutOfMemoryError
NoSuchMethodError 多数由于包版本依赖问题导致
```

```
常见其他异常
IOException
TimeoutException
```

3.switch可用于哪些数据类型

```
char,byte,short,int,及其对应的包装类
String
enum
```



