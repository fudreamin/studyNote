# Java注解

Java.Annotation

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

