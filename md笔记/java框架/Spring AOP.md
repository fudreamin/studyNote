# Spring AOP

## 1、基本概念

**AOP** 为 **A**spect **O**riented **P**rogramming 的缩写，意思为面向切面编程，是通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。

AOP 是 OOP 的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

作用：在程序运行期间，在不修改源码的情况下对方法进行功能增强。

优势：减少重复代码，提高开发效率，并且便于维护。

## 2、AOP实现

AOP 的底层是通过 Spring 提供的的动态代理技术实现的。在运行期间，Spring通过动态代理技术动态的生成代理对象，代理对象方法执行时进行增强功能的介入，在去调用目标对象的方法，从而完成功能的增强。

常用的动态代理技术：

**JDK 代理** : 基于接口的动态代理技术

**cglib 代理**：基于父类的动态代理技术

![image-20201009091438452](http://img.reainyday.top/image-20201009091438452.png)

### 2.1 JDK动态代理

```java
//增强类
package proxy.jdk;

public class Advice {

    public void before(){
        System.out.println("前置增强...");
    }

    public void afterReturning(){
        System.out.println("后置增强...");
    }
}

//目标接口
package proxy.jdk;

public interface TargetInterface {

    public void save();
}

//目标类
package proxy.jdk;

public class Target implements TargetInterface{
    public void save() {
        System.out.println("save running...");
    }
}

//动态代理
package proxy.jdk;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class ProxyTest {

    public static void main(String[] args) {

        //创建目标对象
        final Target target = new Target();

        //获得增强对象
        final Advice advice = new Advice();

        //返回值就是动态生成的代理对象
        TargetInterface proxy = (TargetInterface) Proxy.newProxyInstance(
                target.getClass().getClassLoader(),//目标对象类加载器
                target.getClass().getInterfaces(),//目标对象相同的的接口字节码数组
                new InvocationHandler() {
                    //调用代理对象的任何方法实质执行的invoke方法
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        //前置增强
                        advice.before();
                        //执行目标方法
                        Object invoke = method.invoke(target, args);
                        //后置增强
                        advice.afterReturning();
                        return invoke;
                    }
                }
        );

        proxy.save();
    }
}

```

### 2.2 cglib 动态代理

新版本的Spring已经集成了cglib，所以不需要额外引入cglib的插件。

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.0.11.RELEASE</version>
    </dependency>
</dependencies>
```

```java
//目标类
package proxy.cglib;

public class Target {
    public void save() {
        System.out.println("save running...");
    }
}

//增强类
package proxy.cglib;

public class Advice {

    public void before(){
        System.out.println("前置增强...");
    }

    public void afterReturning(){
        System.out.println("后置增强...");
    }
}

//动态代理
package proxy.cglib;

import org.springframework.cglib.proxy.Enhancer;
import org.springframework.cglib.proxy.MethodInterceptor;
import org.springframework.cglib.proxy.MethodProxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class ProxyTest {

    public static void main(String[] args) {

        //创建目标对象
        final Target target = new Target();

        //获得增强对象
        final Advice advice = new Advice();

        //返回值就是动态生成的代理对象 基于cglib
        //1、创建增强器
        Enhancer enhancer = new Enhancer();
        //2、设置父类（目标）
        enhancer.setSuperclass(Target.class);
        //3、设置回调
        enhancer.setCallback(new MethodInterceptor() {
            public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
                //执行前置
                advice.before();
                //执行目标
                Object invoke = method.invoke(target, args);
                //执行后置
                advice.afterReturning();
                return invoke;
            }
        });
        //4、创建代理对象
        Target proxy = (Target) enhancer.create();

        proxy.save();
    }
}

```

