# MyBatis

配置pom.xml

```xml
<dependencies>

    <!-- 配置mybatis -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.4.5</version>
    </dependency>

    <!-- sqlserver驱动 -->
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>6.4.0.jre8</version>
    </dependency>

</dependencies>

<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.xml</include>
            </includes>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.xml</include>
            </includes>
        </resource>
    </resources>
</build>

```
建立数据库

（略）

将新建数据库表对应实体类Account

```java
package entity;

public class Account {
	private int id;
	private String name;
	private int age;
	private String adress;

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

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

	public String getAdress() {
		return adress;
	}

	public void setAdress(String adress) {
		this.adress = adress;
	}

	@Override
	public String toString() {
		return "Account [id=" + id + ", name=" + name + ", age=" + age + ", adress=" + adress + "]";
	}

}

```

创建MyBatis配置文件config.xml(文件名自定义)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
	<!-- 配置MyBatis运行环境 -->
	<environments default="development">
		<environment id="development">
			<!-- 配置JDBC事务管理 -->
			<transactionManager type="JDBC"></transactionManager>
			<!-- POOLED配置JDBC连接池 -->
			<dataSource type="POOLED">
				<property name="driver"
					value="com.microsoft.sqlserver.jdbc.SQLServerDriver"></property>
				<property name="url"
					value="jdbc:sqlserver://127.0.0.1:1433;DatabaseName=user"></property>
				<property name="username" value="sa"></property>
				<property name="password" value="123456"></property>
			</dataSource>
		</environment>
	</environments>

</configuration>
```

#### 使用原生接口

1、MyBatis框架需要开发者自定义SQL语句，卸载Mapper.xml文件中，实际开发中，会为每个实体类创建对应Mapper.xml，定义管理对象数据的SQL。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC   
    "-//mybatis.org//DTD Mapper 3.0//EN"  
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.mapper.AccountMapper">
	<insert id="save" parameterType="com.entity.Account">
		insert into account(name,age,adress) values(#{name},#{age},#{adress})
	</insert>
<!-- 	<update id="update"></update>
	<delete id="delete"></delete>
	<select id="select"></select> -->
</mapper>
```

2、在全局配置文件config中注册AccountMapper.xml

```xml
<!-- 注册AccountMapper.xml -->
<mappers>
	<mapper resource="com/mapper/AccountMapper.xml"></mapper>
</mappers>
```

3、调用MyBatis原生接口来执行添加操作

```java
public static void main(String[] args) {
		System.out.println("执行开始");
		//加载MyBatis配置文件
		InputStream inputStream=Test.class.getClassLoader().getResourceAsStream("config.xml");
		SqlSessionFactoryBuilder sessionFactoryBuilder=new SqlSessionFactoryBuilder();
		SqlSessionFactory sessionFactory=new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession=sessionFactory.openSession();
		String statement="com.mapper.AccountMapper.save";
		Account account=new Account(2, "小明", 5, "jsdl");
		sqlSession.insert(statement,account);
		//提交事务
		sqlSession.commit();
		System.out.println("执行成功");
	}
```

#### Mapper代理自定义实现接口

-Mapper.xml自定义接口，定义相关业务方法

-编写方法对应的Mapper.xml



1、自定义接口

```java
package com.repository;

import java.util.List;

import com.entity.Account;

public interface AccountRepository {
	
	public int save(Account account);

	public int update(Account account);

	public int deleteById(int id);

	public Account findById(int id);

	public List<Account> findall();
}

```

2、创建接口对应的Mapper.xml，定义接口方法对应的SQL语句。

stament标签可根据SQL执行的业务选择insert、delete、update、select。

MyBatis框架会根据SQL执行的业务选择接口实现类的代理对象。

规则：

Mapper.xml中namespace为接口的全类名。

Mapper.xml中statement的id为接口中对应的方法名。

Mapper.xml中statement的parameterType和接口对应方法的参数类型一致。

Mapper.xml中statement的resultType和接口中对应方法的返回值一致。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC   
    "-//mybatis.org//DTD Mapper 3.0//EN"  
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.repository.AccountRepository">

	<!-- 返回值一定是int类型，所以不必去写resultType -->
	<insert id="save" parameterType="com.entity.Account">
		insert into
		account(name,age,adress) values(#{name},#{age},#{adress})
	</insert>

	<update id="update" parameterType="com.entity.Account">
		update account set
		name=#{name},age=#{age},adress=#{adress}
	</update>

	<delete id="deleteById" parameterType="int">
		delete from account where
		id=#{id}
	</delete>

	<select id="selectById" parameterType="int"
		resultType="com.entity.Account">
		select from account where id=#{id}
	</select>

	<!-- 直接写集合中的泛型 -->
	 <select id="findAll" resultType="com.entity.Account">
		select * from account
	</select> 
</mapper>
```

3、在config.xml中注册AccountRepository.xml

```xml
<mapper resource="com/repository/AccountRepository.xml"></mapper>
```

4、调用接口的代理对象，完成相关业务

```java
package com.test;

import java.io.InputStream;
import java.util.List;

import org.apache.ibatis.scripting.xmltags.ForEachSqlNode;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import com.entity.Account;
import com.repository.AccountRepository;

public class Test2 {

	public static void main(String[] args) {
		InputStream inputStream = Test.class.getClassLoader().getResourceAsStream("config.xml");
		SqlSessionFactoryBuilder sessionFactoryBuilder = new SqlSessionFactoryBuilder();
		SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		SqlSession sqlSession = sessionFactory.openSession();
		// 获取实现接口的代理对象
		AccountRepository accountRepository = sqlSession.getMapper(AccountRepository.class);
		accountRepository.deleteById(1);
		Account account1=new Account(2,"xioaming2",18," jsd1");
		accountRepository.update(account1);
		List<Account> list = accountRepository.findAll();
		for (Account account2 : list) {
			System.out.println(account1.toString());
		}
		sqlSession.close();
	}

}

```

