# 4.1 值类型转换

将值从一种类型转换为另一种类型通常称为类型转换（type casting），这是显式的情况，隐式的情况称为强制类型转换（coercion）。
>JavaScript 中的强制类型转换总是返回标量基本类型值（参见第 2 章），如字符串、数字和布尔值，不会返回对象和函数，在第 3 章中，我们介绍过“封装”，就是为标量基本类型值封装一个相应类型的对象，但这并非严格意义上的强制类型转换。

也可以这样来区分：类型转换发生在静态类型语言的编译阶段，而强制类型转换则发生在动态类型语言的运行时（runtime）。

然而在 JavaScript 中通常将它们统称为强制类型转换，我个人则倾向于用“隐式强制类型转换”（implicit coercion）和”显式强制类型转换“（explicit coercion）来区分。

二者的区别显而易见，我们能够从代码中看出哪些地方是显式强制类型转换，而隐式强制类型转换则不那么明显，通常是某些操作产生的副作用。

例如：
```javascript
var a = 42;

var b = a + ""; // 隐式强制类型转换

var c = String(a); // 显式强制类型转换
```
对变量 b 而言，强制类型转换是隐式的，由于 + 运算符的其中一个操作数是字符串，所以是字符串拼接操作，结果是数字 42 被强制类型转换为相应的字符串 “42”。

而 String(..) 则是将 a 显式强制类型转换为字符串。

两者都是将数字 42 转换为字符串 “42”。然而它们各自不同的处理方式成为了争论的焦点。
>从技术角度来说，除了字面上的差别以外，二者在行为特征上也有一些细微的差别。我们将在 4.4.2 节详细介绍。

这里的“显式”和“隐式”以及“明显的副作用”和“隐藏的副作用”，都是相对而言的。

要是你明白 a + "" 是怎么回事，它对你来说就是“显式”的。相反，如果你不知道 String(..) 可以用来做字符串强制类型转换，它对你来说可能就是“隐式”的。

我们在这里以普遍通行的标准来讨论“显式“和”隐式“，而非 JavaScript 专家和规范的标准。如果你的理解与此有出入，请参照我们的标准。

要知道我们编写的代码大都是给别人看的。即便是 JavaScript 高手也需要顾及其他不同水平的开发人员，要考虑他们是否能读懂自己的代码，以及他们对于”显式“和”隐式“的理解是否和自己一致。
# 4.2 抽象值操作

介绍显式和隐式强制类型转换之前，我们需要掌握字符串、数字和布尔值之间类型转换的基本规则。ES5 规范第 9 节中定义了一些”抽象操作“（即”仅供内部使用的操作“）和转换规则。这里我们着重介绍 ToString、ToNumber 和 ToBoolean，附带讲一讲 ToPrimitive。
## 4.2.1 ToString

规范的 9.8 节中定义了抽象操作 ToString，它负责处理非字符串到字符串的强制类型转换。

基本类型值的字符串化规则为：null 转换为 "null"，undefined 转换为 "undefined"，true 转换为 "true"。数字的字符串化则遵循通用规则，不过第 2 章中讲过的那些极小和极大的数字使用指数形式：
```javascript
// 1.07 连续乘以七个 1000
var a = 1.07 * 1000 * 1000 * 1000 * 1000 * 1000 * 1000 * 1000;

// 七个 1000 一共 21 位数字
a.toString(); // "1.07e21"
```
对普通对象来说，除非自行定义，否则 toString()（Object.prototype.toString()）返回内部属性`[[Class]]]` 的值（参见第 3 章），如 `"[object Object]"`。

然而前面我们介绍过，如果对象有自己的 toString() 方法，字符串化时就会调用该方法并使用其返回值。
>将对象强制类型转换为 string 是通过 ToOPrimitive 抽象操作来完成的（ES5 规范，9.1 节），我们在此略过，稍后将在 4.2.2 节中详细介绍。

数组的默认 toString() 方法经过了重新定义，将所有单元字符串化以后再用","连接起来：
```javascript
var a = [1,2,3];

a.toString(); // "1,2,3"
```
toString() 可以被显式调用，或者在需要字符串化时自动调用。
### JSON 字符串化

工具函数 JSON.stringify(..) 在将 JSON 对象序列化为字符串时也用到了 ToString。

请注意，JSON 字符串化并非严格意义上的强制类型转换，因为其中也涉及 ToString 的相关规则，所以这里顺带介绍一下。

对大多数简单值来说，JSON 字符串化和 toString() 的效果基本相同，只不过序列化的结果总是字符串：
```javascript
JSON.stringify(42); // "42"
JSON.stringify("42"); // ""42""（含有双引号的字符串）
JSON.stringify(null); // "null"
JSON.stringify(true); // "true"
```
所有安全的 JSON 值（JSON-safe）都可以使用 JSON.stringify(..) 字符串比。安全的 JSON 值是指能够呈现为有效 JSON 格式的值。

