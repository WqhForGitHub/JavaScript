
在第 1 章和第 2 章中，我们介绍了函数调用位置的不同会造成 this 绑定对象的不同。但是对象到底是什么，为什么我们需要绑定它们呢？本章会详细介绍对象。
# 3.1 语法

对象可以通过两种形式定义：声明（文字）形式和构造形式。

对象的文字语法大概是这样：
```javascript
var myObject = {
	key: value
	// ...
};
```
构造形式大概是这样：
```javascript
var myObj = new Object();
myObj.key = value;
```
构造形式和文字形式生成的对象是一样的。唯一的区别是，在文字声明中你可以添加多个键/值对，但是在构造形式中你必须逐个添加属性。
>用上面的构造形式来创建对象是非常少见的，一般来说你会使用文字语法，绝大多数内置对象也是这样做的（稍后解释）。

# 3.2 类型

对象是 JavaScript 的基础。在 JavaScript 中一共有六种主要类型（术语是语言类型）：
- string
- number
- boolean
- null
- undefined
- object

注意，简单基本类型（string、boolean、number、null 和 undefined）本身并不是对象。null 有时胡被当作一种对象类型，但是这其实只是语言本身的一个 bug，即对 null 指定 typeof null  时会返回字符串 "object"。实际上，null 本身是基本类型。

有一种常见的错误说法是JavaScript 中万物皆是对象，这显然是错误的。

实际上，JavaScript 中有许多特殊的对象子类型，我们可以称之为复杂基本类型。

函数就是对象的一个子类型（从技术角度来说就是可调用的对象）。JavaScript 中的函数是一等公民，因为它们本质上和普通的对象一样（只是可以调用），所以可以像操作其他对象一样操作函数（比如当作另一个函数的参数）。

数组也是对象的一种类型，具备一些额外的行为。数组中内容的组织方式比一般的对象要稍微复杂一些。
## 内置对象

JavaScript 中还有一些对象子类型，通常被称为内置对象。有些内置对象的名字看起来和简单基础类型一样，不过实际上它们的关系更复杂，我们稍后会详细介绍。
- String
- Number
- Boolean
- Object
- Function
- Array
- Date
- RegExp
- Error

这些内置对象从表现形式来说很像其他语言中的类型（type）或者类（class），比如 Java 中的 String 类。

但是在 JavaScript 中，它们实际上只是一些内置函数。这些内置函数可以当作构造函数（由 new 产生的函数调用，参见第 2 章）来使用，从而可以构造一个对应子类型的新对象。举例来说：
```javascript
var strPrimitive = "I am a string";
typeof strPrimitive; // "string"
strPrimitive instanceof String; // false

var strObject = new String("I am a string");
typeof strObject; // "object"
strObject instanceof String; // true

// 检查 sub-type 对象
Object.prototyoe.toString.call(strObject); // [object String]
```
在之后的章节中我们会详细介绍 Object.prototype.toString... 是如何工作的，不过简单来说，我们可以认为子类型在内部借用了 Object 中的 toString() 方法。从代码中可以看到，strOject 是由 String 构造函数创建的一个对象。

原始值 "I am a string" 并不是一个对象，它只是一个字面量，并且是一个不可变的值。如果要在这个字面量上执行一些操作，比如获取长度、访问其中某个字符等，那需要将其转换为 String 对象。

幸好，在必要时语言会自动把字符串字面量转换成一个 String 对象，也就是说你并不需要显式创建一个对象。JavaScript 社区中的大多数人都认为能使用文字形式时就不要使用构造形式。

思考下面的代码：
```javascript
var strPrimitive = "I am a string";

console.log(strPrimitive.length); // 13
console.log(strPrimitive.charAt(3)); // "m"
```

使用以上两种方法，我们都可以直接在字符串字面量上访问属性或者方法，之所以可以这样做，是因为引擎自动把字面量转换成 String 对象，所以可以访问属性和方法。

同样的事也会发生在数值字面量上，如果使用类似 42.359.toFixed(2) 的方法，引擎会把 42 转换成 new Number(42)。对于布尔字面量来说也是如此。

null 和 undefined 没有对应的构造形式，它们只有文字形式。相反，Date 只有构造，没有文字形式。

对于 Object、Array、Function 和 RegExp（正则表达式）来说，无论使用文字形式还是构造形式，它们都是对象，不是字面量。在某些情况下，相比用文字形式创建对象，构造形式可以提供一些额外选项。由于这两种形式都可以创建对象，所以我们首选更简单的文字形式。建议只在需要那些额外选项时使用构造形式。
# 3.3 内容

