# HashMap

## 1、概念

- HashMap在jdk1.7中是由数组+链表的数据结构，而到了jdk1.8之后，经过进一步的优化，引入了红黑树的结构。
- HashMap是非同步的，可能出现线程安全问题，且HashMap支持空。

## 2、基本参数

**默认加载因子**：当数组数据量达到0.75就触发扩容。如果加载因子太小，空间浪费严重，如果加载因子太大，扩容时挪动数据量较大，消耗时间过多。

```
DEFAULT_LOAD_FACTOR = 0.75F;
```

**默认长度**：16。

```
DEFAULT_INITIAL_CAPACITY = 1<<4；
```

**最大长度**：2^30。 int取值范围-2^31——2^31-1，所以无法取31。

```
MAXIMUM_CAPACITY = 1<<30
```

**树化阈值**：当链表内数据达到8，链表转为红黑树 。

```
TREEIFY_THRESHOLD = 8
```

**还原阈值**：当链表内数据降至6，链表转回正常链表，（之所以不在8转回，是为了起缓冲作用，避免链表和红黑树之间频繁的转换）。

```
UNTREEIFY_THRESHOLD = 6
```

**最小树长度**：64

```
static final int MIN_TREEIFY_CAPACITY = 64; 
```

源码:

```java
public V put(K key, V value) {
    return this.putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent, boolean evict) {
    HashMap.Node[] tab;
    int n;
    if ((tab = this.table) == null || (n = tab.length) == 0) {
        n = (tab = this.resize()).length;
    }

    Object p;
    int i;
    if ((p = tab[i = n - 1 & hash]) == null) {
        tab[i] = this.newNode(hash, key, value, (HashMap.Node)null);
    } else {
        Object e;
        Object k;
        if (((HashMap.Node)p).hash == hash && ((k = ((HashMap.Node)p).key) == key || key != null && key.equals(k))) {
            e = p;
        } else if (p instanceof HashMap.TreeNode) {
            e = ((HashMap.TreeNode)p).putTreeVal(this, tab, hash, key, value);
        } else {
            int binCount = 0;

            while(true) {
                if ((e = ((HashMap.Node)p).next) == null) {
                    ((HashMap.Node)p).next = this.newNode(hash, key, value, (HashMap.Node)null);
                    if (binCount >= 7) {
                        this.treeifyBin(tab, hash);
                    }
                    break;
                }

                if (((HashMap.Node)e).hash == hash && ((k = ((HashMap.Node)e).key) == key || key != null && key.equals(k))) {
                    break;
                }

                p = e;
                ++binCount;
            }
        }

        if (e != null) {
            V oldValue = ((HashMap.Node)e).value;
            if (!onlyIfAbsent || oldValue == null) {
                ((HashMap.Node)e).value = value;
            }

            this.afterNodeAccess((HashMap.Node)e);
            return oldValue;
        }
    }

    ++this.modCount;
    if (++this.size > this.threshold) {
        this.resize();
    }

    this.afterNodeInsertion(evict);
    return null;
}
```



