# Vue入门

Vue 是一套用于构建用户界面的**渐进式框架**。

MVVM模式：

![](https://img-blog.csdnimg.cn/20190302180508660.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4Mjg3OTUy,size_16,color_FFFFFF,t_70)

第一个vue程序：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>

<body>
    <div id="app">
        {{message}}
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.11"></script>
    <script>
        var vm=new Vue({
            el:"#app",
            data:{
                message:"Hello Vue!"
            }
        });
    </script>
</body>
</html>
```

## 1、Vue语法

### 1.1 循环和判断

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>

<body>
<div id="app">
    <h1>{{ok}}</h1>
    <h1 v-if="ok">Yes</h1>
    <h1 v-else>No</h1>
</div>

<div id="app2">
    <h2 v-if="message==='test'">true</h2>
    <h2 v-else>false</h2>
            <sapn v-for="item in items">
                {{item.name}}
            </sapn>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.6.11"></script>
<script>
    var vm = new Vue({
        el: "#app",
        data: {
            ok: true
        }
    });

    var vm = new Vue({
        el: "#app2",
        data: {
            message: "test",
            items: [
                {name: '张三'},
                {name: '李四'},
                {name: '王五'}
            ]
        }
    });
</script>
</body>
</html>
```

### 1.2 绑定事件

```html
<!DOCTYPE html>
<html lang="en" xmlns:v-on="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>

<body>
<div id="app">
    {{message}}
    <button v-on:click="sayHi">提示</button>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.6.11"></script>
<script>
    var vm=new Vue({
        el:"#app",
        data:{
            message:"Hello Vue!"
        },
        methods:{//方法必须定义在vue的methods对象中
            sayHi:function () {
                alert(this.message);
            }
        }
    });
</script>
</body>
</html>
```

### 1.3 双向绑定

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>

<body>
<div id="app">
    <input type="text" v-model="message">{{message}}
    <br>
    <select v-model="fruit">
        <option value="" disabled>请选择</option>
        <option>apple</option>
        <option>pear</option>
        <option>banner</option>
    </select>
    {{fruit}}
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.6.11"></script>
<script>
    var vm=new Vue({
        el:"#app",
        data:{
            message:"Hello Vue!",
            fruit:""
        }
    });
</script>
</body>
</html>
```

### 1.4 组件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>

<body>
<div id="app">
    <mydiv v-for="item in items" v-bind:data="item"></mydiv>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.6.11"></script>
<script>
    //定义组件
    Vue.component("mydiv",{
        props:["data"],
        template:"<li>{{data}}</li>"
    });
    var vm=new Vue({
        el:"#app",
        data:{
            message:"Hello Vue!",
            items:["张三","李四","王五"]
        }
    });
</script>
</body>
</html>
```

### 1.5 Axios异步通讯

```html
<!DOCTYPE html>
<html lang="en" xmlns:v-bind="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        [v-clock]{
            display: none;//防止闪烁
        }
    </style>
</head>
<body>
<div id="app" v-clock>
    {{info.name}}
    <a v-bind:href="info.url">跳转</a>//注意：网址需要带http请求，否则会默认在前面拼接项目路径
/div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.6.11"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script>
    //ES6新特性
    var vm = new Vue({
        el: "#app",
        data(){
            return{
                info:null
            }
        },
        mounted(){
            axios.get("message.json").then(response=>(this.info=response.data));
        }
    });
</script>
</body>
</html>
```

### 1.6 计算属性

- 计算属性是基于它们的响应式依赖进行缓存的。只在相关响应式依赖发生改变时它们才会重新求值。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>

<body>
<div id="app">
    {{message}}
    <hr>
    {{data}}
    <hr>
    {{now}}
    <br>
    {{message}}
    <hr>
    {{data2()}}
    <hr>
    {{now2()}}
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.6.11"></script>
<script>
    var vm=new Vue({
        el:"#app",
        data:{
            message:"Hello Vue!"
        },
        methods: {
            data2:function() {
                return this.message.split("").reverse().join("");
            },
            now2: function () {
                return Date.now();
            }
        },
        computed: {
            data:function() {
                return this.message.split("").reverse().join("");
            },
            now: function () {
                return Date.now();
            }
        }
    });
</script>
</body>
</html>
```

