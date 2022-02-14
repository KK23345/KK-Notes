# Java常用容器&方法

- [Java常用容器&方法](#java常用容器&方法)
  * [Collection接口](#collection接口)
    + [List接口](#list接口)
      - [ArrayList](#arraylist)
      - [LinkedList](#linkedlist)
    + [Queue接口](#queue接口)
      - [PriorityQueue](#priorityqueue)
      - [ArrayDeque](#arraydeque)
    + [Set接口](#set接口)
      - [HashSet](#hashset)
      - [TreeSet](#treeset)
  * [Map接口](#map接口)
    + [HashMap](#hashmap)
    + [TreeMap](#treemap)

## Collection接口

List、Set和Queue接口均继承了Collection，并且扩展了自己所必须的方法，下面常用的方法都可以被它们调用：

```java
int size();                 //返回集合中元素个数
boolean isEmpty();          //集合为空 ？true : false
Object[] toArray();         //将集合转换为Object数组
void clear();               //清空集合
boolean contains(Object o); //包含对象o ? true : false
boolean containsAll(Collection<?> c);
```

### List接口

<tt>ArrayList</tt>和<tt>LinkedList</tt>相同的方法 (名字相同，底层实现有区别)

```java
//添加
boolean add(E e);               //集合末尾添加元素
void add(int index, E element); //指定索引处添加元素
boolean addAll(Collection<? extends E> c);
boolean addAll(int index, Collection<? extends E> c);

//删除 index处或第一个值为o 的元素
E remove(int index);
boolean remove(Object o);

//修改 index处值为element
E set(int index, E element);

//查找
E get(int index); //返回索引从的元素值
	//返回o第一次出现的下标，不存在返回-1
int indexOf(Object o);
	//返回o最后一次出现的下标，不存在返回-1
int lastIndexOf(Object o);
```

#### ArrayList

```java
//删除 
	//从该列表中删除指定集合中包含的所有元素。
boolean removeAll(Collection<?> c);
	//从该列表中删除指定集合中不包含的所有元素。
boolean retainAll(Collection<?> c);

//修改
	//返回区间[fromIndex, toIndex)内的元素组成的集合
List<E> subList(int fromIndex, int toIndex);
	//排序
void sort(Comparator<? super E> c);
```

#### LinkedList

下面的方法都是实现<tt>Deque</tt>接口中的方法，可作为队列或栈使用。

| <tt>Deque</tt>接口 | 抛出异常                                 | 返回false/null                                   |
| ------------------ | ---------------------------------------- | ------------------------------------------------ |
| 队首添加元素       | void addFirst(E e);<br />void push(E e); | boolean offerFirst(E e);                         |
| 队尾添加元素       | void addLast(E e);                       | boolean offer(E e);<br />boolean offerLast(E e); |
| 删除队首元素       | E removeFirst();<br />E pop();           | E poll();<br />E pollFirst();                    |
| 删除队尾元素       | E removeLast();                          | E pollLast();                                    |
| 返回队首元素       | E getFirst();                            | E peek();<br />E peekFirst();                    |
| 返回队尾元素       | E getLast();                             | E peekLast();                                    |

### Queue接口

若因容量问题导致操作失败，处理方式可分为两种：抛出异常和返回特殊值(false/null)，相应的方法也可分为两类：

| <tt>Queue</tt>接口 | 抛出异常          | 返回false/null      |
| ------------------ | ----------------- | ------------------- |
| 队尾添加元素       | boolean add(E e); | boolean offer(E e); |
| 删除队首元素       | E remove();       | E poll();           |
| 返回队首元素       | E element();      | E peek();           |

#### PriorityQueue

```java

```

#### ArrayDeque

<tt>ArrayDeque</tt>和<tt>LinkedList</tt>都实现了<tt>Deque</tt>接口，方法类似，只是底层实现不同，都都可以用来模拟栈和队列。方法同[LinkedList](#LinkedList)

### Set接口

#### HashSet

```java
//向哈希表中添加元素
boolean add(E e);  
//删除
boolean remove(Object o);
//查找
boolean contains(Object o);
```

#### TreeSet

```java
//返回一个按照递减顺序遍历元素的迭代器
Iterator<E> descendingIterator();

//添加
boolean add(E e);
boolean addAll(Collection<? extends E> c);

//删除
	//返回并删除表中的最大或最小元素
E pollFirst();                  E pollFirst();

//查找
boolean contains(Object o);
	//返回表中第一个或最后一个元素
E first();                      E Last();
	//返回>=e的最小元素    /    <=e的最大元素，没有则返回null
E ceiling(E e);                 E floor(E e);
	//返回>e的最小元素    /     <e的最大元素，没有则返回null
E higher(E e);                  E lower(E e);

//返回指定范围内的元素组成的新哈希表
NavigableSet<E> subSet(E fromElement, boolean fromInclusive,
                                  E toElement,   boolean toInclusive);
SortedSet<E> subSet(E fromElement, E toElement); //[from，to)
```

## Map接口

<tt>HashMap</tt>和<tt>TreeMap</tt>的基本操作类似：

```java
//添加k-v键值对
	//若之前有key存在，则替换并返回之前key对应的value；否则返回null
V put(K key, V value);
void putAll(Map<? extends K, ? extends V> m);

//删除k-v键值对
V remove(Object key); //删除并返回key对应的value，若key不存在返回null

//修改k-v键值对
V replace(K key, V value); //修改并返回key对应的oldvalue

//查找k-v键值对
boolean containsKey(Object key);    boolean containsValue(Object value);
V get(Object key); //key存在 ? 返回v : null

//返回map中所有key或value组成的集合
Collection<V> values();  
Set<K> keySet();         
```

### HashMap

```java
//查找
V getOrDefault(Object key, V defaultValue) //key存在 ? 返回v : defaultValue 
```

### TreeMap

```java
//删除
Map.Entry<K,V> pollFirstEntry();       Map.Entry<K,V> pollLastEntry();

//查找
Map.Entry<K,V> firstEntry();           K firstKey();
Map.Entry<K,V> lastEntry();            K lastKey();

//截取map
NavigableMap<K,V> subMap(K fromKey, boolean fromInclusive,
                                    K toKey,   boolean toInclusive);
SortedMap<K,V> subMap(K fromKey, K toKey); //[from, to)

NavigableMap<K,V> tailMap(K fromKey, boolean inclusive);
SortedMap<K,V> tailMap(K fromKey); //[from, ...]
```

