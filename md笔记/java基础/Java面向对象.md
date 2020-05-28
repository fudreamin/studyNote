# Java面向对象

[TOC]

## java多态（Polymorphism）

1、概述

多态是同一个行为具有多个不同表现形式或形态的能力。

多态体现为父类引用变量指向子类对象。

2、条件/要素

- 继承

- 重写

- 父类引用指向子类对象

3、优点

提高代码的扩展性，降低代码的耦合性，使关联性变少。

4、多态简单实现

```java
//动物父类
public class Animal {
	public void shout() {
		System.out.println("动物发出声音...");
	}
}
//子类——Cat
public class Cat extends Animal{
	public void shout() {
		System.out.println("喵喵喵...");
	}
	
	public void eat() {
		System.out.println("小猫在吃鱼...");
	}
}
//子类——Dog
public class Dog extends Animal{
	public void shout() {
		System.out.println("汪汪汪...");
	}
	
	public void sleep() {
		System.out.println("小狗睡了...");
	}
}
//类Girl
public class Girl {
	public void play(Animal an) {
		an.shout();
	}
}
//测试类
public class Test {
	public static void main(String[] args) {
		Dog dog=new Dog();
		Animal an=dog;
		Girl girl=new Girl();	//即Animal an=new Dog();
		girl.play(an);
	}
}
```

5、多态内存

![](https://cdn.u1.huluxia.com/g3/M01/E5/D7/wKgBOV58TN-AC4VnAAFM9hH7-E0240.png)

## Java注解（Annotation)

1、概念

Annotation是 JDK5.0 引入的一种注释机制。

- 作用

  不是程序本身，可以对程序作出解释（这点与（comment）没有什么区别）。

  可以被其他程序（比如：编译器）读取。

- 格式

  注解是以“@注释名“在代码中存在，还可以添加一些参数值。

- 使用位置

  可以附加在package、class、method、field等上面，相当于给他们添加了额外的辅助信息。我们可以通过反射机制编程实现对这些元数据的访问。

2、内置注解

- @Override：检查该方法是否是重写方法。如果发现其父类，或者是引用的接口中并没有该方法时，会报编译错误。

- @Deprecated：标记过时方法。如果使用该方法，会报编译警告。

- @SuppressWarnings：警告镇压，指示编译器去忽略注解中声明的警告。

3、元注解

- @Retention：表示需要在什么级别保存该注释信息，用于描述注解的生命周期。（SOURCE<CLASS<RUNTIME)

- @Documented：说明该注解是否包含在javadoc(用户文档)中。

- @Target：用于描述注解的使用范围。

- @Inherited：标记这个注解是继承于哪个注解类(默认 注解并没有继承于任何子类)，说明子类可以继承父类给注解。

```java
package com.dreamin.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Inherited;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Myannotation
public class Test {
	
	//测试元注解
	@Myannotation
	public void test() {
		
	}

}

//定义一个注解
@Target(value= {ElementType.METHOD,ElementType.TYPE})
@Retention(value=RetentionPolicy.RUNTIME)
@Documented
@Inherited
@interface Myannotation{
	
}
```

4、自定义注解

- 使用@interface自定义注解时，自动继承了java.lang.annotation.Annotation接口

- 分析
  - @interface用来声明一个注释，格式public @interface 注解名 {定义内容}。
  - 其中的每一个方法实际上时声明了一个配置参数。
  - 方法的名称就是参数的名称。
  - 返回值类型就是参数的类型（返回值只能是基本类型：Class、String、enum)。
  - 可以通过default来声明参数的默认值。
  - 如果只有一个参数成员，一般参数名为value。
  - 注解元素必须要有值，我们定义注解元素时，经常使用空字符串、0作为默认值。

