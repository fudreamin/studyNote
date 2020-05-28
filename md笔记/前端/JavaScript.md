# JavaScript

## 1、什么是JavaScript

### 1.1 概述

- JavaScript是目前世界上最流行的脚本语言。
- JavaScript（简称“JS”） 是一种具有函数优先的轻量级，解释型或即时编译型的编程语言。
- ECMAScript是一种语言标准，是几个公司联合ECMA（European Computer Manufacturers Association）组织定制的JavaScript语言的标准，而JavaScript是网景公司对ECMAScript标准的一种实现。‘
- 由于浏览器的安全限制，以`file://`开头的地址无法执行如联网等JavaScript代码，你需要一个Web服务器，然后以`http://`开头的地址来正常执行所有JavaScript代码。

### 1.2 引入

- 外部引入

```html
<script src="js/hello.js"></script>
```

- 内部引入

```html
<script>
   alert("Hello World");
</script>
```

## 2、基本语法

### 2.1 数据类型

| 数据类型  | 解释说明                                                     |
| --------- | ------------------------------------------------------------ |
| Number    | JavaScript不区分整数和浮点数，统一用Number表示               |
| string    | 字符串是以单引号'或双引号"括起来的任意文本                   |
| boolean   | 一个布尔值只有`true`、`false`两种值                          |
| null      | `null`表示一个“空”的值，它和`0`以及空字符串`''`不同，`0`是一个数值，`''`表示长度为0的字符串，而`null`表示“空”。 |
| undefined | `undefined`表示值未定义，仅仅在判断函数参数是否传递的情况下有用。 |
| object    | Object类型是所有它的实例的基础，换句话说，Object类型所具有的任何属性和方法也同样存在于更具体的对象中。 |

合法Number类型：

```javaScript
123; // 整数123
0.456; // 浮点数0.456
1.2345e3; // 科学计数法表示1.2345x1000，等同于1234.5
-99; // 负数
NaN; // NaN表示Not a Number，当无法计算结果时用NaN表示，如：0 / 0;
Infinity; // Infinity表示无限大，当数值超过了JavaScript的Number所能表示的最大值时，就表示为Infinity，如：2/0;
```

- 要特别注意相等运算符`==`。JavaScript在设计时，有两种比较运算符：

  第一种是`==`比较，它会自动转换数据类型再比较，很多时候，会得到非常诡异的结果；

  第二种是`===`比较，它不会自动转换数据类型，如果数据类型不一致，返回`false`，如果一致，再比较。

  由于JavaScript这个设计缺陷，*不要*使用`==`比较，始终坚持使用`===`比较。

- 另一个例外是`NaN`这个特殊的Number与所有其他值都不相等，包括它自己。唯一能判断`NaN`的方法是通过`isNaN()`函数：

**数组：**数组是一组按顺序排列的集合，集合的每个值称为元素。

数组表示：

```js
//用`[]`表示，元素之间用`,`分隔。
[1, 2, 3.14, 'Hello', null, true];
//通过Array()函数实现
new Array(1, 2, 3);
//出于代码的可读性考虑，强烈建议直接使用[]

//通过索引来访问数组元素
var arr = [1, 2, 3.14, 'Hello', null, true];
arr[0]; // 返回索引为0的元素，即1
arr[5]; // 返回索引为5的元素，即true
arr[6]; // 索引超出了范围，返回undefined
```

**对象**：JavaScript的对象是一组由键-值组成的无序集合。

- 对象表示：

```js
var person = {
    name: 'Bob',
    age: 20,
    tags: ['js', 'web', 'mobile'],
    city: 'Beijing',
    hasCar: true,
    zipcode: null
};
```

- JavaScript对象的键都是字符串类型，值可以是任意数据类型。上述`person`对象一共定义了6个键值对，其中每个键又称为对象的属性，例如，`person`的`name`属性为`'Bob'`，`zipcode`属性为`null`。

- 要获取一个对象的属性，我们用`对象变量.属性名`的方式:

```js
person.name; // 'Bob'
person.zipcode; // null
```

**变量**：变量的概念基本上和初中代数的方程变量是一致的，只是在计算机程序中，变量不仅可以是数字，还可以是任意数据类型。

- 变量在JavaScript中就是用一个变量名表示，变量名是大小写英文、数字、`$`和`_`的组合，且不能用数字开头。变量名也不能是JavaScript的关键字，如`if`、`while`等。

- 变量申明：

