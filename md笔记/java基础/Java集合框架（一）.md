# Java集合框架（一）

**引言**

我们可以将集合可以看作是一种容器，用来存储对象信息。Java为了不同类型的集合定义了大量接口，而两大基本接口为：`Collection`和`Map`。对于`Collection`而言，存储的是元素集合，而`Map`存储的是键值对/值对映射。所有集合类都位于`java.util`包下，但支持多线程的集合类位于`java.util.concurrent`包下。

我们需要注意的是，数组并不是集合，数组和集合有两个很大的区别：

1. 数组长度固定，一旦创建便没有办法再次改变容量，而集合可以动态的改变容量。
2. 数组元素既可以是基本类型的值，也可以是对象，而集合只能保存对象类型。

## 1、具体集合

**`java.util.Collection`下的接口和继承类关系简易结构图：**

![img](http://img.reainyday.top/1078540-20191115163627420-566269456.png)

**`java.util.Map`下的接口和继承类关系简易结构图：**

![img](http://img.reainyday.top/1078540-20191115163647011-1193506491.png)

[图片引用]: https://www.cnblogs.com/dxflqm/p/11867611.html

（我们可以通过Idea的快捷键清晰的梳理集合类之间的继承和实现关系：选中某个集合类后配合快捷键`Ctrl`+`H`便可以看见该集合类与其他类的继承和实现关系，再配合`Ctrl`+`Shift`+`Alt`+`N` 查找类效率更佳）。

## 2、Collection

**Collection常见方法：**

![img](http://img.reainyday.top/1362965-20190111153657049-1511371305.png)

### 2.1 List

我们常用的`List`有`ArrayList`、`LinkedList`，而对于`Vector`和`Stack`并不推荐使用，因为

1. 首先，而`Vector`是JDK 1.0的产物，为了保证同步在很多方法都加上了`synchronized` 关键字，在单线程情况下效率不高。

2. `Vector`底层依然是依靠数组实现，而不是链表，也就是说`Vector`依然需要对数组进行扩容。不过值得注意的是相比`ArrayList`每次扩容至原来的1.5倍，`Vector`直接扩容到原来的两倍。

   ```Java
   //Vector扩容机制
   private void grow(int minCapacity) {
       // overflow-conscious code
       int oldCapacity = elementData.length;
       int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                        capacityIncrement : oldCapacity);
       if (newCapacity - minCapacity < 0)
           newCapacity = minCapacity;
       if (newCapacity - MAX_ARRAY_SIZE > 0)
           newCapacity = hugeCapacity(minCapacity);
       elementData = Arrays.copyOf(elementData, newCapacity);
   }
   //ArrayLst扩容机制
   private void grow(int minCapacity) {
       // overflow-conscious code
       int oldCapacity = elementData.length;
       int newCapacity = oldCapacity + (oldCapacity >> 1);
       if (newCapacity - minCapacity < 0)
           newCapacity = minCapacity;
       if (newCapacity - MAX_ARRAY_SIZE > 0)
           newCapacity = hugeCapacity(minCapacity);
       // minCapacity is usually close to size, so this is a win:
       elementData = Arrays.copyOf(elementData, newCapacity);
   }
   ```

3. `Stack`是继承自`Vector`，因此`Stack` 可以复用 `Vector` 大量方法，这使得 `Stack` 在设计上不严谨。

所以更多情况下，我们更推荐使用双端队列`Deque`接口来实现栈，`Deque`可以满足`Vector`需要的FIFO，同时，`Deque`既支持有容量限制的双端队列，也支持没有固定大小限制的双端队列，并且在性能上优于`Vector` 。

而对于`ArrayList`和`LinkedList`，前者是底层是数组，而后者底层数据结构就是链表。这也就说明两者各有春秋：

1. 在查询效率上，`ArrayList`无论是通过下标查找还是对于排序好的数据集（二分查找）进行值查找效率都明显高于需要逐个遍历的`LinkedList`，即使对于无序数据集的值查找，`ArrayList`性能依旧优于`LinkedList`,虽然两者查询时间复杂度都为O(n)，但因为数组的连续内存，会有一部分或者全部数据一起进入到CPU缓存，而链表还需要在去内存中根据上下游标查找,，CPU缓存比内存块太多。
2. 但是，在插入和删除的效率上，`ArrayList`在添加元素时需要通过数组扩容来动态改变状态，在删除时又需要移动所有后续元素才能实现，而`LinkedList`只需要改变指针指向便可以很简单的实现容量改变，而不需要移动数据。

## 2.1 Queue

队列通常以 FIFO（先进先出）的方式排序各个元素。不过优先级队列`PriorityQueue`和 LIFO（后进先出） 队列（或堆栈）例外，前者根据提供的比较器或元素的自然顺序对元素进行排序，后者按 LIFO的方式对元素进行排序。

## 2.3 Set

`Set`是用来存储无序元素的集合，并且元素之间的值不能相同。Java提供了`HashSet`、`LinkedHashSet`、`TreeSet`三种常用的Set实现方式。

- 对于`HashSet`而言，其实就是依赖`HashMap`来实现，将要放入`HashSet`的值作为`HashMap`的key，而值使用一个不可变的`PRESENT`对象。所以`HashSet`的查找效率达到了O(1)，但没办法有序遍历。

  ```Java
  //HashSet部分源码
  private transient HashMap<E,Object> map;
  
  // Dummy value to associate with an Object in the backing Map
  private static final Object PRESENT = new Object();
  
  public boolean add(E e) {
      return map.put(e, PRESENT)==null;
  }
  ```

- 对于`LinkedHashSet`而言，`LinkedHashSet`正好介于`HashSet`和`TreeSet`之间，它依然继承了`HashSet`，但它同时维护了一个双链表来记录插入的顺序，所以基本方法的复杂度为O(1)。但是因为相比于`HashSet`它还需要维护一个链表，所以性能会不如`HashSet`。
- 对于`TreeSet`而言，`TreeSet`是采用树结构实现(称为红黑树算法)，元素是按顺序进行排列，主要有`add()`、`remove()`以及`contains()`等方法，它们都是复杂度为`O(log (n))`的方法；它还提供了一些处理排序的set方法，如`first()`, `last()`, `headSet()`, `tailSet()`等。

## 3、Map

`Map`存储的key-value键值对形式的对象，我们平时使用最广的必定是`HashMap`了，这个我们之后会着重讲，所以在这就不多赘述了。

**Map常见方法**：

![img](http://img.reainyday.top/1362965-20190111155052064-620367509.png)