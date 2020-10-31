# Springboot知识点

## 1、Springboot优势

1. 在Spring 1.x 时代，我们需要处理大量的xml配置文件，需要在开发的类和配置文件之间频繁切换。
2. 而到了Spring 2.x 时代，随着JDK1.5带来的注解支持，Spring提供了声明Bean的注解（例如@Component、@Service），大大减少了配置量。主要使用的方式是应用的基本配置（如数据库配置）用xml，业务配置用注解。
3. Spring 3.0 引入了基于 Java 的配置能力，这是一种类型安全的可重构配置方式，可以代替 XML。我们目前刚好处于这个时代，Spring4.x和Spring Boot都推荐使用Java配置。
4. Spring 4.0 又引入了@Conditional注解，它的作用是按照一定的条件进行判断，满足条件给容器注册bean。
5. Spring Boot 简化了基于Spring的应用开发。SpringBoot一方面简化了绝大多数的用户配置，只需要在properties或者yaml文件进行简单配置；而另一方面，SpringBoot可以使用内置的tomcat，只需要“run”就能创建一个独立的、生产级别的Spring应用。

## 2、Springboot自动配置