```js
var a; // 申明了变量a，此时a的值为undefined
var $b = 1; // 申明了变量$b，同时给$b赋值，此时$b的值为1
var s_007 = '007'; // s_007是一个字符串
var Answer = true; // Answer是一个布尔值true
var t = null; // t的值是null
```

- 在JavaScript中，使用等号`=`对变量进行赋值。可以把任意数据类型赋值给变量，同一个变量可以反复赋值，而且可以是不同类型的变量，但是要注意只能用`var`申明一次。
- 这种变量本身类型不固定的语言称之为动态语言，与之对应的是静态语言。静态语言在定义变量时必须指定变量类型，如果赋值的时候类型不匹配，就会报错。

```js
//以下代码在JavaScript中为正确
var a = 123; // a的值是整数123
a = 'ABC'; // a变为字符串
```

**strict模式**：

- JavaScript在设计之初，为了方便初学者学习，并不强制要求用`var`申明变量。这个设计错误带来了严重的后果：如果一个变量没有通过`var`申明就被使用，那么该变量就自动被申明为全局变量：

```js
i = 10; // i现在是全局变量
```

- 在同一个页面的不同的JavaScript文件中，如果都不用`var`申明，恰好都使用了变量`i`，将造成变量`i`互相影响，产生难以调试的错误结果。

- 使用`var`申明的变量则不是全局变量，它的范围被限制在该变量被申明的函数体内（函数的概念将稍后讲解），同名变量在不同的函数体内互不冲突。

- 为了修补JavaScript这一严重设计缺陷，ECMA在后续规范中推出了strict模式，在strict模式下运行的JavaScript代码，强制通过`var`申明变量，未使用`var`申明变量就使用的，将导致运行错误。

- 启用strict模式的方法是在JavaScript代码的第一行写上：

```js
'use strict';
```

- 不用`var`申明的变量会被视为全局变量，为了避免这一缺陷，所有的JavaScript代码都应该使用strict模式。

**条件判断**

- JavaScript使用`if () { ... } else { ... }`来进行条件判断。
- JavaScript把`null`、`undefined`、`0`、`NaN`和空字符串`''`视为`false`，其他值一概视为`true`，因此上述代码条件判断的结果是`true`。

**循环**

```js
var x = 0;
var i;
for (i=1; i<=10000; i++) {
    x = x + i;
}
x; // 50005000

//for循环的一个变体是for ... in循环，它可以把一个对象的所有属性依次循环出来
var o = {
    name: 'Jack',
    age: 20,
    city: 'Beijing'
};
for (var key in o) {
    console.log(key); // 'name', 'age', 'city'
}

//for ... in循环可以直接循环出Array的索引
var a = ['A', 'B', 'C'];
for (var i in a) {
    console.log(i); // '0', '1', '2'
    console.log(a[i]); // 'A', 'B', 'C'
}

//while循环
var n = 0;
do {
    n = n + 1;
} while (n < 100);
n; // 100

```



### 2.2 字符串

**转义字符**

- 如果字符串内部既包含`'`又包含`"`可以用转义字符`\`来标识。
- \n`表示换行，`\t`表示制表符，字符`\`本身也要转义，所以`\\`表示的字符就是`\，\x##表示十六进制数，\u####表示一个Unicode符。
- 由于多行字符串用`\n`写起来比较费事，所以最新的ES6标准新增了一种多行字符串的表示方法，用反引号(tab键上键位)表示。

```js
'I\'m \"OK\"!'; // 表示I'm "OK"!

'\x41'; // 完全等同于 'A'

'\u4e2d\u6587'; // 完全等同于 '中文'

console.log(`多行
字符串
测试`); // 多行显示
```

**字符串连接**

- 要把多个字符串连接起来，可以用`+`号连接。
- 如果有很多变量需要连接，用`+`号就比较麻烦。ES6新增了一种模板字符串，表示方法和上面的多行字符串一样，但是它会自动替换字符串中的变量。

```js
var name = '小明';
var age = 20;
var message = '你好, ' + name + ', 你今年' + age + '岁了!'; // 连接字符串

var name = '小明';
var age = 20;
var message = `你好, ${name}, 你今年${age}岁了!`;
```

**字符串操作**

- 获取长度

```js
var s = 'Hello, world!';
s.length; // 13
```

- 获取指定位置字符