之前我们提到过，对象的内部是由一些存储在特定命名位置的（任意类型的）值组成的，我们称之为属性。
需要强调的一点是，当我们说内容时，似乎在暗示这些值实际上被存储在对象内部，但是这只是它的表现形式。在引擎内部，这些值的存储方式是多种多样的，一般并不会存在对象容器内部。存储在对象容器内部的是这些属性的名称，它们就像指针（从技术角度来说就是引用）一样，指向这些值真正的存储位置。

思考下面的代码：
```javascript
var myObject = {
	a: 2
};

myObject.a; // 2

myObject["a"]; // 2
```
如果要访问 myObject 中 a 位置上的值，我们需要使用 . 操作符或者 [] 操作符。.a 语法通常被称为“属性访问”，["a"] 语法通常被称为键访问。实际上它们访问的是同一个位置，并且会返回相同的值 2，所以这两个术语是可以互换的。在本书中我们会使用最常见的术语“属性访问”。

这两种语法的主要区别在于 . 操作符要求属性名满足标识符的命名规范，而 [".."] 语法可以接受任意 UTF-8/Unicode 字符串作为属性名。举例来说，如果要引用名称为 "Super-Fun!" 的属性，那就必须使用 ["Super-Fun!"] 语法访问，因为 Super-Fun! 并不是一个有效的标识符属性名。

此外，由于 [".."] 语法使用字符串来访问属性，所以可以在程序中构造这个字符串，比如说：
```javascript
var myObject = {
	a: 2
};

var idx;

if (wantA) {
	idx = "a";
}

// 之后

console.log(myObject[idx]); // 2
```
在对象中，属性名永远都是字符串。如果你使用 string（字面量）以外的其他值作为属性名，那它首先会被转换为一个字符串。即使是数字也不例外，虽然在数组下标中使用的的确是数字，但是在对象属性名中数字会被转换成字符串，所以当心不要搞混对象和数组中年数字的用法：
```javascript
var myObject = { };

myObject[true] = "foo";
myObject[3] = "bar";
myObject[myObject] = "baz";

myObject["true"]; // "foo"
myObject["3"]; // "bar"
myObject["[object Object]"]; // "baz"
```
## 3.3.1 可计算属性名

如果你需要通过表达式来计算属性名，那么我们刚刚讲到的 myObject[..] 这种属性访问语法就可以排上用场了，如可以使用 myObject[prefix + name]。但是使用文字形式来声明对象时这样做是不行的。

ES6 增加了可计算属性名，可以在文字形式中使用 [] 包裹一个表达式来当作属性名。
```javascript
var prefix = "foo";

var myObject = {
	[prefix + "bar"]: "hello",
	[prefix + "baz"]: "world"
};

myObject["foobar"]; // hello
myObject["foobaz"]; // world
```

可计算属性名最常用的场可能是 ES6 的符号（Symbol），本书时不作详细介绍。不过简单来说，它们是一种新的基础数据类型，包含一个不透明且语法预测的值（从技术角度来说就是一个字符串）。一般来说你不会用到符号的实际值（因为理论上来说在不同的 JavaScript 引擎中值是不同的），所以通常你接触到的是符号的名称，比如 Symbol.Something（这个名字是我编的）：
```javascript
var myObject = {
	[Symbol.Something]: "hello world"
}
```
## 3.3.2 属性与方法

如果访问的对象属性是一个函数，有些开发者喜欢使用不一样的叫法以作区分。由于函数很容易被认为是属于某个对象，在其他语言中，属于对象（也被称为“类”）的函数通常被称为“方法“，因此把”属性访问“说成是”方法访问“也就不奇怪了。

有意思的是，JavaScript 的语法规范也做出了同样的区分、

从技术角度来说，函数永远不会属于一个对象，所以把对象内部引用的函数称为”方法“似乎有点不妥。

确实，有些函数具有 this 引用，有时候这些 this 确实会指向调用位置的对象引用。但是这种用法从本质上来说并没有把一个函数变成一个”方法“，因为 this 是在运行时根据调用位置动态绑定的，所以函数和对象的关系最多也只能说是间接关系。

无论返回值是什么类型，每次访问对象的属性欧就是属性访问。如果属性访问返回的是一个函数，那它也并不是一个”方法“。属性访问返回的哈数和其他函数没有任何区别（除了可能发生的隐式绑定 this，就像我们刚才提到的）。

举例来说：
```javascript
function foo() {
	console.log("foo");
}

var someFoo = foo; // 对 foo 的变量引用

var myObject = {
	someFoo: foo
};

foo; // function foo() {..}
someFoo; // function foo() {..}
myObject.someFoo; // function foo() {..}
```
someFoo 和 myObject.someFoo 只是对于同一个函数的不同引用，并不能说明这个函数是特别的或者“属于”某个对象。如果 foo() 定义时在内部有一个 this 引用，那这两个函数引用的唯一区别就是 myObject.someFoo 中的 this 会被隐式绑定到一个对象。无论哪种引用形式都不能称之为“方法”。

