# Jsoup爬虫

[TOC]

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1583555021404.png" alt="1583555021404" style="zoom: 80%;" />

（其实jsoup的connect功能已经能直接凭借网页的url提取html文档了，所以其实可以省略HttpClient，至于两者具体区别，论坛说法不一，多数人认为HttpClient相比jsoup的connect可以满足更高层次的需求）

## HttpClient

HttpClient 是Apache Jakarta Common 下的子项目，可以用来提供高效的、最新的、功能丰富的支持 HTTP 协议的客户端编程工具包，并且它支持 HTTP 协议最新的版本和建议。 

### 功能

- 实现了所有 HTTP 的方法（GET、POST、PUT、HEAD、DELETE、HEAD、OPTIONS 等）
- 支持 HTTPS 协议
- 支持代理服务器（Nginx等）等
- 支持自动（跳转）转向

#### 1、引入依赖

```xml
  <dependency>
   <groupId>org.apache.httpcomponents</groupId>
   <artifactId>httpclient</artifactId>
   <version>4.5.2</version>
 </dependency> 
```
#### 2、简单使用（Get无参请求）

```java
package com.test;

import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;

import org.apache.http.HttpEntity;
import org.apache.http.ParseException;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.config.RequestConfig;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.util.EntityUtils;

public class HttpClientGet {

	public static void main(String[] args) {
		// 获得Http客户端(可以理解为:你得先有一个浏览器;注意:实际上HttpClient与浏览器是不一样的)
		CloseableHttpClient httpClient = HttpClientBuilder.create().build();

		// 创建Get请求
		HttpGet httpGet = new HttpGet("http://www.baidu.com");
		// 响应模型
		CloseableHttpResponse response = null;
		try {
			// 配置信息
			RequestConfig requestConfig = RequestConfig.custom()
					// 设置连接超时时间(单位毫秒)
					.setConnectTimeout(5000)
					// 设置请求超时时间(单位毫秒)
					.setConnectionRequestTimeout(5000)
					// socket读写超时时间(单位毫秒)
					.setSocketTimeout(5000)
					// 设置是否允许重定向(默认为true)
					.setRedirectsEnabled(true).build();

			// 将上面的配置信息 运用到这个Get请求里
			httpGet.setConfig(requestConfig);

			// 由客户端执行(发送)Get请求
			response = httpClient.execute(httpGet);

			// 从响应模型中获取响应实体
			HttpEntity responseEntity = response.getEntity();
            //对entity对象进行解码
			String html=EntityUtils.toString(responseEntity, "UTF-8");
			System.out.println("响应状态为:" + response.getStatusLine());
			if (responseEntity != null) {
				System.out.println("响应内容长度为:" + responseEntity.getContentLength());
				System.out.println("响应内容为:" + html);
			}
		} catch (ClientProtocolException e) {
			e.printStackTrace();
		} catch (ParseException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			try {
				// 释放资源
				if (httpClient != null) {
					httpClient.close();
				}
				if (response != null) {
					response.close();
				}
			} catch (IOException e) {
				e.printStackTrace();
			}
		}

	}

}

```

## Jsoup

 jsoup 是一款Java 的HTML解析器，可直接解析某个URL地址、HTML文本内容。它提供了一套非常省力的API，可通过DOM，CSS以及类似于jQuery的操作方法来取出和操作数据。 

### 功能

从一个URL，文件或字符串中解析HTML；

使用DOM或CSS选择器来查找、取出数据；

可操作HTML元素、属性、文本；

#### 输入

##### 1、引入依赖

```xml
<!-- https://mvnrepository.com/artifact/org.jsoup/jsoup --><dependency>
     <groupId>org.jsoup</groupId>
        <artifactId>jsoup</artifactId>
        <version>1.8.3</version>
 </dependency>
```

##### 2、解析和遍历html文档

```java
String html = "<html><head><title>First parse</title></head>"
  + "<body><p>Parsed HTML into a doc.</p></body></html>";
Document doc = Jsoup.parse(html);
```

##### 3、解析body片段

```java
String html = "<div><p>Lorem ipsum.</p>";
Document doc = Jsoup.parseBodyFragment(html);
Element body = doc.body();
```

##### 3、从一个URL加载一个Document

```java
Document doc = Jsoup.connect("http://example.com/").get();
String title = doc.title();
```

- 只支持 Web URLs (http和https 协议) 

##### 4、从一个文件加载文档

