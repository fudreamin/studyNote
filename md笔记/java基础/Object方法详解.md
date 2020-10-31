# Object方法详解

## 概述

我们先使用快捷键`Alt`+`7`展开类的结构，可以看到Object类的结构，Object并没有类变量（独立于方法之外的变量，用 static 修饰）或者实例变量（独立于方法之外的变量，不过没有 static 修饰），只有十个基本方法（其中`wait`方法有三个重载），通常说法的九大方法会忽略`registerNatives`方法。

```java
package java.lang;


public class Object {

    private static native void registerNatives();
    static {
        registerNatives();
    }

    public final native Class<?> getClass();

    public native int hashCode();

    public boolean equals(Object obj) {
        return (this == obj);
    }

    protected native Object clone() throws CloneNotSupportedException;

    public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
    }

    public final native void notify();

    public final native void notifyAll();

    public final native void wait(long timeout) throws InterruptedException;

    public final void wait(long timeout, int nanos) throws InterruptedException {
        if (timeout < 0) {
            throw new IllegalArgumentException("timeout value is negative");
        }
        if (nanos < 0 || nanos > 999999) {
            throw new IllegalArgumentException(
                                "nanosecond timeout value out of range");
        }
        if (nanos > 0) {
            timeout++;
        }
        wait(timeout);
    }

    public final void wait() throws InterruptedException {
        wait(0);
    }

    protected void finalize() throws Throwable { }
}

```

## 方法详解

### registerNatives()

通常，为了让JVM找到你的本地函数，它们必须以某种方式命名。例如，`java.lang.Object.registerNatives`对应的C函数被命名`Java_java_lang_Object_registerNatives`。通过使用`registerNatives`（或者说，JNI函数`RegisterNatives`），你可以任意指定你的C函数。

### getClass()

返回对象所属的类，通常在反射中使用。

```java
public class Test {
    public static void main(String[] args) {
        Object object = new Object();
        System.out.println(object.getClass());
    }
}
//结果：class java.lang.Object
```

### hashCode()

返回字符串的哈希码，需要注意的是，即使两个对象的hashCode相同，也无法直接确认这两个对象是同一个对象，因为比较两个对象的哈希值时可能出现哈希碰撞。而且，可以看出`hashCode()`方法并没有用`final`修饰，也就是说该方法是支持重写的，事实上，比如`String`之类的类，其实已经对该方法进行了重写。

```java
// String的hashCode()方法
public int hashCode() {
    int h = hash;
    if (h == 0 && value.length > 0) {
        char val[] = value;

        for (int i = 0; i < value.length; i++) {
            h = 31 * h + val[i];
        }
        hash = h;
    }
    return h;
}
```

### equals()

`equals()`方法在`Object`中就是使用`==`来比较两个对象。`==`比较两个变量本身的值，即两个对象在内存中的首地址。java中，对象的首地址是它在内存中存放的起始地址，它后面的地址是用来存放它所包含的各个属性的地址，所以内存中会用多个内存块来存放对象的各个参数，而通过这个首地址就可以找到该对象，进而可以找到该对象的各个属性。注意，`equals()`方法也可以进行重写，比如在`String`类中比较的就是两个字符串值是否相同。

### clone()

在开始之前，我们需要先谈一谈深拷贝和浅拷贝这个经久不衰的话题：

- 浅拷贝：被复制对象的所有值属性都含有与原来对象的相同，而所有的对象引用属性仍然指向原来的对象。

- 深拷贝：在浅拷贝的基础上，所有引用其他对象的变量也进行了clone，并指向被复制过的新对象。

那么`clone()`是深拷贝还是浅拷贝呢？