```js
var s = 'Hello, world!';
s[0]; // 'H'
s[6]; // ' '
s[7]; // 'w'
s[12]; // '!'
s[13]; // undefined 超出范围的索引不会报错，但一律返回undefined

//需要特别注意的是，字符串是不可变的，如果对字符串的某个索引赋值，不会有任何错误，但是，也没有任何效果。
```

- JavaScript为字符串提供了一些常用方法，注意，调用这些方法本身不会改变原有字符串的内容，而是返回一个新字符串。

  - **toUpperCase**：把一个字符串全部变为大写。

  ```js
  var s = 'Hello';
  s.toUpperCase(); // 返回'HELLO'
  ```

  - **toLowerCase**：把一个字符串全部变为小写。

  ```js
  var s = 'Hello';
  var lower = s.toLowerCase(); // 返回'hello'并赋值给变量lower
  lower; // 'hello'
  ```

  - **indexOf**：会搜索指定字符串出现的位置。

  ```js
  var s = 'hello, world';
  s.indexOf('world'); // 返回7
  s.indexOf('World'); // 没有找到指定的子串，返回-1
  ```

  - **substring**：返回指定索引区间的子串。

  ```js
  var s = 'hello, world'
  s.substring(0, 5); // 从索引0开始到5（不包括5），返回'hello'
  s.substring(7); // 从索引7开始到结束，返回'world'
  ```


### 2.3 数组

**数组长度**

- 要取得`Array`的长度，直接访问`length`属性。

```js
var arr = [1, 2, 3.14, 'Hello', null, true];
arr.length; // 6
```

- 直接给`Array`的`length`赋一个新的值会导致`Array`大小的变化。

```js
var arr = [1, 2, 3];
arr.length; // 3
arr.length = 6;
arr; // arr变为[1, 2, 3, undefined, undefined, undefined]
arr.length = 2;
arr; // arr变为[1, 2]
```

- Array可以通过索引把对应的元素修改为新的值，因此，对Array的索引进行赋值会直接修改这个Array。

```js
var arr = ['A', 'B', 'C'];
arr[1] = 99;
arr; // arr现在变为['A', 99, 'C']
```

- 如果通过索引赋值时，索引超过了范围，同样会引起`Array`大小的变化。

```js
var arr = [1, 2, 3];
arr[5] = 'x';
arr; // arr变为[1, 2, 3, undefined, undefined, 'x']
```

- 大多数其他编程语言不允许直接改变数组的大小，越界访问索引会报错。然而，JavaScript的`Array`却不会有任何错误。在编写代码时，不建议直接修改`Array`的大小，访问索引时要确保索引不会越界。

**indexOf**

- 与String类似，`Array`也可以通过`indexOf()`来搜索一个指定的元素的位置。

```js
var arr = [10, 20, '30', 'xyz'];
arr.indexOf(10); // 元素10的索引为0
arr.indexOf(20); // 元素20的索引为1
arr.indexOf(30); // 元素30没有找到，返回-1
arr.indexOf('30'); // 元素'30'的索引为2
//注意，数字30和字符串'30'是不同的元素。
```

**slice**

- slice()就是对应String的substring()版本，它截取Array的部分元素，然后返回一个新的Array。

- `slice()`的起止参数包括开始索引，不包括结束索引。
- 如果不给`slice()`传递任何参数，它就会从头到尾截取所有元素。利用这一点，我们可以很容易地复制一个`Array`。

```js
var arr = ['A', 'B', 'C', 'D', 'E', 'F', 'G'];
arr.slice(0, 3); // 从索引0开始，到索引3结束，但不包括索引3: ['A', 'B', 'C']
arr.slice(3); // 从索引3开始到结束: ['D', 'E', 'F', 'G']

var arr = ['A', 'B', 'C', 'D', 'E', 'F', 'G'];
var aCopy = arr.slice();
aCopy; // ['A', 'B', 'C', 'D', 'E', 'F', 'G']
aCopy === arr; // false
```

**push和pop**

- `push()`向`Array`的末尾添加若干元素，`pop()`则把`Array`的最后一个元素删除掉：

```js
var arr = [1, 2];
arr.push('A', 'B'); // 返回Array新的长度: 4
arr; // [1, 2, 'A', 'B']
arr.pop(); // pop()返回'B'
arr; // [1, 2, 'A']
arr.pop(); arr.pop(); arr.pop(); // 连续pop 3次
arr; // []
arr.pop(); // 空数组继续pop不会报错，而是返回undefined
arr; // []
```