```java
package com.dreamin.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

//自定义注解
public class Test02 {

	// 注解可以显式赋值，如果没有默认值，我们就必须给注解赋值
	@Myannotation2(age = 18)
	public void test() {

	}

	@Myannotation3("dreamin")
	public void test2() {

	}

}

@Target({ ElementType.METHOD, ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)
@interface Myannotation2 {
	// 注解的参数：参数类型 + 参数名 ();
	String name() default "";

	int age();

	int id() default -1;// 如果默认值为-1，代表不存在

	String[] schools() default { "清华大学", "北京大学" };

}

@Target({ ElementType.METHOD, ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)
@interface Myannotation3 {
	String value();
}
```

## Java反射（Reflection)

1、概念

Java的反射机制是指在程序的运行状态中，可以构造任意一个类的对象，可以了解任意一个对象所属的类，可以了解任意一个类的成员变量和方法，可以调用任意一个对象的属性和方法。这种动态获取程序信息以及动态调用对象的功能称为Java语言的反射机制。

2、作用

- 可以在运行时检查类、接口、方法和变量等信息，无需知道类的名字，方法名等。
- 可以在运行时实例化新对象，调用方法以及设置和获取变量值。

3、Class对象

- Class类被创建后的对象就是Class对象，注意，Class对象表示的是自己手动编写类的类型信息，比如创建一个Shapes类，那么，JVM就会创建一个Shapes对应Class类的Class对象，该Class对象保存了Shapes类相关的类型信息。实际上在Java中每个类都有一个Class对象，每当我们编写并且编译一个新创建的类就会产生一个对应Class对象并且这个Class对象会被保存在同名.class文件里(编译后的字节码文件保存的就是Class对象)，那为什么需要这样一个Class对象呢？是这样的，当我们new一个新对象或者引用静态成员变量时，Java虚拟机(JVM)中的类加载器子系统会将对应Class对象加载到JVM中，然后JVM再根据这个类型信息相关的Class对象创建我们需要实例对象或者提供静态变量的引用值。需要特别注意的是，手动编写的每个class类，无论创建多少个实例对象，在JVM中都只有一个Class对象，即在内存中每个类有且只有一个相对应的Class对象，挺拗口，通过下图理解（内存中的简易现象图）：
  

