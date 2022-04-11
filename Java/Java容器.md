# Java容器

[Java容器源码分析](./Java容器源码分析.md)



- [Java容器](#java--)
  * [基本框架](#----)
  * [Collection-List接口](#collection-list--)
    + [ArrayList](#arraylist)
    + [LinkedList](#linkedlist)
    + [Vector](#vector)
  * [Collection-Queue接口](#collection-queue--)
    + [PriorityQueue](#priorityqueue)
    + [ArrayDeque](#arraydeque)
  * [Collection-Set接口](#collection-set--)
    + [HashSet](#hashset)
    + [TreeSet](#treeset)
    + [LinkedHashSet](#linkedhashset)
  * [Map接口](#map--)
    + [HashMap](#hashmap)
    + [TreeMap](#treemap)
    + [HashTable](#hashtable)
    + [LinkedHashMap](#LinkedHashMap)
  * [fail-fast](#fail-fast)


## 基本框架

> Collection：存放单一元素，存储一组不唯一、无序的对象

![image-20220209120812825](Java容器.assets/image-20220209120812825.png)

> Map：存放键值对

![image-20220209124955382](Java容器.assets/image-20220209124955382.png)

上面列举了常用的接口以及实现类，详细框架参考[Java 集合框架](https://www.runoob.com/java/java-collections.html)

Collection：接口中定义了大部分的集合操作方法；

- List：存储的元素有序且可重复；
- Queue：类似List，拥有队列(FIFO)的性质；
  - Deque：双端队列，队首队尾均可以插入删除元素；
- Set：存储的元素无序且唯一；
  - SortedSet：实现元素有序存储。

Map：存储的元素是key/value键值对，使用key进行索引；key无序且唯一，value无序且可重复。

## Collection-List接口

### ArrayList

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

ArrayList：基于动态数组Object[] (容量可变)实现，支持随机访问(实现了RandomAccess接口)；查找效率高，线程不安全。

### LinkedList

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
```

LinkedList：基于双向链表 (JDK1.6之前为双向循环链表) 实现，只能顺序访问元素；插入删除元素的效率高，线程不安全。

### Vector

```java
public class Vector<E>
    extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

Vector：类似ArrayList，但是线程安全的。(方法使用了synchronized关键字)，如：

```java
public synchronized int size() {
    return elementCount;
}
```

## Collection-Queue接口

### PriorityQueue

```c++
//An unbounded priority based on a priority heap......
//Priority queue represented as a balanced binary heap......
public class PriorityQueue<E> extends AbstractQueue<E>
    implements java.io.Serializable {
```

PriorityQueue：优先队列是基于堆实现的，并且是一个平衡二叉堆，底层仍使用Object[] 数组存储元素。线程不安全。

### ArrayDeque

```java
public class ArrayDeque<E> extends AbstractCollection<E>
                           implements Deque<E>, Cloneable, Serializable
```

ArrayDeque：使用 Object[]数组 以及 指向队列头尾的双指针 实现，允许在队列的头部和尾部进行插入删除操作。线程不安全。

## Collection-Set接口

### HashSet

```c++
/*This class implements the Set interface, backed by a hash table(actually a HashMap instance)*/
public class HashSet<E>
    extends AbstractSet<E>
    implements Set<E>, Cloneable, java.io.Serializable
```

HashSet：存储的元素无序且唯一，底层基于HashMap实现，支持快速查找(O(1))。线程不安全。

### TreeSet

```c++
public class TreeSet<E> extends AbstractSet<E>
    implements NavigableSet<E>, Cloneable, java.io.Serializable
```

TreeSet：存储的元素有序且唯一，底层基于红黑树(自平衡的二叉排序树)实现，查找效率(O(logN))不如HashSet。线程不安全。

### LinkedHashSet

```java
/*This implementation differs from HashSet in that it maintains a doubly-linked list running through all of its entries.*/
public class LinkedHashSet<E>
    extends HashSet<E>
    implements Set<E>, Cloneable, java.io.Serializable {
```

LinkedHashSet：继承HashSet，查找效率和HashSet相同，但内部使用双向链表来维护元素的插入顺序。线程不安全。

## Map接口

### HashMap

```java
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable 
```

HashMap：基于哈希表实现，线程不安全。(`key`唯一且可以为null，`value`可以有多个为null)

HashMap 的初始长度为 16，之后每次扩充变为原来的两倍。

### TreeMap

```java
public class TreeMap<K,V>
    extends AbstractMap<K,V>
    implements NavigableMap<K,V>, Cloneable, java.io.Serializable
```

TreeMap：基于红黑树实现，线程不安全。

### Hashtable

```java
public class Hashtable<K,V>
    extends Dictionary<K,V>
    implements Map<K,V>, Cloneable, java.io.Serializable
```

Hashtable：与HashMap类似，但线程安全。不过它是遗留类，目前使用ConcurrentHashMap来支持线程安全。（`Hashtable`中的`key`和`value`都不能为null）

Hashtable 的初始长度是 11，之后每次扩充容量变为之前的 2n+1（n 为上一次的长度）

### LinkedHashMap

```java
public class LinkedHashMap<K,V>
    extends HashMap<K,V>
    implements Map<K,V>
```

LinkedHashMap：继承HashMap，使用双向链表维护元素插入的顺序。



## fail-fast

fail-fast 机制是 Java 集合（Collection）中的一种错误机制。当多个线程对同一个集合的内容进行操作时，就可能会产生 fail-fast 事件。 

例如：当某一个线程 A 通过 iterator 去遍历某集合的过程中，若该集合的内容被其他线程所改变了，那么线程 A 访问集合时，就会抛出 ConcurrentModificationException 异常，产生 fail-fast 事 件。这里的操作主要是指 add、remove 和 clear，对集合元素个数进行修改。

 解决办法：建议使用“java.util.concurrent 包下的类”去取代“java.util 包下的类”。 可以这么理解：在遍历之前，把 modCount 记下来 expectModCount，后面 expectModCount 去 和 modCount 进行比较，如果不相等了，证明已并发了，被修改了，于是抛出 ConcurrentModificationException 异常。



[Java常用集合&方法](../数据结构与算法/java常用容器&方法.md)