**unshift和shift**

- `sort()`可以对当前`Array`进行排序，它会直接修改当前`Array`的元素位置，直接调用时，按照默认顺序排序

```js
var arr = ['B', 'C', 'A'];
arr.sort();
arr; // ['A', 'B', 'C']
```

**reverse**

- `reverse()`把整个`Array`的元素给掉个个，也就是反转：

```js
var arr = ['one', 'two', 'three'];
arr.reverse(); 
arr; // ['three', 'two', 'one']
```

**splice**

- `splice()`方法是修改`Array`的“万能方法”，它可以从指定的索引开始删除若干元素，然后再从该位置添加若干元素。

```js
var arr = ['Microsoft', 'Apple', 'Yahoo', 'AOL', 'Excite', 'Oracle'];
// 从索引2开始删除3个元素,然后再添加两个元素:
arr.splice(2, 3, 'Google', 'Facebook'); // 返回删除的元素 ['Yahoo', 'AOL', 'Excite']
arr; // ['Microsoft', 'Apple', 'Google', 'Facebook', 'Oracle']
// 只删除,不添加:
arr.splice(2, 2); // ['Google', 'Facebook']
arr; // ['Microsoft', 'Apple', 'Oracle']
// 只添加,不删除:
arr.splice(2, 0, 'Google', 'Facebook'); // 返回[],因为没有删除任何元素
arr; // ['Microsoft', 'Apple', 'Google', 'Facebook', 'Oracle']
```

**concat**

- `concat()`方法把当前的`Array`和另一个`Array`连接起来，并返回一个新的`Array`.
- 注意，`concat()`方法并没有修改当前`Array`，而是返回了一个新的`Array`。
- 实际上，`concat()`方法可以接收任意个元素和`Array`，并且自动把`Array`拆开，然后全部添加到新的`Array`里

```js
var arr = ['A', 'B', 'C'];
var added = arr.concat([1, 2, 3]);
added; // ['A', 'B', 'C', 1, 2, 3]
arr; // ['A', 'B', 'C']

var arr = ['A', 'B', 'C'];
arr.concat(1, 2, [3, 4]); // ['A', 'B', 'C' 3, 4]
```

**join**

- `join()`方法是一个非常实用的方法，它把当前`Array`的每个元素都用指定的字符串连接起来，然后返回连接后的字符串。

- 如果`Array`的元素不是字符串，将自动转换为字符串后再连接。

```js
var arr = ['A', 'B', 'C', 1, 2, 3];
arr.join('-'); // 'A-B-C-1-2-3'
```

### 2.4 对象

- JavaScript的对象是一种无序的集合数据类型，它由若干键值对组成。

- JavaScript的对象用于描述现实世界中的某个对象。例如，为了描述“小明”这个人，我们可以用若干键值对来描述他：

```js
var xiaoming = {
    name: '小明',
    birth: 1990,
    school: 'No.1 Middle School',
    height: 1.70,
    weight: 65,
    score: null
};

//获取属性
xiaoming.name; // '小明'
xiaoming.birth; // 1990
```

- JavaScript用一个`{...}`表示一个对象，键值对以`xxx: xxx`形式申明，用`,`隔开。注意，最后一个键值对不需要在末尾加`,`，如果加了，有的浏览器（如低版本的IE）将报错。
- 访问属性是通过`.`操作符完成的，但这要求属性名必须是一个有效的变量名。如果属性名包含特殊字符，就必须用`''`括起来。

```js
var xiaohong = {
    name: '小红',
    'middle-school': 'No.1 Middle School'
};

//xiaohong的属性名middle-school不是一个有效的变量，就需要用''括起来。访问这个属性也无法使用.操作符，必须用['xxx']来访问
xiaohong['middle-school']; // 'No.1 Middle School'
xiaohong['name']; // '小红'
xiaohong.name; // '小红'
```

- 也可以用xiaohong['name']来访问xiaohong的name属性，不过xiaohong.name的写法更简洁。我们在编写JavaScript代码的时候，属性名尽量使用标准的变量名，这样就可以直接通过object.prop的形式访问一个属性了。

- JavaScript规定，访问不存在的属性不报错，而是返回`undefined`。
- 由于JavaScript的对象是动态类型，你可以自由地给一个对象添加或删除属性。