或许有人会辩解说，函数并不是在定义时成为方法，而是在被调用时根据调用位置的不同（是否具有上下文对象，详见第 2 章）成为方法。即便如此，这种说法仍然有些不妥。

最保险的说法可能是，“函数”和“方法”在 JavaScript 中是可以互换的。
>ES6 增加了 super 引用，一般来说会被用在 class 中（参见附录 A）。super 的行为似乎更有理由吧 super 绑定的函数称为“方法”。但是再说一次，这些只是一些语义（和技术）上的微妙差别，本质上是一样的。

即使你在对象的文字形式中声明一个函数表达式，这个函数也不会“属于”这个对象，它们只是对于相同函数对象的多个引用。
```javascript
var myObject = {
	foo: function() {
		console.log("foo");
	}
};

var someFoo = myObject.foo;

someFoo; // function foo() {..}
myObject.foo; // function foo() {..}
```
>第 6 章会介绍本例对象的文字形式中声明函数的语法，这是 ES6 增加的一种简易函数声明语法。

## 3.3.3 数组

数组也支持 [] 访问形式，不过就像我们之前提到过的，数组有一套更加结构化的值存储机制（不过仍然不限制值的类型。）数组期望的是数值下标，也就是说值存储的位置（通常被称为索引）是非负整数，比如说 0 和 42：
```javascript
var myArray = ["foo", 42, "bar"];

myArray.length; // 3

myArray[0]; // "foo"

myArray[2]; // "bar"
```
数组也是对象，所以虽然每个下标都是整数，你仍然可以给数组添加属性：
```javascript
var myArray = ["foo", 42, "bar"];

myArray.baz = "baz";

myArray.length; // 3

myArray.baz = "baz"; // "baz"
```
可以看到虽然添加了命名属性（无论是通过，语法还是 [] 语法），数组的 length 值并未发生变化。

你完全可以把数组当作一个普通的键/值对象来使用，并且不添加任何数值索引，但是这并不是一个好主意。数组和普通的对象都根据其对应的行为和用途进行了优化，所以最好只用对象来存储键/值对，只用数组来存储数值下标/值对。

注意：如果你试图向数组添加一个属性，但是属性名”看起来“像一个数字，那它会变成一个数值下标（因此会修改数组的内容而不是添加一个属性）：
```javascript
var myArray = ["foo", 42, "bar"];

myArray["3"] = "baz";

myArray.length; // 4

myArray[3]; // "baz"
```
## 3.3.4 复制对象

JavaScript 初学者最常见的问题之一就是如何复制一个对象。看起来应该有一个内置的 coy() 方法，是吧？实际上事情比你想象的更复杂，因为我们无法选择一个默认的复制算法。

举例来说，思考一下这个对象：
```javascript
function anotherFunction() { /*..*/ }

var anotherObject = {
	c: true
};

var anotherArray = [];

var myObject = {
	a: 2,
	b: anotherObject, // 引用，不是复本
	c: anotherArray, // 另一个引用
	d: anotherFunction
};

anotherArray.push(anotherObject, myObject);
```
如何准确地表示 myObject 的复制呢？

首先，我们应该判断它是浅复制还是深复制。对于浅拷贝来说，复制出的新对象中 a 的值会复制旧对象中 a 的值，也就是 2，但是新对象中 b、c、d 三个属性其实只是三个引用，它们和旧对象总 被b、c、d 引用的对象是一样的。对于深复制来说，除了复制 myObject 以外还会复制 anotherObject 和 anotherArray。这时问题就来了，anotherArray 引用了 anotherObject 个 myObject，所以又需要复制 myObject，这样就会由于循环引用导致死循环。

我们是应该检测循环引用并终止循环（不复制深层元素）？还是应当直接报错后者是选择其他方法？

除此之外，我们还不确定”复制“一个函数意味着什么。有些人会通过 toString() 来序列化一个函数的源代码（但是结果取决于 JavaScript 的具体实现，而且不同的引擎对于不同类型的函数处理方式并不完全相同）。

那么如何解决这些棘手问题呢？许多 JavaScript 框架都提出了自己的解决方法，但是 JavaScript 应当采用哪种方法作为标准呢？在很长一段时间里，这个问题都没有明确的答案。

对于 JSON 安全（也就是说可以被序列化一个 JSON 字符串并且可以根据这个字符串解析出一个结构和值完全一样的对象）的对象来说，有一种巧妙的复制方法：
```javascript
var newObj = JSON.parse(JSON.stringify(someObj));
```
当然，这种方法需要保证对象是 JSON 安全的，所以只适用于部分情况。。

