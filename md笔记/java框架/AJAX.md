# AJAX

AJAX简介

AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。

AJAX 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。

AJAX 不是新的编程，指的是一种交互方式，异步加载，客户端与服务器的数据更新在局部页面的技术，不需要刷新整个界面（局部刷新）。

优点：1、局部刷新，效率更高    2、用户体验更好

#### AJAX String/text数据类型交互

index.jsp

```xml
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
    <script src="/js/jquery-3.3.1.min.js"></script>
    <!--引入juery文件：webapp/js/jquery-3.3.1.min.js-->
    <script type="text/javascript">
        function a1() {
            $.ajax({
                url: "/ajax/a1",//相当于action,交互目标地址
                type: "post",//请求方式
                data: {"id": $(".text1").val()},//数据
                dataType: "text",//服务器返回数据类型
                success: function (data) {
                    alert(data);
                }
            })

        }
    </script>
</head>
<body>
<input type="text"><br>
<input type="text" class="text1"><br>
<button class="btn" value="提交" onclick="a1()
">提交
</button>
</body>
</html>
```

ajaxController

```java
@Controller
@RequestMapping("/ajax")
public class ajaxController {

    @RequestMapping("/a1")
    @ResponseBody
    public String a1(String id){
        String str="进入handler..."+id;
        return str;
    }

}
```

#### AJAX json数据类型交互

##### JSON(JavaScript Object Notation, JS 对象简谱) 

- 一种轻量级的数据交换格式
- JSON 是 JS 对象的字符串表示法，它使用文本表示一个 JS 对象的信息，本质是一个字符串

SpringBoot内部已经实现了json相关的转换，所以只需要直接返回封装的实体类即可。

user.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page isELIgnored="false" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>Title</title>
    <script src="/js/jquery-3.3.1.min.js"></script>
    <script>
         function a2() {
            $.ajax({
                url: "/ajax/a2",
                type: "get",
                success: function (list) {
                    html="";
                    for(var i=0;i<list.length;i++){
                        html+=  "<tr>"+
                            "<td>"+list[i].id+"</td>"+
                        "<td>"+list[i].name+"</td>"+
                        "<td>"+list[i].age+"</td>"+
                        "</tr>";
                        console.log(html);
                    }
                    $(".message").html(html);
                }
            })

        }
        function findById() {
            $.ajax({
                url:"/ajax/findById",
                type: "post",
                data: {"id": $(".t1").val()},
                success:function (data) {
                    html="";
                    html+="<tr>"+
                        "<td>"+data.id+"</td>"+
                        "<td>"+data.name+"</td>"+
                        "<td>"+data.age+"</td>"+
                        "</tr>";
                    console.log(html+"fjl");
                    $(".message").html(html);
                }
            })

        }
    </script>
</head>
<body>
<input type="text" class="t1">
<button onclick="findById()">查找</button>
<button onclick="a2()">显示数据</button>
<table>
    <tr>
        <th>id</th>
        <th>姓名</th>
        <th>年龄</th>
    </tr>
    <tbody class="message">

    </tbody>
</table>
</body>
</html>
```

ajaxController

```java
@RequestMapping("/a2")
@ResponseBody
public List<User> a2(HttpServletRequest request){
    List<User> list=new ArrayList<User>();
    User user=new User(1,"张三",16);
    User user1=new User(2,"李四",17);
    User user2=new User(3,"王五",26);
    User user3=new User(4,"老刘",36);
    list.add(user);
    list.add(user1);
    list.add(user2);
    list.add(user3);
    return list;
}

@RequestMapping(value = "/findById",method = RequestMethod.POST)
@ResponseBody
public User findById(Integer id,HttpServletRequest request){
    List<User> list=new ArrayList<User>();
    User user=new User(1,"张三",16);
    User user1=new User(2,"李四",17);
    User user2=new User(3,"王五",26);
    User user3=new User(4,"老刘",36);
    list.add(user);
    list.add(user1);
    list.add(user2);
    list.add(user3);
    User aim_user=list.get(id);
    System.out.println(aim_user);
    return aim_user;
}
```