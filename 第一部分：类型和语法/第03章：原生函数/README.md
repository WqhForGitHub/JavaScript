# 3.1 内部属性 `[[class]]`

所有typeof 返回值为 "object" 的对象（如数组）都包含一个内部属性 `[[Class]]`（我们可以把它看作一个内部的分类，而非传统的面向对象意义上的类）。整个属性无法直接访问，一般通过 `Object.prototype.toString(..)` 来查看。例如：
```javascript
Object.prototype.toString.call([1, 2, 3]);
// "[object Array]"

Object.prototype.toString.call(/regex-literal/i);
// "[object RegExp]"
```
上例中，数组的内部 `[[Class]]` 属性值是 "Array"，正则表达式的值是 "RegExp"。多数情况下，对象的内部 `[[Class]]` 属性和创建该对象的内建原生构造函数相对应（如下），但并非总是如此。

那么基本类型值呢？下面先来看看 null 和 undefined：
```javascript
Object.prototype.toString.call(null);
// "[object Null]"

Object.prototype.toString.call(undefined);
// "[object Undefined]"
```
虽然 Null() 和 Undefined() 这样的原生构造函数并不存在，但是内部 `[[Class]]` 属性值仍然是 "Null" 和 "Undefined"。

其他基本类型值（如字符串、数字和布尔）的情况有所不同，通常被称为”包装“（boxing，参见 3.2 节）：
```javascript
Object.prototype.toString.call("abc");
// "[object String]"

Object.prototype.toString.call(42);
// "[object Number]"

Object.prototype.toString.call(true);
// "[object Boolean]"
```
上例中基本类型值被各自的封装对象自动包装，所有它们的内部 `[[Class]]` 属性值分别为 "String"、"Number" 和 "Boolean"。
>从 ES5 到 ES6，toString() 和 `[[Class]]` 的行为发生了一些变化，详情见本系列的《你不知道的 JavaScript（下卷）》的 ”ES6 & Beyond“ 部分。

# 3.2 封装对象包装

封装对象（object wrapper）扮演着十分重要的角色。由于基本类型值没有 .length 和 .toString() 这样的属性和方法，需要通过封装对象才能访问，此时 JavaScript 会自动为基本类型值包装（box 或者 wrap）一个封装对象：
```javascript
var a = "abc";

a.length; // 3
a.toUpperCase(); // "ABC"
```
如果需要经常用到这些字符串属性和方法，比如在 for 循环中使用 i < a.length，那么从一开始就创建一个封装对象也许更为方便，这样 JavaScript 引擎就不用每次都自动创建了。

但实际证明这并不是一个好办法，因为浏览器已经为 .length 这样的常见情况做了性能优化，直接使用封装对象来”提前优化”代码反而会降低执行效率。

一般情况下，我们不需要直接使用封装对象。最好的办法是让 JavaScript 引擎自己决定什么时候应该使用封装对象。换句话说，就是应该优先考虑使用 "abc" 和 42 这样的基本类型值，而非 new String("abc")
## 3.2.1 封装对象释疑

使用封装对象时有些地方需要特别注意。

比如 Boolean：
```javascript
var a = new Boolean(false);

if (!a) {
	console.log("Oops"); // 执行不到这里
}
```
我们为 false 创建了一个封装对象，然而该对象是真值（"truthy"，即总是返回 true，参见第 4 章），所以这里使用封装对象得到的结果和使用 false 截然相反。

如果想要自行封装基本类型值，可以使用 `Object(..)` 函数（不带 new 关键字）：
```javascript
var a = "abc";
var b = new String(a);
var c = Object(a);

typeof a; // "string"
typeof b; // "object"
typeof c; // "object"

b instanceof String; // true
c instanceof String; // true

Object.prototype.toString.call(b); // "[object String]"
Object.prototype.toString.call(c); // "[object String]"
```
再次强调，一般不推荐直接使用封装对象（如上例中的 b 和 c），但它们偶尔也会派上用场。
# 3.3 拆封

如果想要得到封装对象中的基本类型值，可以使用 valueOf() 函数：
```javascript
var a = new String("abc");
var b = new Number(42);
var c = new Boolean(true);

a.valueOf(); // "abc"
b.valueOf(); // 42
c.valueOf(); // true
```
在需要用到封装对象中的基本类型值的地方会发生隐式拆封。具体过程（即强制类型转换）将在第 4 章详细介绍。
```javascript
var a = new String("abc");
var b = a + ""; // b 的值为 "abc"

typeof a; // "object"
typeof b; // "string"
```