相比深复制，浅复制非常易懂并且问题要少得多，所以 ES6 定义了 Object.assign(...) 方法来实现浅复制。Object..assign(..) 方法的第一个参数是目标对象，之后还可以跟一个或多个源对象。它与遍历一个或多个源对象的所有可枚举（enumerable，参见下面的代码）的自有键（own key，很快会介绍）并把它们复制（使用 = 操作符赋值）到目标对象，最后返回目标对象，就像这样：
```javascript
var newObj = Object.assign({}, myObject);

newObj.a; // 2
newObj.b === anotherObject; // true
newObj.c === anotherArray; // true
newObj.d === anotherFunction; // true
```
>下一节会介绍”属性描述符“以及 Object.defineProperty(..) 用法。但是需要注意一点是，由于 Object.assign(..) 就是使用 = 操作符来赋值，所以源对象属性的一些特性（比如 writable）不会被复制到目标对象。

## 3.3.5 属性描述符

在 ES5 之前，JavaScript 语言本身并没有直接检测属性特性的方法，比如判断属性是否是只读。

但是从 ES5 开始，所有的属性都具备了属性描述符。

思考下面的代码：
```javascript
var myObject = {
	a: 2
};

Object.getOwnPropertyDescriptor(myObject, "a");
//{
//	value: 2,
//	writable: true,
//	enumerable: true,
//	configurable: true
//}
```
如你所见，这个普通的对象属性对应的属性描述符（也被称为“数据描述符”，因为它只保存一个数据值）可不仅仅只是一个 2。它还包含另外三个特性：writable（可写）、enumerable（可枚举）、和configurable（可配置）。

在创建普通属性时属性描述符会使用默认值，我们也可以使用 Object.defineProperty(..)来添加一个新属性或者修改一个已有属性（如果它是 configurable）并对特性进行设置。

举例来说：
```javascript
var myObject = {};

Object.defineProperty(myObject, "a", {
	value: 2,
	writable: true,
	configurable: true,
	enumerable: true
});

myObject.a; // 2
```
我们使用 defineProperty(..) 给 myObject 添加了一个普通的属性并显式指定了一些特定。然而，一般来说你不会使用这种方式，除非你想修改属性描述符。
### 1. Writable

writable 决定是否可以修改属性的值。

思考下面的代码：
```javascript
var myObject = {};

Object.defineProperty(myObject, "a", {
	value: 2,
	writable: false, // 不可写
	configurable: true,
	enumerable: true
});

myObject.a = 3;

myObject.a; // 2
```
如你所见，我们对于属性值的修改静默失败（silently failed）了。如果在严格模式下，这种方法会出错：
```javascript
"use strict"

var myObject = {};

Object.getOwnProperty(myObject, "a", {
	value: 2,
	writable: false, // 不可写
	configurable: true,
	enumerable: true
});

myObject.a = 3; // TypeError
```
TypeError 错误表示我们无法修改一个不可写的属性。
>之后我们会介绍 getter 和 setter，不过简单来说，你可以把 writable: fase 看作是属性不可改变，相当于你定义了一个空操作 setter。严格来说，如果要和 writable: false 一致的话，你的 setter 被调用时应当抛出一个 TypeError 错误。

### 2. Configurable

只要属性是可配置的，就可以使用 defineProperty(..) 方法来修改属性描述符：
```javascript
var myObject = {
	a: 2
};

myObject.a = 3;
myObject.a; // 3

Object.defineProperty(myObject, "a", {
	value: 4,
	writable: true,
	configurable: false, // 不可配置
	enumerable: true
});

myObject.a; // 4
myObject.a = 5;
myObject.a; // 5

Object.defineProperty(myObject, "a", {
	value: 6,
	writable: true,
	configurable: true,
	enumerable: true
}); // TypeError
```
最后一个 defineProperty(..)会产生一个 TypeError 错误，不管是不是处于严格模式，尝试修改一个不可配置的属性描述符都会出错。注意：如你所见，把 configurable 修改成 false 是单向操作，无法撤销。
>要注意有一个小小的例外：即便属性是 configrable: false，我们还是可以把 writable 的状态由 true 改为 false，但是无法由 false 改为 true。

除了无法修改，configurable: false 还会禁止删除这个属性：
```javascript
var myObject = {
	a: 2
};

myObject.a; // 2

delete myObject.a;
myObject.a; // undefined

Object.defineProperty(myObject, "a", {
	value: 2,
	writable: true,
	configurable: false,
	enumerable: true
});

myObject.a; // 2
delete myObject.a;
myObject.a; // 2
```
如你所见，最后一个 delete 语句（静默）失败了，因为属性是不可配置的。