```js
var xiaoming = {
    name: '小明'
};
xiaoming.age; // undefined
xiaoming.age = 18; // 新增一个age属性
xiaoming.age; // 18
delete xiaoming.age; // 删除age属性
xiaoming.age; // undefined
delete xiaoming['name']; // 删除name属性
xiaoming.name; // undefined
delete xiaoming.school; // 删除一个不存在的school属性也不会报错
```

- 如果我们要检测`xiaoming`是否拥有某一属性，可以用`in`操作符。

```js
var xiaoming = {
    name: '小明',
    birth: 1990,
    school: 'No.1 Middle School',
    height: 1.70,
    weight: 65,
    score: null
};
'name' in xiaoming; // true
'grade' in xiaoming; // false
```

- 不过要小心，如果`in`判断一个属性存在，这个属性不一定是`xiaoming`的，它可能是`xiaoming`继承得到的。


```js
//因为toString定义在object对象中，而所有对象最终都会在原型链上指向object，所以xiaoming也拥有toString属性。
'toString' in xiaoming; // true
```

- 要判断一个属性是否是`xiaoming`自身拥有的，而不是继承得到的，可以用`hasOwnProperty()`方法

```js
var xiaoming = {
    name: '小明'
};
xiaoming.hasOwnProperty('name'); // true
xiaoming.hasOwnProperty('toString'); // false
```

### 2.5 map和set

`Map`和`Set`都是ES6标准新增的数据类型，需要根据浏览器的支持情况决定是否要使用。

**map**

- `Map`是一组键值对的结构，具有极快的查找速度。
- 初始化`Map`需要一个二维数组，或者直接初始化一个空`Map`。

```js
var m = new Map([['Michael', 95], ['Bob', 75], ['Tracy', 85]]);
m.get('Michael'); // 95

var m = new Map(); // 空Map
m.set('Adam', 67); // 添加新的key-value
m.set('Bob', 59);
m.has('Adam'); // 是否存在key 'Adam': true
m.get('Adam'); // 67
m.delete('Adam'); // 删除key 'Adam'
m.get('Adam'); // undefined
```

- 由于一个key只能对应一个value，所以，多次对一个key放入value，后面的值会把前面的值冲掉。

```js
var m = new Map();
m.set('Adam', 67);
m.set('Adam', 88);
m.get('Adam'); // 88
```

**set**

- `Set`和`Map`类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在`Set`中，没有重复的key。

- 要创建一个`Set`，需要提供一个`Array`作为输入，或者直接创建一个空`Set`。

```js
var s1 = new Set(); // 空Set
var s2 = new Set([1, 2, 3]); // 含1, 2, 3
```

- 重复元素在`Set`中自动被过滤。

```js
var s = new Set([1, 2, 3, 3, '3']);
s; // Set {1, 2, 3, "3"}
```

- 通过`add(key)`方法可以添加元素到`Set`中，可以重复添加，但不会有效果。

```js
s.add(4);
s; // Set {1, 2, 3, 4}
s.add(4);
s; // 仍然是 Set {1, 2, 3, 4}
```

- 通过`delete(key)`方法可以删除元素。

```js
var s = new Set([1, 2, 3]);
s; // Set {1, 2, 3}
s.delete(3);
s; // Set {1, 2}
```

### 2.6 iterable

iterable也是ES6标准引入了新的`iterable`类型，需要根据浏览器的支持情况决定是否要使用。

- 遍历`Array`可以采用下标循环，遍历`Map`和`Set`就无法使用下标。为了统一集合类型，ES6标准引入了新的`iterable`类型，`Array`、`Map`和`Set`都属于`iterable`类型。
- 具有`iterable`类型的集合可以通过新的`for ... of`循环来遍历。

```js
var a = ['A', 'B', 'C'];
var s = new Set(['A', 'B', 'C']);
var m = new Map([[1, 'x'], [2, 'y'], [3, 'z']]);
for (var x of a) { // 遍历Array
    console.log(x);
}
for (var x of s) { // 遍历Set
    console.log(x);
}
for (var x of m) { // 遍历Map
    console.log(x[0] + '=' + x[1]);
}
```

- `for ... in`循环由于历史遗留问题，它遍历的实际上是对象的属性名称。一个`Array`数组实际上也是一个对象，它的每个元素的索引被视为一个属性。

```js
var a = ['A', 'B', 'C'];
a.name = 'Hello';
for (var x in a) {
    console.log(x); // '0', '1', '2', 'name'
}

var a = ['A', 'B', 'C'];
a.name = 'Hello';
for (var x of a) {
    console.log(x); // 'A', 'B', 'C'
}
```

