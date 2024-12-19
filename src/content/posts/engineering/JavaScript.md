---

title: JavaScript

published: 2024-10-24

description: 'rt'

image: ''

tags: [tutorial]

category: 'engineering'

draft: false 

---

let 创建一个在块内的变量 var 创建全局变量
用一个字符串加上一个数字（或其他值），那么操作数都会被首先转换为字符串
`===`在比较时不进行类型转换 for of for in

```
function Person(name, age) {
  this.name = name;
  this.age = age;
}

// 定义一个对象
var You = new Person("You", 24);
// 我们创建了一个新的 Person，名称是 "You"
// ("You" 是第一个参数，24 是第二个参数..)

可通过
obj.name 或[name]访问
```

```
var a = ["dog", "cat", "hen"];
a[100] = "fox";
a.length; // 101
```

```
function avg(...args) {
  var sum = 0;
  for (let value of args) {
    sum += value;
  }
  return sum / args.length;
}

avg(2, 3, 4, 5); // 3.5
avg.apply(null, [2, 3, 4, 5]); // 3.5

```

---

```
var charsInBody = (function counter(elm) {
  if (elm.nodeType == 3) {
    // 文本节点
    return elm.nodeValue.length;
  }
  var count = 0;
  for (var i = 0, child; (child = elm.childNodes[i]); i++) {
    count += counter(child);
  }
  return count;
})(document.body);

```

[[Pasted image 20231004164704.png]]
`Person.prototype` 是一个可以被 `Person` 的所有实例共享的对象。它是一个名叫原型链（prototype
chain）的查询链的一部分：当你试图访问 `Person` 某个实例（例如上个例子中的
s）一个没有定义的属性时，解释器会首先检查这个 `Person.prototype` 来判断是否存在这样一个属性。所以，任何分配给 `Person.prototype` 的东西对通过 `this` 对象构造的实例都是可用的。

这个特性功能十分强大，JavaScript
允许你在程序中的任何时候修改原型（prototype）中的一些东西，也就是说你可以在运行时
(runtime) 给已存在的对象添加额外的方法：

JSCopy to Clipboard

```
s = new Person("Simon", "Willison");
s.firstNameCaps(); // TypeError on line 1: s.firstNameCaps is not a function

Person.prototype.firstNameCaps = function () {
  return this.first.toUpperCase();
};
s.firstNameCaps(); // SIMON
```

alert弹出 document.write(“shuchu”) console.log(‘控制台打印输出’)