在本例中，delete 只用来直接删除对象的（可删除）属性。如果对象的某个属性是某个对象/函数的最后一个引用者，对这个属性执行 delete 操作之后，这个未引用的对象/函数就可以被垃圾回收。但是，不要把 delete 看作一个释放内存的工具（就像 C/C++ 中那样），它就是一个删除对象属性的操作，仅此而已。
### 3. Enumerable

这里我们要介绍的最后一个属性描述符（还有两个，我们会在介绍 getter 和 setter 时提到）是 enumerable。

从名字就可以看出，这个描述符控制的是属性是否会出现在对象的属性枚举中，比如说 for..in 循环。如果把 enumerable 设置成 false，这个属性就不会在枚举中，虽然仍然可以正常访问它。相对地，设置成 true 就会让它出现在枚举中。

用户定义的所有的普通属性默认是 enumerable: true，这通常就是你想要的。但是如果你不希望某些特殊属性出现在枚举中，那就把它设置为 enumerable: false。

稍后我们详细介绍可枚举性，这里先提示一下。
## 3.3.6 不变性

有时候你会希望属性或者对象是不可改变（无论是有意还是无意）的，在 ES5 中可以通过很多种方法来实现。

很重要一点是，所有的方法创建的都是浅不变性，也就是说，它们只会影响目标对象和它的直接属性。如果目标对象引用了其他对象（数组、对象、函数，等），其他对象的内容不受影响，仍然是可变的：
```javascript
myImmutableObject.foo; // [1,2,3]
myImmutableObject.foo.push(4);
myImmtutableObject.foo; // [1,2,3,4]
```
假设代码中的 myImmutableObject 已经被创建而且是不可变的，但是为了保护它的内容 myImmutableObject.foo，你还需要使用下面的方法让 foo 也不可变。
>在 JavaScript 程序中很少需要深不可变性。有些特殊情况可能需要这样做，但是根据通用的设计模式，如果你发现需要密封或者冻结所有的对象，那你或许应当退一步，重新思考一下程序的设计，让它能更好地应对对象值的改变。

### 3.3.6.1 对象常量

结合 writable: false 和 configurable: false 就可以创建一个真正的常量属性（不可修改、重定义或者删除）：
```javascript
var myObject = {};

Object.defineProperty(myObject, "FAVORITE_NUMBER", {
	value: 42,
	writable: false,
	configurable: false
});
```
### 3.3.6.2 禁止扩展

如果你想禁止一个对象添加新属性并且保留已有属性，可以使用 Object.preventExtensions(..)：
```javascript
var myObject= {
	a: 2
};

Object.preventExtensions(myObject);

myObject.b = 3;
myObject.b; // undefined
```
在非严格模式下，创建属性 b 会静默失败。在严格模式下，将会抛出 TypeError 错误。
### 3.3.6.3 密封

Object.seal(..) 会创建一个“密封”的对象，这个方法实际上会在一个现有对象上调用 Object.preventExtensions(..) 并把所有属性标记为 configurable: false。

所以，密封之后不仅不能添加新属性，也不能重新配置或者删除任何现有属性（虽然可以修改属性的值）。
### 3.3.6.4 冻结

Object.freeze(..) 会创建一个冻结对象，这个方法实际上会在一个现有对象上调用 Object.seal(..) 并把所有“数据访问”属性标记为 writable: false，这样就无法修改它们的值。

这个方法是你可以应该在对象上的级别最高的不可变性，它会禁止对于对象本身及其任意直接属性的修改（不过就像我们之前说过的，这个对象引用的其他对象是不受影响的）。

你可以“深度冻结”一个对象，具体方法为，首先在这个对象上调用 Object.freeze(..)，然后遍历它引用的所有对象并在这些对象上调用 Object.freeze(..)。但是一定要小心，因为这样做有可能会在无意中终结其他（共享）对象。
## 3.3.7 `[[Get]]`

属性访问在实现时有一个微妙却非常重要的细节，思考下面的代码：
```javascript
var myObject = {
	a: 2
};

myObject.a; // 2
```
myObject.a 是一次属性访问，但是这条语句并不仅仅是在 myObject 中查找名字为 a 的属性，虽然看起来好像是这样。

在语言规范中，myObject.a 在 myObject 上实际上是实现了 `[[Get]]` 操作（有点像函数调用：`[[Get]]()`）。对象默认的内置`[[Get]]` 操作首先在对象中查找是否有名称相同的属性，如果找到就会返回这个属性的值。

然而，如果没有找到名称相同的属性，按照 `[[Get]]` 算法的定义会执行另外一种非常重要的行为、我们会在第 5 章中介绍这个行为（其实就是遍历可能存在的 `[[Protoype]]` 链，也就是原型链）。