```java
File input = new File("/tmp/input.html");
Document doc = Jsoup.parse(input, "UTF-8", "http://example.com/";);
```

#### 数据抽取

##### 1、使用DOM方法来遍历一个文档

```java
File input = new File("/tmp/input.html");
Document doc = Jsoup.parse(input, "UTF-8", "http://example.com/");

Element content = doc.getElementById("content");
Elements links = content.getElementsByTag("a");
for (Element link : links) {
  String linkHref = link.attr("href");
  String linkText = link.text();
}
```

查找元素：

- getElementById(String id)
- `getElementsByTag(String tag)`
- `getElementsByClass(String className)`
- `getElementsByAttribute(String key)` (and related methods)
- Element siblings: `siblingElements()`, `firstElementSibling()`, `lastElementSibling()`; `nextElementSibling()`, `previousElementSibling()`
- Graph: `parent()`, `children()`, `child(int index)`

元素数据：

- `attr(String key)`获取属性`attr(String key, String value)`设置属性
- `attributes()`获取所有属性
- `id()`, `className()` and `classNames()`
- `text()`获取文本内容`text(String value)` 设置文本内容
- `html()`获取元素内HTML`html(String value)`设置元素内的HTML内容
- `outerHtml()`获取元素外HTML内容
- `data()`获取数据内容（例如：script和style标签)
- `tag()` and `tagName()`

操作HTML和文本

- `append(String html)`, `prepend(String html)`
- `appendText(String text)`, `prependText(String text)`
- `appendElement(String tagName)`, `prependElement(String tagName)`
- `html(String value)`

##### 2、使用选择器语法来查找元素

```java
File input = new File("/tmp/input.html");
Document doc = Jsoup.parse(input, "UTF-8", "http://example.com/");

Elements links = doc.select("a[href]"); //带有href属性的a元素
Elements pngs = doc.select("img[src$=.png]");
  //扩展名为.png的图片

Element masthead = doc.select("div.masthead").first();
  //class等于masthead的div标签

Elements resultLinks = doc.select("h3.r > a"); //在h3元素之后的a元素
```

