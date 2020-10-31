# Java集合框架（三）——HashMap

## 概述

HashMap是基于Map接口实现的键值对存储集合，允许键值为空，但本身并不支持同步操作。

其实HashMap源码开头一串极长的英文已经基本完整的向我们介绍了HashMap的基本概念，但是相信绝大多数人应该能这大串的英文毫无兴趣，所以这边直接上翻译了，英文版详见源码。[翻译地址](https://baijiahao.baidu.com/s?id=1632613082650129465&wfr=spider&for=pc)

- HashMap基于Map接口实现，提供了所有可选的map操作，允许空键和空值。(除了非线程安全、允许空键和空值，HashMap和HashTable基本类似)。HashMap不保证键值对的顺序(即存储顺序跟put顺序不一致)，也不保证这个顺序不会发生改变(当rehash时，可能会发生变化)。
- 假使Hash函数能够将元素均匀的分布在不同的桶中，HashMap对于基本操作(get和put)可以提供稳定的性能( constant-time performance)。遍历元素所需时间取决于HashMap实例的容量(capacity，桶的数量)和大小(size,键值对的数量)。如果对迭代性能要求较高，那么不要将初始容量设得过高或者负载因子设得过低(初始容量过高或负载因子过低，都会导致较多的空桶)。
- 影响HashMap实例性能的参数有两个：初始容量和负载因子( initial capacity and load factor)。容量是指哈希表中桶(bucket)的数量，初始容量即哈希表在创建时的桶的数量，默认为16。负载因子是衡量哈希表在扩容前其容量能达到多满的一个尺度，当哈希表中的条目(entries )数超过了负载因子与当前容量的乘积时，就会进行rehash操作(重建哈希表内部数据结构),哈希表中桶的数量会扩展为原来的两倍。

- HashMap默认负载因子为0.75，是对时间和空间开销的权衡。较高的负载因子可以减少空间开销，但却会增加查找成本(反映在HashMap的大多操作上，包括get和put等)。在设置初始容量时，要考虑哈希表预计存储的条目数以及负载因子大小，尽量减少rehash操作的次数。如果初始容量大于预计存放条目数除以负载因子的商(预计条目数小于初始容量与负载因子的乘积)，则永远不会发生 rehash 操作。
- 如果提前知道要存储很多条目，在创建HahMap实例时就要设置足够大的初始容量，这样可以省去自动扩容带来的性能开销。 注意，如果很多键的哈希值相同， 会降低哈希表的性能，这些键可以实现Comparable，这样这个类(HashMap)就能在这些键之间使用比较来减少这种影响。(To ameliorate impact, when keys are java.lang.Comparable, this class may use comparison order among keys to help break ties.)
- HashMap的实现不是同步的，如果多个线程并发地访问一个哈希表，且至少有一个线程修改哈希表结构，那么必须在该HashMap的外部进行同步(结构改变是指添加或删除键值对的操作，仅仅改变键值对的值不是结构性修改)。通常，可以对包含这个哈希表的对象进行同步来实现线程安全(This is typically accomplished by synchronizing on some object that naturally encapsulates the map. )。如果没有这样的对象，应该用 Collections.synchronizedMap来包装HashMap。为了避免意外的非同步访问，最好在创建的时候就对HashMap进行包装，示例如下:
  Map m = Collections.synchronizedMap(new HashMap(…));
- HashMap遍历元素采用快速失败(fail-fast:)机制。在迭代器创建之后，除了迭代器自己的remove方法，任何对哈希表的结构性修改，迭代器都会抛出并发修改异常(ConcurrentModificationException)。这样，对于并发修改，迭代器简洁明了的抛出异常，避免了未来某个不确定时间某个不确定行为带来的风险。
- 需要注意的是，迭代器并不能保证一定快速失败，一般来说，对于非同步的并发修改，迭代器不可能做出坚决的保证一定会快速失败。快速失败迭代器只是尽最大努力抛出 ConcurrentModificationException。因此，编写依赖于此异常的程序的做法是错误的，正确的做法应该是：迭代器的快速失败行为仅用于检测程序Bug。
- HashMap类是Java集合框架中的一员。

看完这冗长的翻译，我们看下HashMap的继承与实现：

```java
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable 
```

- 继承`AbstractMap`类并实现`Map`接口来Map的实现基本方法，至于为什么继承`AbstractMap`还去实现`Map`接口在ArrayList已经细说了。
- 实现`Cloneable`，标志该类可以克隆。
- 实现`Serializable`接口，标志该类可以序列化。

## 基本参数

`serialVersionUID`：序列化版本号，验证序列化 和 反序列化过程中，对象是否还保持一致。

```java
private static final long serialVersionUID = 362498820763181265L;
```

`DEFAULT_INITIAL_CAPACITY`：初始容量，默认16（必须是2的次幂）

```java
/**
 * The default initial capacity - MUST be a power of two.
 */
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
```

`MAXIMUM_CAPACITY`：最大容量，默认二的三十次方。

```java
/**
 * The maximum capacity, used if a higher value is implicitly specified
 * by either of the constructors with arguments.
 * MUST be a power of two <= 1<<30.
 */
static final int MAXIMUM_CAPACITY = 1 << 30;
```

`DEFAULT_LOAD_FACTOR`：默认加载因子，默认0.75。

```java
/**
 * The load factor used when none specified in constructor.
 */
static final float DEFAULT_LOAD_FACTOR = 0.75f;
```

`TREEIFY_THRESHOLD`：当链表中的值的数量超过8时链表会转成红黑树。

```java
/**
 * The bin count threshold for using a tree rather than list for a
 * bin.  Bins are converted to trees when adding an element to a
 * bin with at least this many nodes. The value must be greater
 * than 2 and should be at least 8 to mesh with assumptions in
 * tree removal about conversion back to plain bins upon
 * shrinkage.
 */
static final int TREEIFY_THRESHOLD = 8;
```

`UNTREEIFY_THRESHOLD`：当红黑树中的值的数量大于6时会从红黑树转回链表。

```java
/**
 * The bin count threshold for untreeifying a (split) bin during a
 * resize operation. Should be less than TREEIFY_THRESHOLD, and at
 * most 6 to mesh with shrinkage detection under removal.
 */
static final int UNTREEIFY_THRESHOLD = 6;
```

`MIN_TREEIFY_CAPACITY`：当Map里面的数量超过这个值时，表中的桶才能进行树形化 ，否则桶内元素太多时会扩容，而不是树形化。为了避免进行扩容、树形化选择的冲突，这个值不能小于 4 * TREEIFY_THRESHOLD。

```java
/**
 * The smallest table capacity for which bins may be treeified.
 * (Otherwise the table is resized if too many nodes in a bin.)
 * Should be at least 4 * TREEIFY_THRESHOLD to avoid conflicts
 * between resizing and treeification thresholds.
 */
static final int MIN_TREEIFY_CAPACITY = 64;
```