如果无论如何都没有找到名称相同的属性，那 `[[Get]]` 操作会返回值 undefined：
```javascript
var myObject = {
	a: 2
};

myObject.b; // undefined
```
## 3.3.8 `[[Put]]`
## 3.3.9 Getter 和 Setter

对象默认的 `[[Put]]` 和 `[[Get]]` 操作分别可以控制属性值的设置和获取。
>在语言的未来/高级特性中，有可能可以改写整个对象（不仅仅是某个属性）的默认 `[[Get]]` 和 `[[Put]]` 操作。这已经超出了本书的讨论范围，但是将来“你不知道的 JavaScript” 系统丛书中有可能会对这个问题进行探讨。

在 ES5 中可以使用 getter 和 setter 部分改写默认操作，但是只能应用在单个属性上，无法应用在整个对象上。getter 是一个隐藏函数，会在获取属性值时调用。setter 也是一个隐藏函数，会在设置属性值时调用。

当你给一个属性定义 getter、setter 或者两者都有时，这个属性会被定义为“访问描述符”（和“数据描述符” 相对）。对于访问描述符来说，JavaScript 会忽略它们的 value 和 writable 特性，取而代之的是关心 set 和 get（还有 configurable 和 enumerable）特性。

思考下面的代码：
```javascript
var myObject = {
	// 给 a 定义一个 getter
	get a() {
		return 2;
	}
};

Object.defineProperty(myObject, "b", {
	// 给 b 设置一个 getter
	get: function() {
		return this.a * 2;
	},
	// 确保 b 会出现在对象的属性列表中
	enumerable: true
});

myObject.a; // 2

myObject.b; // 4
```
不管是对象文字语法中的 get a() {..}，还是 defineProperty(..) 中的显式定义，二者都会在对象中创建一个不包含值的属性，对于这个属性的访问会自动调用一个隐藏函数，它的返回值会被当作属性访问的返回值：
```javascript
var myObject = {
	// 给 a 定义一个 getter
	get a() {
		return 2;
	}
};

myObject.a = 3;

myObject.a; // 2
```
由于我们只定义了 a 的 getter，所以对 a 的值进行设置时 set 操作会忽略赋值操作，不会抛出错误。而且即便有合法的 setter，由于我们自定义的 getter 只会返回 2，所以 set 操作是没有意义的。

为了让属性更合理，还应当定义 setter，和你期望的一样，setter 会覆盖单个属性默认的`[[Put]]`（也被称为赋值）操作。通常来说 getter 和 setter 是成对出现的（只定义一个的话通常会产生意料之外的行为）：
```javascript
var myObject = {
	// 给 a 定义一个 getter
	get a() {
		return this._a_;
	},
	set a(val) {
		this._a_ = val * 2;
	}
};

myObject.a = 2;

myObject.a; // 4
```
由于我们只定义了 a 的 getter，所以对 a 的值进行设置时 set 操作会忽略赋值操作，不会抛出错误。而且即便有合法的 setter，由于我们自定义的 getter 只会返回 2，所以 set 操作是没有意义的。

为了让属性更合理，还应当定义 setter，和你期望的一样，setter 会覆盖单个属性默认的 `[[Put]]`（也被称为赋值）操作。通常来说 getter 和 setter 是成对出现的（只定义一个的话通常会产生意料之外的行为）：
```javascript
var myObject = {
	// 给 a 定义一个 getter
	get a() {
		return this._a_;
	},
	// 给 a 定义一个 setter
	set a(val) {
		this._a_ = val * 2;
	}
};

myObject.a = 2;

myObject.a; // 4
```
>在本例中，实际上我们把赋值(`[[Put]]`) 操作中的值 2 存储到了另一个变量`_a_`中。名称 `_a_` 只是一种惯例，没有任何特殊的行为，和其他普通属性一样。

## 3.3.10 存在性

前面我们介绍过，如 myObject.a 的属性访问返回值可能是 undefined，但是这个值有可能是属性中存储的 undefined，也可能是因为属性不存在所以返回 undefined，那么如何区分这两种情况呢？

我们可以在不访问属性值的情况下判断对象中是否存在这个属性：
```javascript
var myObject = {
	a: 2
};

("a" in myObject); // true
("b" in myObject); // false

myObject.hasOwnProperty("a"); // true
myObject.hasOwnProperty("b"); // false
```
in 操作符会检查属性是否都在对象及其 `[[Prototype]]` 原型链中（参见第 5 章）。相比之下，hasOwnProperty(..) 只会检查属性是否在 myObject 对象中，不会检查 `[[Prototype]]` 链。在第 5 章讲解 `[[Prototype]]` 时我们会详细介绍这两者的区别。

