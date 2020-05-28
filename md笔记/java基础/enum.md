# 枚举（enum)类型

- 枚举变量：变量的取值只在一个有限的集合内。

- enum关键字声明枚举类，且都是Enum的子类（但不需要写extends);
- enum内部有多少个值，就有多少个实例对象；
- 不能直接new枚举类对象。

```java
public class Test {
    public static void main(String[] args) {
        Size s1=Size.SMALL;
        Size s2=Size.SMALL;
        Size s3=Size.LARGE;
        System.out.println(s1==s2);//true
        System.out.println(s2==s3);//false
    }

}

enum Size {
    SMALL,MEDIUM,LARGE,EXTRA_LARGE;
}
```

- 除了枚举的内容，还可以添加属性/构造函数/方法；
- 构造函数只能是package-private(default)或者private，内部调用。

```java
public class Test2 {
    public static void main(String[] args) {
        Fruit f1=Fruit.APPLE;
        System.out.println(f1.getPrice());//10
    }
}

enum Fruit{
    APPLE(10),ORANGE(8);

    private int price;

    Fruit(int price){
        this.price=price;
    }

    public int  getPrice(){
        return this.price;
    }
}

```

- 所有的enum都是Enum的子类，也继承了相应方法：

| 方法                                                         | 说明                                       |
| ------------------------------------------------------------ | ------------------------------------------ |
| final int ordinal()                                          | 返回枚举值所在的索引值                     |
| final int compareTo(E o)                                     | 比较两个枚举值的索引位置大小               |
| String toString()                                            | 返回枚举值的字符串表示                     |
| static <T extends Enum<T>> T valueOf(Class<T> enumType, String name) | 返回具有指定名称的此类型的枚举常量。       |
| values()                                                     | 按声明顺序返回包含此枚举类型的常量的数组。 |

注意：values()方法是编译器插入到enum定义中的static方法，所以，将enum实例向上转型为父类Enum时，values()就不可访问了。

**优点**：

- 限定取值集合
- 减少实例对象