```java
public class Test1027 {

    public static void main(String[] args) {
        try {
            Person p1 = new Person();
            p1.setId(1);
            p1.setAddress(new Address());
            p1.getAddress().setValue(1000000);

            Person p2 = (Person) p1.clone();
            System.out.println(p1 == p2);

            p1.setId(2);
            p1.getAddress().setValue(2000000);
            System.out.println(p2.getId());
            System.out.println(p2.getAddress().getValue());
            
            System.out.println(p1.getAddress() == p2.getAddress());

        } catch (CloneNotSupportedException exception) {
            System.out.println(exception);
        }

    }

}

class Address {
    private int size;
    private int value;

    public int getSize() {
        return size;
    }

    public void setSize(int size) {
        this.size = size;
    }

    public int getValue() {
        return value;
    }

    public void setValue(int value) {
        this.value = value;
    }
}

//注意：Object本身并没有实现cloneable接口并且clone为protected方法，所以如果想使用clone拷贝对象需要先让对象实现Cloneable方法并重写clone()方法。
class Person implements Cloneable {

    private int id;

    private String name;

    private Address address;


    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Address getAddress() {
        return address;
    }

    public void setAddress(Address adress) {
        this.address = adress;
    }

    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}

//结果：
false
1
2000000
true
```

通过结果我们可以看出，拷贝后的两个类并不相同，并且基本类型并不会因为原对象的改变而改变，但是，对于对象引用依然指向原对象参数的引用，所以可以肯定，Object默认的拷贝方法仍然是浅拷贝。

那么怎么实现深拷贝呢？

- 通过构造函数
- 通过重写clone方法
- 通过序列化

### toString()

打印出类的类名和十六进制的hashcode值，中间以@符号相连。正如之前在hashcode描述，我们仍然不能通过toString的相同直接推导出两个类相同。

```java
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

### notify()、notifyAll()、wait()

三个都是在多线程环境下使用的方法，

**wait()**：作用是挂起当前线程，释放获取到的锁，直到别的线程调用了这个对象的notify或notifyAll方法。

**notify()**：作用是唤醒因调用wait挂起的线程，如果有挂起线程，随机唤醒一个。

**notifyAll**()：作用是唤醒全部因调用wait挂起的线程。

 提到wait(),很容易想到另一个多线程环境下常用的方法——sleep()，他们都是在多线程的环境下，都可以在程序的调用处阻塞指定的毫秒数，并且都可以通过interrupt()方法 打断线程的暂停状态 ，从而使线程立刻抛出InterruptedException。

但同时，二者也有一些区别：

1. wait()是Object类的普通方法，而sleep()是Thread的静态方法；
2. 每个对象都有一个锁来控制同步访问。Synchronized关键字可以和对象的锁交互，来实现线程的同步。 sleep()方法没有释放锁，而wait()方法释放了锁，使得其他线程可以使用同步控制块或者方法；
3.  wait()，notify()和notifyAll()只能在同步控制方法或者同步控制块里面使用，而sleep()可以在任何地方使用；
4.  sleep()必须捕获异常，而wait()，notify()和notifyAll()不需要捕获异常

### finalize()

对于finalize()方法，深入理解Java虚拟机一书中有较为详细的描述：

> 即使在可达性分析算法中判定为不可达的对象，也不是“非死不可”的，这时候它们暂时还处于“缓刑”阶段，要真正宣告一个对象死亡， 至少要经历两次标记过程:如果对象在进行可达性分析后发现没有与GC Roots相连接的引用链，那它将会被第一次标记，随后进行一次筛选， 筛选的条件是此对象是否有必要执行finalize()方法。假如对象没有覆盖finalize0方法，或者finalize()方法已经被虚拟机调用过，那么虚拟机将这两种情况都视为“没有必要执行”。
>
> 如果这个对象被判定为有必要执行finalize()方法，那么这个对象将会被放置在一个名为：F-Queue的队列之中，并在稍后由一条虚拟机自动建立的、低优先级的Finalizer线程去执行。这里所谓的“执行”是指虚拟机会触发这个方法，但并不承诺会等待它运行结束。这样做的原因是，如果一个对象finalize()方法中执行缓慢，或者发生死循环（更极端的情况），将很可能会导致F-Queue队列中的其他对象永久处于等待状态，甚至导致整个内存回收系统崩溃。finalize()方法是对象脱逃死亡命运的最后一次机会，稍后GC将对F-Queue中的对象进行第二次小规模标记，如果对象要在finalize()中成功拯救自己----只要重新与引用链上的任何的一个对象建立关联即可，譬如把自己赋值给某个类变量或对象的成员变量，那在第二次标记时它将移除出“即将回收”的集合。如果对象这时候还没逃脱，那基本上它就真的被回收了。