所有的普通对象都可以通过对于 Object.prototype 委托（参见第 5 章）来访问 hasOwnProperty(..)，但是有的对象可能没有连接到 Object.prototype（通过 Object.create(null) 来创建，参见第 5 章）。在这种情况下，形如 myObject.hasOwnProperty(..) 就会失败。

这时可以使用一种更加强硬的方法来进行判断：Object.prototype.hasOwnProperty.call(myObject, "a")，它借用基础的 hasOwnProperty(..) 方法并把它显式绑定（参见第 2 章）到 myObject 上。
>看起来 in 操作符可以检查容器内是否有某个值，但是它实际上检查的是某个属性名是否存在。对于数组说这个区别非常重要，4 in [2,4,6] 的结果并不是你期待的 true，因为 [2,4,6] 这个数组中包含的属性名是 0、1、2，没有 4。
### 3.3.10.1 枚举

之前介绍 enumerable 属性描述符特性时我们简单解释过什么是“可枚举性”，现在详细介绍一下：
```javascript
var myObject = { };

Object.defineProperty(myObject, "a", {
	enumerable: true,
	value: 2
});

Object.defineProperty(myObject, "b", {
	enumerable: false,
	value: 3
});

myObject.b; // 3
("b" in myObject); // true
myObject.hasOwnProperty("b"); // true

for (var k in myObject) {
	console.log(k, myObject[k]);
}

// "a" 2
```
可以看到，myObject.b 确实存在并且有访问值，但是却不会出现在 for..in 循环中（尽管可以通过 in 操作符来判断是否存在）。原因是“可枚举”就相当于“可以出现在对象属性的遍历中“。
>在数组上应用 for..in 循环有时会产生出人意料的结果，因为这种枚举不仅会包含所有数值索引，还会包含所有可枚举属性。最好只在对象上应用 for..in 循环，如果要遍历数组就使用传统的 for 循环来遍历数值索引。

也可以通过另一种方式来区分属性是否可枚举：
```javascript
var myObject = { };

Object.defineProperty(myObject, "a", {
	enumerable: true,
	value: 2
});

Object.defineProperty(myObject, "b", {
	enumerable: false,
	value: 3
});

myObject.propertyIsEnumerable("a"); // true
myObject.propertyIsEnumerable("b"); // false

Object.keys(myObject); // ["a"]
Object.getOwnPropertyNames(myObject); // ["a", "b"]
```
propertyIsEnumerable(..) 会检查给定的属性名是否直接存在于对象中（而不是在原型链上）并且满足 enumerable：true。

Object.keys(..) 会返回一个数组，包含所有可枚举属性，Object.getOwnPropertyNames(..) 会返回一个数组，包含所有属性，无论它们是否可枚举。

in 和 hasOwnProperty(..) 的区别在于是否查找 `[[Prototype]]` 链，然而，Object.keys(..) 和 Object.getOwnPropertyNames(..) 都只会查找对象直接包含的属性。

（目前）并没有内置的方法可以获取 in 操作符使用功能的属性列表（对象本身的属性以及 `[[Prototype]]` 链中的所有属性，参见第 5 章）。不过你可以递归遍历某个对象的整条 `[[Prototype]]` 链并保存每一层中使用 Object.keys(..) 得到的属性列表，只包含可枚举属性。
# 3.4 遍历

for..in 循环可以用来遍历对象的可枚举属性列表（包括 `[[Prototype]]` 链）。但是如何遍历属性的值呢？

对于数值索引的数组来说，可以使用标准的 for 循环来遍历值：
```javascript
var myArray = [1, 2, 3];

for (var i = 0; i < myArray.length; i++) {
	console.log(myArray[i]);
}

// 1 2 3
```
这实际上并不是在遍历值，而是遍历下标来指向值，如 myArray[i]。

ES5 中增加了一些数组的辅助迭代器，包括 forEach(..)、every(..) 和 some(..)。每种辅助迭代器都可以接受一个回调函数并把它应用到数组的每个元素上，唯一的区别就是它们对于回调函数返回值的处理方式不同。

forEach(..) 会遍历数组中的所有值并忽略回调函数的返回值。eveny(..) 会一直运行直到回调函数返回 false（或者“假”值），some(..) 会一直运行直到回调函数返回 true（或者“真”值）。

every(..) 和 some(..) 中特殊的返回值和普通 for 循环中的 break 语句类似，它们会提前终止遍历。

使用 for..in 遍历对象是无法直接获取属性值的，因为它实际上遍历的是对象中的所有可枚举属性，你需要手动获取属性值。
>遍历数组下标时采用的是数字顺序（for 循环或者其他迭代器），但是遍历对象属性时的顺序是不确定的，在不同的 JavaScript 引擎中可能不一样。因此，在不同的环境中需要保证一致性时，一定不要相信任何观察到的顺序，它们是不可靠的。