为了简单起见，我们来看看设么是不安全的 JSON 值。undefined、function、symbol（ES6+）和包含循环引用（对象之间相互引用，形成一个无限循环）的对象都不符合 JSON 结构标准，其他支持 JSON 的语言无法处理它们。

JSON.stringify(..) 在对象中遇到 undefined、function 和 symbol 时会自动将其忽略，在数组中则会返回 null（以保证单元位置不变）。

例如：
```javascript
JSON.stringify(undefined); // undefined
JSON.stringify(function() {}); // undefined

JSON.stringify([1, undefined, function() {}, 4]); // "[1, null, null, 4]"

JSON.stringify({ a:2, b: function() {} }); // "{"a": 2}"
```
对包含循环引用的对象执行 JSON.stringify(..) 会出错。

如果对象中定义了 toJSON() 方法，JSON 字符串化时会首先调用该方法，然后用它的返回值来进行序列化。

如果要对含有非法 JSON 值的对象做字符串化，或者对象中的某些值无法被序列化时，就需要定义 toJSON() 方法来返回一个安全的 JSON 值。

例如：
```javascript
var o = { };

var a = {
	b: 42,
	c: o,
	d: function() {}
};

// 在 a 中创建一个循环引用
o.e = a;

// 循环引用在这里会产生错误
// JSON.stringify(a);

// 自定义的 JSON 序列化
a.toJSON = function() {
	// 序列化仅包含 b
	return { b: this.b };
};

JSON.stringify(a); // "{"b": 42}"
```
很多人误以为 toJSON() 返回的是 JSON 字符串化后的值，其实不然，除非我们确实想要对字符串进行字符串化（通常不会）。toJSON() 返回的应该是一个适当的值，可以是任何类型，然后由 JSON,stringify(..) 对其进行字符串化。

也就是说，toJSON() 应该”返回一个能够被字符串化的安全的 JSON 值“，而不是”返回一个 JSON 字符串“。
例如：
```javascript
var a = {
	val: [1,2,3],
	
	// 可能是我们想要的结果
	toJSON: function() {
		return this.val.slice(1)
	}
};

var b = {
	val: [1,2,3],
	
	// 可能不是我们想要的结果
	toJSON: function() {
		return "[" + this.val.slice(1).join() + "]"
	}
}

JSON.stringify(a); // "[2, 3]"

JSON.stringify(b); // ""[2, 3]""
```
这里第二个函数是对 JSON 返回的字符串做字符串化，而非数组本身。

现在介绍几个不太为人所知但却非常有用的功能。

我们可以向 JSON.stringify(..) 传递一个可选参数 replacer，它可以是数组或者函数，用来指定对象序列化过程中哪些属性应该被处理，哪些应该被排除，和 toJSON() 很像。

如果 replacer 是一个数组，那么它必须是一个字符串数组，其中包含序列化要处理的对象的属性名称，除此之外其他的属性则被忽略。

如果 replacer 是一个函数，它会对对象本身调用一次，然后对对象中的每个属性各调用一次，每次传递两个参数，键和值。如果要忽略某个键就返回 undefined，否则返回指定的值。
```javascript
var a = {
	b: 42,
	c: "42",
	d: [1,2,3]
};

JSON.stringify(a, ["b", "c"]); // "{"b": 42, "c": "42"}"

JSON.stringify(a, function(k, v) {
	if (k !== "c") return v;
});
// "{"b": 42, "d": [1,2,3]}"
```
>如果 replacer 是函数，它的参数 k 在第一次调用时为 undefined（就是对对象本身调用的那次）。if 语句将属性 "c" 排除掉。由于字符串化是递归的，因此数组 [1,2,3] 中的每个元素都会通过参数 v 传递给 replacer，即 1、2 和 3，参数 k 是它们的索引值，即 0、1 和 2。

JSON.stringify 还有一个可选参数 space，用来指定输出的缩进格式，space 为正整数时是指定每一级缩进的字符数，它还可以是字符串，此时最前面的十个字符被用于每一级的缩进：
```javascript
var a = {
	b: 42,
	c: "42",
	d: [1,2,3]
};

JSON.stringify(a, null, 3);
"{
   "b": 42,
   "c": "42",
   "d": [
      1,
      2,
      3
   ]
}"

JSON.stringify(a, null, "-----");
"{
-----"b": 42,
-----"c": "42",
-----"d": [
----------1,
----------2,
----------3
-----]
}"
```













  