jsoup elements对象支持类似于[CSS](http://www.w3.org/TR/2009/PR-css3-selectors-20091215/) (或[jquery](http://jquery.com/))的选择器语法，来实现非常强大和灵活的查找功能。.

这个`select` 方法在`Document`, `Element`,或`Elements`对象中都可以使用。且是上下文相关的，因此可实现指定元素的过滤，或者链式选择访问。

Select方法将返回一个`Elements`集合，并提供一组方法来抽取和处理结果。

Selector选择器:

- `tagname`: 通过标签查找元素，比如：`a`
- `ns|tag`: 通过标签在命名空间查找元素，比如：可以用 `fb|name` 语法来查找 `` 元素
- `#id`: 通过ID查找元素，比如：`#logo`
- `.class`: 通过class名称查找元素，比如：`.masthead`
- `[attribute]`: 利用属性查找元素，比如：`[href]`
- `[^attr]`: 利用属性名前缀来查找元素，比如：可以用`[^data-]` 来查找带有HTML5 Dataset属性的元素
- `[attr=value]`: 利用属性值来查找元素，比如：`[width=500]`
- `[attr^=value]`, `[attr$=value]`, `[attr*=value]`: 利用匹配属性值开头、结尾或包含属性值来查找元素，比如：`[href*=/path/]`
- `[attr~=regex]`: 利用属性值匹配正则表达式来查找元素，比如： `img[src~=(?i)\.(png|jpe?g)]`
- `*`: 这个符号将匹配所有元素

Selector选择器组合使用:

- `el#id`: 元素+ID，比如： `div#logo`
- `el.class`: 元素+class，比如： `div.masthead`
- `el[attr]`: 元素+class，比如： `a[href]`
- 任意组合，比如：`a[href].highlight`
- `ancestor child`: 查找某个元素下子元素，比如：可以用`.body p` 查找在"body"元素下的所有 `p`元素
- `parent > child`: 查找某个父元素下的直接子元素，比如：可以用`div.content > p` 查找 `p` 元素，也可以用`body > *` 查找body标签下所有直接子元素
- `siblingA + siblingB`: 查找在A元素之前第一个同级元素B，比如：`div.head + div`
- `siblingA ~ siblingX`: 查找A元素之前的同级X元素，比如：`h1 ~ p`
- `el, el, el`:多个选择器组合，查找匹配任一选择器的唯一元素，例如：`div.masthead, div.logo`

伪选择器selectors:

- `:lt(n)`: 查找哪些元素的同级索引值（它的位置在DOM树中是相对于它的父节点）小于n，比如：`td:lt(3)` 表示小于三列的元素
- `:gt(n)`:查找哪些元素的同级索引值大于`n``，比如`： `div p:gt(2)`表示哪些div中有包含2个以上的p元素
- `:eq(n)`: 查找哪些元素的同级索引值与`n`相等，比如：`form input:eq(1)`表示包含一个input标签的Form元素
- `:has(seletor)`: 查找匹配选择器包含元素的元素，比如：`div:has(p)`表示哪些div包含了p元素
- `:not(selector)`: 查找与选择器不匹配的元素，比如： `div:not(.logo)` 表示不包含 class=logo 元素的所有 div 列表
- `:contains(text)`: 查找包含给定文本的元素，搜索不区分大不写，比如： `p:contains(jsoup)`
- `:containsOwn(text)`: 查找直接包含给定文本的元素
- `:matches(regex)`: 查找哪些元素的文本匹配指定的正则表达式，比如：`div:matches((?i)login)`
- `:matchesOwn(regex)`: 查找自身包含文本匹配指定正则表达式的元素
- 注意：上述伪选择器索引是从0开始的，也就是说第一个元素索引值为0，第二个元素index为1等

##### 3、从元素抽取属性、文本和HTML

```java
String html = "<p>An <a href='http://example.com/'><b>example</b></a> link.</p>";
Document doc = Jsoup.parse(html);//解析HTML字符串返回一个Document实现
Element link = doc.select("a").first();//查找第一个a元素

String text = doc.body().text(); // "An example link"//取得字符串中的文本
String linkHref = link.attr("href"); // "http://example.com/"//取得链接地址
String linkText = link.text(); // "example""//取得链接地址中的文本

String linkOuterH = link.outerHtml(); 
    // "<a href="http://example.com"><b>example</b></a>"
String linkInnerH = link.html(); // "<b>example</b>"//取得链接内的html内容
```

基本方法：

- 要取得一个属性的值，可以使用`Node.attr(String key)` 方法
- 对于一个元素中的文本，可以使用`Element.text()`方法
- 对于要取得元素或属性中的HTML内容，可以使用`Element.html()`, 或 `Node.outerHtml()`方法

其他方法：

- `Element.id()`
- `Element.tagName()`
- `Element.className()` and `Element.hasClass(String className)`

##### 4、处理URLs

```java
Document doc = Jsoup.connect("http://www.open-open.com").get();

Element link = doc.select("a").first();
String relHref = link.attr("href"); // == "/"
String absHref = link.attr("abs:href"); // "http://www.open-open.com/"
```

说明：

在HTML元素中，URLs经常写成相对于文档位置的相对路径： `...`. 当你使用 `Node.attr(String key)` 方法来取得a元素的href属性时，它将直接返回在HTML源码中指定定的值。

假如你需要取得一个绝对路径，需要在属性名前加 `abs:` 前缀。这样就可以返回包含根路径的URL地址`attr("abs:href")`

因此，在解析HTML文档时，定义base URI非常重要。

如果你不想使用`abs:` 前缀，还有一个方法能够实现同样的功能 `Node.absUrl(String key)`。

##### 5、简单示例程序：打印微博热搜

```java
package com.test;

import java.io.IOException;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;

public class JsoupWeiBoTest {

	public static void main(String[] args) {
		//网址url，jsoup的connect只能检索http与https协议，所以开头必须加上http/https
		String url="https://s.weibo.com/top/summary?Refer=top_hot&topnav=1&wvr=6";
		try {
			//从url加载document
			Document document=Jsoup.connect(url).get();
			//查找所有td标签下的a元素，就可以导出微博热搜
			Elements elements=document.select("td>a");
			//用index代表热搜第几条，由于有一条置顶热搜，所以从0开始
			int index=0;
			//遍历elements
			for (Element element:elements) {
				System.out.print(index+":");
				//打印a标签内内容：即该条微博热搜
				System.out.println(element.text());
				index++;
			}
			//结束
			System.out.println("爬取完毕");
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
	}

}
```

#### 数据修改

（本次作业基本使用不到，暂略）

参考网址：

 https://www.open-open.com/jsoup/ 

 https://blog.csdn.net/justry_deng/article/details/81042379 