那么如何直接遍历值而不是数组下标（或者对象属性）呢？幸好，ES9 增加了一种用来遍历数组的 for..of 循环语法（如果对象本身定义了迭代器的话也可以遍历对象）：
```javascript
var myArray = [1, 2, 3];

for (var v of myArray) {
	console.log(v);
}

// 1
// 2
// 3
```
for..of 循环首先会向被访问对象请求一个迭代器对象，然后通过调用迭代器对象的 next() 方法来遍历所有返回值。

数组有内置的 @@iterator，因此 for..of 可以直接应用在数组上。我们使用内置的 @@iterator 来手动遍历数组，看看它是怎么工作的：
```javascript
var myArray =- [1, 2, 3];
var it = myArray[Symbol.iterator]();

it.next(); // { value: 1, done: false }
it.next(); // { value: 2, done: false }
it.next(); // { value: 3, done: false }
it.next(); // { done: true }
```
>我们使用 ES6 中的符号 Symbol.iterator 来获取对象的 @@iterator 内部属性。之前我们简单介绍过符号（Symbol，参见 3.3.1 节），跟这里的原理是相同的。引用类似 iterator 的特殊属性时要使用符号名，而不是符号包含的值。此外，虽然看起来很像一个对象，但是 @@iterator 本身并不是一个迭代器对象，而是一个返回迭代器对象的函数，这点非常精妙并且非常重要。

如你所见，调用迭代器的 next() 方法会返回形式为 { value: .. , done: .. } 的值，value 是当前的遍历值，done 是一个布尔值，表示是否还有可以遍历的值。

注意，和值 "3" 一起返回的是 done: false，乍一看好像很奇怪，你必须再调用一次 next() 才能得到 done: true，从而确定完成遍历。这个机制和 ES6 中发生器函数的语义相关，不过已经超出了我们的讨论范围。

和数组不同，普通的对象没有内置的 @@iterator，所以无法自动完成 for..of 遍历。之所以要这样做，有许多非常复杂的原因，不过简单来说，这样做是为了避免影响未来的对象类型。

当然，你可以给任何想遍历的对象定义 @@iterator，举例来说：
```javascript
var myObject = {
	a: 2,
	b: 3
};

Object.defineProperty(myObject, Symbol.iterator, {
	enumerable: false,
	writable: false,
	configurable: true,
	value: function() {
		var o = this;
		var idx = 0;
		var ks = Object.keys(o);
		return {
			next: function() {
				return {
					value: o[ks[idx++]],
					done: (idx > ks.length);
				}
			}
		}
	}
})

// 手动遍历 myObject
var it = myObject[Symbol.iterator]();
it.next(); // { value: 2, done: false }
it.next(); // { value: 3: done: false }
it.next(); // { value: undefined, done: true }

// 用 for..of 遍历 myObject
for (var v of myObject) {
	console.log(v);
}

// 2
// 3
```
>我们使用 Object.defineProperty(..) 定义了我们自己的 @@iterator（主要是为了让它不可枚举），不过注意，我们把符号当作可计算属性名（本章之前有介绍）。此外，也可以直接在定义时进行声明，比如 var myObject = { a: 2, b: 3, [Symbol.iterator]: function() { `/* .. */` }}。

for..of 循环每次调用 myObject 迭代器对象的 next() 方法时，内部的指针都会向前移动并返回对象属性列表的下一个值（再次提醒，需要注意遍历对象属性/值时的顺序）。

代码中的遍历非常简单，只是传递了属性本身的值。不过只要你愿意，当然也可以在自定义的数据结构上实现各种复杂的遍历。对于用户定义的对象来说，结合 for..of 循环和自定义迭代器可以组成非常强大的对象操作工具。

比如说，一个 Pixel 对象（有 x 和 y 坐标值）列表可以按照距离原点的直线距离来决定遍历顺序，也可以过滤掉“太远”的点，等等。只要迭代器的 next() 调用会返回 { value: .. } 和 { done: true }，ES6 中的 for..of 就可以遍历它。

实际上，你甚至可以定义一个“无限“迭代器，它永远不会”结果”并且总会返回一个新值（比如随机数、递增值、唯一标识符，等等）。你可能永远不会在 for..of 循环中使用这样的迭代器，因为它永远不会结束，你的程序会被挂起：
```javascript
var randoms = {
	[Symbol.iterator]: function() {
		return {
			next: function() {
				return { value: Math.random() }
			}
		};
	}
};

var randoms_pool = [];
for (var n of randoms) {
	randoms_pool.push(n);
	
	// 防止无限运行
	if (randoms_pool.length === 100) break;
}
```
这个迭代器会生成“无限个”随机数，因此我们添加了一条 break 语句，防止程序被挂起。









































































































