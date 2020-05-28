# Java值传递

- 以前一直只是单纯的知道Java是值传递的，但最近写代码的时候突然意识到当传一个对象时，我在方法中改变对象的属性值，在主方法中的属性值也被改变了。于是就开始了查资料捋思路的过程。

- **结论**：**Java只有值传递，没有引用传递。**（网上不少的当传的是基本类型时使用值传递，当传的是引用类型时是引用传递是错误的）。

- 首先得弄明白：基本类型和引用类型

  1. **Java八大基本类型：**byte、short、int、long、float、double、char、boolean
  2. **Java引用类型**：类、接口、数组、枚举等。

  ```java
  int num = 10;//基本类型
  String str = "hello";//引用类型
  
  //修改
  num=20;
  str="java";
  ```

  - 对于基本类型变量num，赋值运算符将会直接修改变量的值，原来的数据将被覆盖掉，被替换为新的值。对于引用类型变量str，赋值运算符只会改变变量中所保存的对象的地址信息，原来对象的地址被覆盖掉，重新写入新对象的地址数据。但原来的对象本身并不会被改变，只是不再被任何引用所指向的对象，即“垃圾对象”，后续会被垃圾回收器回收。

- 其次了解赋值运算符（=）

  - 对于基本类型 num ，赋值运算符会直接改变变量的值，原来的值被覆盖掉。
  - 如果修改str，**赋值运算符会改变引用中所保存的地址**，原来的地址被覆盖掉。**但是原来的对象不会被改变（重要）。**
  - 没有被任何引用所指向的对象是垃圾，会被垃圾回收器回收。

```java
//第一个例子：基本类型
void foo(int value) {
    value = 100;
}
foo(num); // num 没有被改变

//第二个例子：没有提供改变自身方法的引用类型
void foo(String text) {
    text = "windows";
}
foo(str); // str 也没有被改变

//第三个例子：提供了改变自身方法的引用类型
StringBuilder sb = new StringBuilder("iphone");
void foo(StringBuilder builder) {
    builder.append("4");
}
foo(sb); // sb 被改变了，变成了"iphone4"。

//第四个例子：提供了改变自身方法的引用类型，但是不使用，而是使用赋值运算符。
StringBuilder sb = new StringBuilder("iphone");
void foo(StringBuilder builder) {
    builder = new StringBuilder("ipad");
}
foo(sb); // sb 没有被改变，还是 "iphone"。
```

- 最后，回到问题：
  - java中方法参数传递方式是按值传递。
  - 如果参数是基本类型，传递的是基本类型的字面量值的拷贝。
  - 如果参数是引用类型，传递的是该参量所引用的对象在堆中地址值的拷贝。
  - 如下代码，a传递给change的传递的是a所引用的对象在堆中地址值的拷贝，所以二者地址相同，就相当于change方法拿到了a的钥匙，对a这个对象内部的改变都会保留下来。但如果对a对象整体赋值，对象地址就会改变，相当于你又建了一栋房子，并且对钥匙进行了改造，你可以对新房子进行改造，但你的改造并不会影响到原来的房子。

```java
public class Test {
    public static void main(String[] args) {
        A a=new A(5);
        System.out.println(a.hashCode());
        change(a);
        System.out.println(a.hashCode());
        System.out.println(a.value);
    }

    public static void change(A b){
        b.value=2;
//        b=new A(5);
        System.out.println(b.hashCode());
    }
}

class A{
    int value;

    public A() {
    }

    public A(int value) {
        this.value = value;
    }
}
```