![](https://cdn.u1.huluxia.com/g3/M01/E7/06/wKgBOV58jXSAGdIQAAGPeBkZvaE539.png)

到这我们也就可以得出以下几点信息：

- Class类也是类的一种，与class关键字是不一样的。


- 手动编写的类被编译后会产生一个Class对象，其表示的是创建的类的类型信息，而且这个Class对象保存在同名.class的文件中(字节码文件)，比如创建一个Shapes类，编译Shapes类后就会创建其包含Shapes类相关类型信息的Class对象，并保存在Shapes.class字节码文件中。


- 每个通过关键字class标识的类，在内存中有且只有一个与之对应的Class对象来描述其类型信息，无论创建多少个实例对象，其依据的都是用一个Class对象。


- Class类只存私有构造函数，因此对应Class对象只能有JVM创建和加载


- Class类的对象作用是运行时提供或获得某个对象的类型信息，这点对于反射技术很重要(关于反射稍后分析)

4、类的加载与ClassLoader的理解

![](https://cdn.u1.huluxia.com/g3/M01/F1/FD/wKgBOV6AIAiAOKwxAAg--p2tiY0441.png)

```java
package com.dreamin.reflection;

public class Test05 {

	public static void main(String[] args) {
		A a = new A();
		System.out.println(A.m);
	}

}

/*
1、加载到内存，会产生一个Class对象
2、链接，链接结束后 m = 0 (默认初始值）
3、初始化（代码块自上而下合并）
	<clinit>() {
		System.out.println("A类静态代码块初始化");
		m = 300;
		m = 100;
	}
*/

class A {
		
	static {
		System.out.println("A类静态代码块初始化");
		m = 300;
	}

	static int m = 100;

	public A() {
		System.out.println("A的无参构造初始化");
	}
}

```

result:

```
静态代码块初始化
A的无参构造初始化
100
```

![](https://cdn.u1.huluxia.com/g3/M01/F2/19/wKgBOV6AKS-AF4-MAAaEJMh3CEc654.png)

```java
package com.dreamin.reflection;

//测试类什么时候会初始化
public class Test06 {
	
	static {
		System.out.println("Main类被加载");
	}

	public static void main(String[] args) throws ClassNotFoundException {
		//1、主动引用
		//Son son=new Son();
		//反射也会产生主动引用
		//Class c1=Class.forName("com.dreamin.reflection.Son");
		//不会产生类的初始化的方法
		//System.out.println(Son.b);
		//Son[] array=new Son[5];
		//System.out.println(Son.M);
	}

}

class Father {

	static int b = 2;

	static {
		System.out.println("父类被加载");
	}

}

class Son extends Father{
	static {
		System.out.println("子类被加载");
		m = 300;
	}

	static int m = 100;

	static final int M = 1;

}
```

![](https://cdn.u1.huluxia.com/g3/M01/F2/31/wKgBOV6AL8qABG4iAAfJ0y9i5FE700.png)



![](https://cdn.u1.huluxia.com/g3/M01/F2/42/wKgBOV6AM9SAZxTfAAnXueIF2uY941.png)

```java
package com.dreamin.reflection;

public class Test07 {

	public static void main(String[] args) throws ClassNotFoundException {
		
		//获取系统类的加载器
		ClassLoader systemClassLoader=ClassLoader.getSystemClassLoader();
		System.out.println(systemClassLoader);
		
		//获取系统类加载器的父类加载器-->扩展类加载器
		ClassLoader parent=systemClassLoader.getParent();
		System.out.println(parent);
		
		//获取扩展类加载器的父类加载器-->根加载器(c/c++)
		ClassLoader parent1=parent.getParent();
		System.out.println(parent1);
		
		//测试当前类是哪个加载器加载的
		ClassLoader classLoader=Class.forName("com.dreamin.reflection.Test07").getClassLoader();
		System.out.println(classLoader);
			
		//测试JDK内置的类是谁加载的
		classLoader=Class.forName("java.lang.Object").getClassLoader();
		System.out.println(classLoader);
		
		//如何获得系统类加载器的路径
		System.out.println(System.getProperty("java.class.path"));
		
		//双亲委派机制
		
	}
}
```

5、通过反射机制获取Class对象的三种方式

```java
public class Test1 {

	public static void main(String[] args) throws ClassNotFoundException, InstantiationException, IllegalAccessException {
		//获取反射对象三种方式
		//方法一
		Class c1=Class.forName("com.dreamin.reflection.Person");
		//方法二
		Person person=new Person();
		Class c2=person.getClass();
		//方法三
		Class c3=Person.class;
		//通过newInstance()方法无参构造Person对象
		System.out.println("=============");
		c1.newInstance();
		c2.newInstance();
		c3.newInstance();
		System.out.println("=============");
	}

}
```

6、通过反射机制操纵类的构造器

```java
public class Test2 {

	public static void main(String[] args) throws ClassNotFoundException, InstantiationException, IllegalAccessException, NoSuchMethodException, SecurityException, IllegalArgumentException, InvocationTargetException {
		
		Class c1=Class.forName("com.dreamin.reflection.Student");
		//无参构造对象
		Object obj=c1.newInstance();
		Student student=(Student)obj;//向下转型
		System.out.println(student);
		System.out.println("============");
		//有参构造对象
		//获取所有公有构造器
		Constructor[] cons=c1.getConstructors();
		for(Constructor con:cons) {
			System.out.println(con);
		}
		System.out.println("============");
		//获取所有构造器
		Constructor[] cons2=c1.getDeclaredConstructors();
		for(Constructor con:cons2) {
			System.out.println(con);
		}
		System.out.println("============");
		//获取指定构造器
		//获取指定公有无参构造器
		Constructor con1=c1.getConstructor();
		System.out.println(con1);
		//获取指定公有有参构造器
		Constructor con2=c1.getConstructor(String.class,int.class);
		//获取任何指定构造器c1.getConstructor(parameterTypes),不做代码展示
		//根据公有构造器创建对象
		System.out.println("============");
		Student student2=(Student)con2.newInstance("男",173988);
		System.out.println(student2);
		//对于私有构造器，只需要在创建对象前加入con2.setAccessible(true);即可
		
	}

}

```

7、通过反射机制操纵类的属性

```java
public class Test3 {
	public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException, SecurityException, InstantiationException, IllegalAccessException {
		Class c1=Class.forName("com.dreamin.reflection.Student");
		//获取公有属性（所有属性与之前格式一致）
		Field[] fields=c1.getFields();
		for(Field field:fields) {
			System.out.println(field);
		}
		System.out.println("============");
		//获取指定属性
		Field field=c1.getField("stuId");
		System.out.println(field);
		System.out.println("============");
		//对指定公有属性赋值（私有与之前处理方法一致）
		Student student=(Student)c1.newInstance();
		field.set(student, 123);
		System.out.println(student);
	}
}
```

8、通过反射操纵类的方法

```java
public class Test4 {

	public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, SecurityException, IllegalAccessException, IllegalArgumentException, InvocationTargetException, InstantiationException {
		Class c1=Class.forName("com.dreamin.reflection.Student");
		Student student=(Student)c1.newInstance();
		//获取公有方法
		Method[] methods=c1.getMethods();
		for(Method method:methods) {
			System.out.println(method);
		}
		System.out.println("============");
		//获取指定公有方法
		Method me=c1.getMethod("setStuId",int.class);
		System.out.println(me);
		//执行方法
		me.invoke(student,173988);
	}

}
```

9、通过反射操作注解

```java
package com.dreamin.reflection;

import java.lang.annotation.Annotation;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import java.lang.reflect.Field;

//联系反射操作注解
public class Test08 {
	public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException, SecurityException {
		Class c1 = Class.forName("com.dreamin.reflection.Student2");

		// 通过反射获得注解
		Annotation[] annotations = c1.getAnnotations();
		for (Annotation annotation : annotations) {
			System.out.println(annotation);
		}

		// 获得注解value的值
		Tablekuang tablekuang =(Tablekuang) c1.getAnnotation(Tablekuang.class);
		String value=tablekuang.value();
		System.out.println(value);
		
		//获得类指定的注解
		Field field=c1.getDeclaredField("name");
		Fieldkuang annotation=field.getAnnotation(Fieldkuang.class);
		System.out.println(annotation.columnName());
		System.out.println(annotation.type());
		System.out.println(annotation.length());
	}
}

//类名的注解
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@interface Tablekuang {
	String value();
}

//属性的注解
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@interface Fieldkuang {
	String columnName();

	String type();

	int length();
}

@Tablekuang("db_student")
class Student2 {
	@Fieldkuang(columnName = "db_id", type = "int", length = 10)
	private int id;
	@Fieldkuang(columnName = "db_age", type = "int", length = 10)
	private int age;
	@Fieldkuang(columnName = "db_name", type = "varchar", length = 3)
	private String name;

	public Student2() {

	}

	public Student2(int id, int age, String name) {
		this.id = id;
		this.age = age;
		this.name = name;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	@Override
	public String toString() {
		return "Student2 [id=" + id + ", age=" + age + ", name=" + name + "]";
	}

}
```

10、例子：支付方式

通过反射机制实现解耦合，降低代码量

```java
public interface Pay {
	public void pay();
}

public class ALiPay implements Pay{

	@Override
	public void pay() {
		System.out.println("支付宝支付完成》》》");
		
	}
	
}

public class WeChat implements Pay{

	@Override
	public void pay() {
		System.out.println("微信支付成功》》》");
	
	}

}

public class Test {

	public static void main(String[] args) throws ClassNotFoundException, InstantiationException, IllegalAccessException {
		String packageStr="com.dreamin.reflectiontest.";
		String payMethod=packageStr+"WeChat";
		
		Class c1=Class.forName(payMethod);
		Pay pay=(Pay)c1.newInstance();
		pay.pay();
	}

}
```

