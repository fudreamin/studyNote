# Java集合框架（二）——ArrayList

## 1、概述

`ArrayList`继承的是`AbstractList`类，并且实现了`List<E>, RandomAccess, Cloneable, java.io.Serializable`四个接口。

- 其实，当看见这个时第一反应可能就是，既然已经继承了`AbstractList`类，那为什么还需要去实现`List`接口，更何况`AbstractList`内部已经实现了`List`接口。不仅如此，去看`HashSet`、`HashMap`之类的源码都会出现已经继承了抽象类，但仍然去实现接口的情况。StackOverflow上很早就有这样的问题，多数人的理解是如果不实现接口，对程序也不会产生变化，实现接口的目的更多是为了起一个标志性的作用，当你点开`ArrayList`，你就知道他是一个`List`，而不需要去看文档或者一直追溯源码。但是跟据 java 集合框架的创始人Josh Bloch描述，这样的写法是一个失误。在java集合框架中，类似这样的写法很多，最开始写java集合框架的时候，他认为这样写，在某些地方可能是有价值的，直到他意识到错了。显然的，JDK的维护者，后来不认为这个小小的失误值得去修改，所以就这样存在下来了。(这波......尴尬......<( _ _ )>......)

- 如果说前者只是一种猜测，那`RandomAccess`接口的实现就是官方的一种规定了，当我们点开`RandomAccess`时，会发现他就是一个单纯的接口，没有任何其他代码。官方文档 [RandomAccess javadoc](http://docs.oracle.com/javase/7/docs/api/java/util/RandomAccess.html)对其的解释为：

  > Marker interface used by `List` implementations to indicate that they support fast (generally constant time) random access. The primary purpose of this interface is to allow generic algorithms to alter their behavior to provide good performance when applied to either random or sequential access lists.

  也就是说`RandomAccess` 是一个标志接口，表明实现这个这个接口的 List 集合是支持快速随机访问的。

  那么快速随机访问是什么意思？

  - ArrayList底层的数据结构是Object[]存储，数组在内存分配里，是一段连续的内存空间，譬如分配的内存地址是002，003，004，005四个地址，那么我们如果要随机访问角标为3的数据，只需要访问002+3=005的数据就行了，这就是快速随机访问。

  - 而 LinkedList是链表结构，在内存里是不连续的，相邻的数据之间用指针指向即可。如001<->003<->004<->005.那么此时，我们要访问角标为3的数据，就只能从第一个数据开始依次迭代，找到第4个数据，这就是顺序访问。

- 而`java.io.Serializable`与`RandomAccess`作用一致，同样是标志接口，不过`java.io.Serializable`表示的是该类支持序列化。关于序列化可以看：[关于序列化的博文](https://blog.csdn.net/so_geili/article/details/78931742)

```Java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

说完了继承和实现，我们可以在看下`ArrayList`的几个默认参数:

```java
private static final long serialVersionUID = 8683452581122892189L;

/**
 * Default initial capacity.
 */
private static final int DEFAULT_CAPACITY = 10;

/**
 * Shared empty array instance used for empty instances.
 */
private static final Object[] EMPTY_ELEMENTDATA = {};

/**
 * Shared empty array instance used for default sized empty instances. We
 * distinguish this from EMPTY_ELEMENTDATA to know how much to inflate when
 * first element is added.
 */
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

/**
 * The array buffer into which the elements of the ArrayList are stored.
 * The capacity of the ArrayList is the length of this array buffer. Any
 * empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
 * will be expanded to DEFAULT_CAPACITY when the first element is added.
 */
transient Object[] elementData; // non-private to simplify nested class access

/**
 * The size of the ArrayList (the number of elements it contains).
 *
 * @serial
 */
private int size;
```

| 参数                              | 描述                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| serialVersionUID                  | 序列化版本号，实现序列化功能                                 |
| DEFAULT_CAPACITY                  | 默认数组长度，默认为10                                       |
| EMPTY_ELEMENTDATA                 | 默认的空数组                                                 |
| DEFAULTCAPACITY_EMPTY_ELEMENTDATA | 默认的空数组（与前者在不同构造方法使用，这个在无参构造函数中使用） |
| elementData                       | 真正用于存放数据的数组                                       |
| size                              | ArrayList实际大小                                            |
| modCount                          | 继承自AbstractList，记录修改次数                             |

## 2、方法解析

### 2.1 构造函数

```java
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```

最常用的构造函数，不带参数，将实际数组置为默认初始空数组。

```Java
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
        this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
        this.elementData = EMPTY_ELEMENTDATA;
    } else {
        throw new IllegalArgumentException("Illegal Capacity: "+
                                           initialCapacity);
    }
}
```

手动设置初始容量，如果初始容量大于0，新建一个传入数字大小的Object数组，如果传入数字为0，将实际数组

置为初始空数组，否则直接抛出异常。

```Java
/**
 * Constructs a list containing the elements of the specified
 * collection, in the order they are returned by the collection's
 * iterator.
 *
 * @param c the collection whose elements are to be placed into this list
 * @throws NullPointerException if the specified collection is null
 */
public ArrayList(Collection<? extends E> c) {
    elementData = c.toArray();
    if ((size = elementData.length) != 0) {
        // c.toArray might (incorrectly) not return Object[] (see 6260652)
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    } else {
        // replace with empty array.
        this.elementData = EMPTY_ELEMENTDATA;
    }
}
```

如果传过来的参数是一个集合，就直接创建一个和指定集合内容一样的`ArrayList`,并且将size设置为集合长度。

### 2.2 添加元素

添加一个元素：

```Java
/**
 * Appends the specified element to the end of this list.
 *
 * @param e element to be appended to this list
 * @return <tt>true</tt> (as specified by {@link Collection#add})
 */
public boolean add(E e) {
    //确认是否需要扩容
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    //在size的下一位加入元素,并让size自增一位
    elementData[size++] = e;
    return true;
}

//参数为需要的最小容量大小
private void ensureCapacityInternal(int minCapacity) {
    ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}

private void ensureExplicitCapacity(int minCapacity) {
    //修改次数+1
    modCount++;

    //如果容量不足以放下，则扩容
    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        //扩容
        grow(minCapacity);
}


private static int calculateCapacity(Object[] elementData, int minCapacity) {
    //如果容器为空
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        return Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    return minCapacity;
}

/**
 * Increases the capacity to ensure that it can hold at least the
 * number of elements specified by the minimum capacity argument.
 *
 * @param minCapacity the desired minimum capacity
 */
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    //扩容至原先的1.5倍
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

在指定位置添加元素：

（看源码我们可以注意到，我们无法在实际容量size后的位置添加元素，即使容器容量足够的情况下。

```JAVA
public void add(int index, E element) {
    //检查待插入位置是否合法
    rangeCheckForAdd(index);

    ensureCapacityInternal(size + 1);  // Increments modCount!!
    //将待插入位置后所有数字后移
    System.arraycopy(elementData, index, elementData, index + 1,
                     size - index);
    //添加元素
    elementData[index] = element;
    size++;
}

/**
 * A version of rangeCheck used by add and addAll.
 */
private void rangeCheckForAdd(int index) {
    if (index > size || index < 0)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}

private void ensureCapacityInternal(int minCapacity) {
    ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}
```

添加集合：

```java
public boolean addAll(Collection<? extends E> c) {
    Object[] a = c.toArray();
    int numNew = a.length;
    ensureCapacityInternal(size + numNew);  // Increments modCount
    System.arraycopy(a, 0, elementData, size, numNew);
    size += numNew;
    return numNew != 0;
}
```