- 使用`iterable`内置的`forEach`方法，它接收一个函数，每次迭代就自动回调该函数。

```js
a.forEach(function (element, index, array) {
    // element: 指向当前元素的值
    // index: 指向当前索引
    // array: 指向Array对象本身
    console.log(element + ', index = ' + index);
});
```

## 3、函数

### 3.1 定义和调用

**函数定义**

- 方法一：
  - `function`指出这是一个函数定义；
  - `abs`是函数的名称；
  - `(x)`括号内列出函数的参数，多个参数以`,`分隔；
  - `{ ... }`之间的代码是函数体，可以包含若干语句，甚至可以没有任何语句。

```js
function abs(x) {
    if (x >= 0) {
        return x;
    } else {
        return -x;
    }
}
```

- ​	方法二：
  - `function (x) { ... }`是一个匿名函数，它没有函数名。但是，这个匿名函数赋值给了变量`abs`，所以，通过变量`abs`就可以调用该函数。
  - 注意这种方式按照完整语法需要在函数体末尾加一个`;`，表示赋值语句结束。	

```js
var abs = function (x) {
    if (x >= 0) {
        return x;
    } else {
        return -x;
    }
};
```

**函数调用**

```js
abs(10); // 返回10
abs(-9); // 返回9
```

- 由于JavaScript允许传入任意个参数而不影响调用，因此传入的参数比定义的参数多也没有问题，虽然函数内部并不需要这些参数：

```js
abs(10, 'blablabla'); // 返回10
abs(-9, 'haha', 'hehe', null); // 返回9
```

- 传入的参数比定义的少也没有问题：

```js
abs(); // 返回NaN
```

- 此时`abs(x)`函数的参数`x`将收到`undefined`，计算结果为`NaN`。

- 要避免收到`undefined`，可以对参数进行检查：

```js
function abs(x) {
    if (typeof x !== 'number') {
        throw 'Not a number';
    }
    if (x >= 0) {
        return x;
    } else {
        return -x;
    }
}
```

**关键字**

- arguments
  - 利用`arguments`，你可以获得调用者传入的所有参数。
  - 即使函数不定义任何参数，还是可以拿到参数的值。

```js
function foo(x) {
    console.log('x = ' + x); // 10
    for (var i=0; i<arguments.length; i++) {
        console.log('arg ' + i + ' = ' + arguments[i]); // 10, 20, 30
    }
}
foo(10, 20, 30);
```

- rest
  - rest参数只能写在最后，前面用`...`标识。
  - 如果传入的参数连正常定义的参数都没填满，也不要紧，rest参数会接收一个空数组（注意不是`undefined`）。
  - rest参数是ES6新标准。

```js
function foo(a, b, ...rest) {
    console.log('a = ' + a);
    console.log('b = ' + b);
    console.log(rest);
}

foo(1, 2, 3, 4, 5);
// 结果:
// a = 1
// b = 2
// Array [ 3, 4, 5 ]

foo(1);
// 结果:
// a = 1
// b = undefined
// Array []
```

### 3.2 变量作用域与解构赋值

**作用域**

- 如果一个变量在函数体内部申明，则该变量的作用域为整个函数体，在函数体外不可引用该变量。

```js
'use strict';

function foo() {
    var x = 1;
    x = x + 1;
}

x = x + 2; // ReferenceError! 无法在函数体外引用变量x
```

- 如果两个不同的函数各自申明了同一个变量，那么该变量只在各自的函数体内起作用。

```js
'use strict';

function foo() {
    var x = 1;
    x = x + 1;
}

function bar() {
    var x = 'A';
    x = x + 'B';
}
```

- JavaScript的函数可以嵌套，此时，内部函数可以访问外部函数定义的变量，反过来则不行。

```js
'use strict';

function foo() {
    var x = 1;
    function bar() {
        var y = x + 1; // bar可以访问foo的变量x!
    }
    var z = y + 1; // ReferenceError! foo不可以访问bar的变量y!
}
```

**变量提升**

- JavaScript的函数定义有个特点，它会先扫描整个函数体的语句，把所有申明的变量“提升”到函数顶部。
- JavaScript引擎会自动提升了变量的声明，但不会提升变量的赋值。

```js
'use strict';

function foo() {
    var x = 'Hello, ' + y;
    console.log(x);
    var y = 'Bob';
}

foo();
```

