# 4.1 先有鸡还是先有蛋

直觉上会认为 JavaScript 代码在执行时是由上到下一行一行执行的。但实际上这并不完全正确，有一种特殊情况会导致这个假设是错误的。

考虑以下代码：
```javascript
a = 2;

var a;

console.log(a);
```
你认为 console.log(..) 声明会输出什么呢？

很多开发者会认为是 undefined，因为 var a 声明在 a = 2 之后，他们自然而然地认为变量被重新赋值了，因此会被赋予默认值 undefined。但是，真正的输出结果是 2。

考虑另外一段代码：
```javascript
console.log(a);

var a = 2;
```
鉴于上一个代码片段所表现出来的某种非自上而下的行为特点，你可能会认为这个代码片段也会有同样的行为而输出 2。还有人可能会认为，由于变量 a 在使用前没有先进行声明，因此会抛出 ReferenceError 异常。

不幸的是两种猜测都是不对的。输出来的会是 undefined。

那么到底发生了什么？看起来我们面对的是一个先有鸡还是先有蛋的问题。到底是声明（蛋）在前，还是赋值（鸡）在前？
# 4.2 编译器再度来袭

为了搞明白这个问题，我们需要回顾一下第 1 章中关于编译器的内容。回忆一下，引擎会在解释 JavaScript 代码之前首先对其进行编译。编译阶段总的一部分工作就是找到所有的声明，并用合适的作用域将它们关联起来。第 2 章中展示了这个机制，也正是词法作用域的核心内容。

因此，正确的思考思路是，包括变量和函数在内的所有声明都会在任何代码被执行前首先被处理。

当你看到 var a = 2; 时，可能会认为这是一个声明。但 JavaScript 实际上会将其看成两个声明：var a; 和 a = 2;。第一个定义声明是在编译阶段进行的。第二个赋值声明会被留在原地等待执行阶段。

我们的第一个代码片段会以如下形式进行处理：
```javascript
var a;

a = 2;

console.log(a);
```
其中第一部分是编译，而第二部分是执行。

类似地，我们的第二个代码片段实际是按照以下流程处理的：
```javascript
var a;

console.log(a);

a = 2;
```
因此，打个比方，这个过程就好像变量和函数声明从它们在代码中出现的位置被“移动”到了最上面。这个过程就叫作提升。

换句话说，先有蛋（声明）后有鸡（赋值）。
>只有声明本身会被提升，而赋值或其他运行逻辑会留在原地。如果提升改变了代码执行的顺序，会造成非常严重的破坏。
>```javascript
>foo();
>
>function foo() {
>	console.log(a); // undefined
>	var a = 2;
>}
>```

foo 函数的声明（这个例子还包括实际函数的隐含值）被提升了，因此第一行中的调用可以正常执行。

另外值得注意的是，每个作用域都会进行提升操作。尽管前面大部分的代码片段已经简化了（因为它们只包含全局作用域），而我们正在讨论的 foo(..) 函数自身也会在内部对 var a 进行提升（显然并不是提升到了整个程序的最上方）。因此这段代码实际上会被理解为下面的形式：
```javascript
function foo() {
	var a;
	
	console.log(a); // undefined
	
	a = 2;
}

foo();
```
可以看到，函数声明会被提升，但是函数表达式却不会被提升。
```javascript
foo(); // 不是 ReferenceError, 而是 TypeError

var foo = function bar() {
	// ...
}
```
这段程序中的变量标识符 foo() 被提升并分配给所在作用域（在这里是全局作用域），因此 foo() 不会导致 ReferenceError。但是 foo 此时并没有赋值（如果它是一个函数声明而不是函数表达式，那么就会赋值）。foo() 由于对 undefined 值进行函数调用而导致非法操作，因此抛出 TypeError 异常。

同时也要记住，即使是具名的函数表达式，名称标识符在赋值之前也无法在所在作用域中使用：
```javascript
foo(); // TypeError
bar(); // ReferenceError

var foo = function bar() {
	// ...
}
```
这个代码片段经过提升后，实际上会被理解为以下形式：
```javascript
var foo;

foo(); // TypeError
bar(); // ReferenceError

foo = function() {
	var bar = ...self...
	// ...
}
```
# 4.3 函数优先

函数声明和变量都会被提升。但是一个值得注意的细节（这个细节可以出现在有多个“重复” 声明的代码中）是函数会首先被提升，然后才是变量。

考虑以下代码：
```javascript
foo(); // 1

var foo;

function foo() {
	console.log(1);
}

foo = function () {
	console.log(2);
};
```
会输出 1 二不是 2。这个代码片段会被引擎理解为如下形式：
```javascript
function foo() {
	console.log(1);
}

foo(); // 1

foo = function() {
	console.log(2);
};
```
注意，var foo 尽管出现在 function foo()... 的声明之前，但它是重复的声明（因此被忽略了）。因为含税声明会被提升到普通变量之前。

尽管重复的 var 声明会被忽略掉，但出现在后面的函数声明还是可以覆盖前面的。
```javascript
foo(); // 3

function foo() {
	console.log(1);
}

var foo = function () {
	console.log(2);
}

function foo() {
	console.log(3);
}
```
虽然这些听起来都是些无用的学院理论，但是它说明了在同一个作用域中进行重复定义是非常糟糕的，而且经常会导致各种奇怪的问题。

一个普通块内部的函数声明通常会被提升到所在作用域的顶部，这个过程不会像下面的代码暗示的那样可以被条件判断所控制：
```javascript
foo(); // TypeError: foo is not a function

var a = true;

if (a) {
	function foo() { console.log("a"); }
} else {
	function foo() { console.log("b"); }
}
```
但是需要注意这个行为并不可靠，在 JavaScript 未来的版本中有可能发生改变，因此应该尽可能避免在块内部声明函数。



