**全局作用域**

- JavaScript默认有一个全局对象`window`，全局作用域的变量实际上被绑定到`window`的一个属性。

- 不在任何函数内定义的变量就具有全局作用域。

```js
'use strict';

var course = 'Learn JavaScript';
alert(course); // 'Learn JavaScript'
alert(window.course); // 'Learn JavaScript'
```

- 以变量方式`var foo = function () {}`定义的函数实际上也是一个全局变量。

```js
'use strict';

function foo() {
    alert('foo');
}

foo(); // 直接调用foo()
window.foo(); // 通过window.foo()调用
```

**局部作用域**

- JavaScript的变量作用域实际上是函数内部，我们在`for`循环等语句块中是无法定义具有局部作用域的变量的。

```js
'use strict';

function foo() {
    for (var i=0; i<100; i++) {
        //
    }
    i += 100; // 仍然可以引用变量i
}
```

- 为了解决块级作用域，ES6引入了新的关键字`let`，用`let`替代`var`可以申明一个块级作用域的变量

```js
'use strict';

function foo() {
    var sum = 0;
    for (let i=0; i<100; i++) {
        sum += i;
    }
    // SyntaxError:
    i += 1;
}
```

**常量**

- ES6标准引入了新的关键字`const`来定义常量，`const`与`let`都具有块级作用域。

```js
'use strict';

const PI = 3.14;
PI = 3; // 某些浏览器不报错，但是无效果！
PI; // 3.14
```

**解构赋值**

- 从ES6开始，JavaScript引入了解构赋值，可以同时对一组变量进行赋值。

```js
'use strict';

// 如果浏览器支持解构赋值就不会报错:
var [x, y, z] = ['hello', 'JavaScript', 'ES6'];
```

- 如果数组本身还有嵌套，也可以通过下面的形式进行解构赋值，注意嵌套层次和位置要保持一致

```js
let [x, [y, z]] = ['hello', ['JavaScript', 'ES6']];
x; // 'hello'
y; // 'JavaScript'
z; // 'ES6'
```

- 解构赋值还可以忽略某些元素。

```js
let [, , z] = ['hello', 'JavaScript', 'ES6']; // 忽略前两个元素，只对z赋值第三个元素
z; // 'ES6'
```

- 如果需要从一个对象中取出若干属性，也可以使用解构赋值，便于快速获取对象的指定属性。

```js
'use strict';

var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678',
    school: 'No.4 middle school'
};
var {name, age, passport} = person;
```

- 对一个对象进行解构赋值时，同样可以直接对嵌套的对象属性进行赋值，只要保证对应的层次是一致的。

```js
var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678',
    school: 'No.4 middle school',
    address: {
        city: 'Beijing',
        street: 'No.1 Road',
        zipcode: '100001'
    }
};
var {name, address: {city, zip}} = person;
name; // '小明'
city; // 'Beijing'
zip; // undefined, 因为属性名是zipcode而不是zip
// 注意: address不是变量，而是为了让city和zip获得嵌套的address对象的属性:
address; // Uncaught ReferenceError: address is not defined
```

- 使用解构赋值对对象属性进行赋值时，如果对应的属性不存在，变量将被赋值为`undefined`，这和引用一个不存在的属性获得`undefined`是一致的。如果要使用的变量名和属性名不一致，可以用下面的语法获取。

```js
var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678',
    school: 'No.4 middle school'
};

// 把passport属性赋值给变量id:
let {name, passport:id} = person;
name; // '小明'
id; // 'G-12345678'
// 注意: passport不是变量，而是为了让变量id获得passport属性:
passport; // Uncaught ReferenceError: passport is not defined
```

- 解构赋值还可以使用默认值，这样就避免了不存在的属性返回`undefined`的问题。

```js
var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678'
};

// 如果person对象没有single属性，默认赋值为true:
var {name, single=true} = person;
name; // '小明'
single; // true
```

- 有些时候，如果变量已经被声明了，再次赋值的时候，正确的写法也会报语法错误。这是因为JavaScript引擎把`{`开头的语句当作了块处理，于是`=`不再合法。解决方法是用小括号括起来。

```js
// 声明变量:
var x, y;
// 解构赋值:
{x, y} = { name: '小明', x: 100, y: 200};
// 语法错误: Uncaught SyntaxError: Unexpected token =

({x, y} = { name: '小明', x: 100, y: 200});
```

