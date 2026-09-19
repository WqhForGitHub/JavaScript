# 5.1 启示

对于那些有一点 JavaScript 使用经验但从未真正理解闭包概念的人来说，理解闭包可以看作是某种意义上的重生，但是需要付出非常多的努力和牺牲才能理解这个概念。

回忆我前几年的时光，大量使用 JavaScript 但却完全不理解闭包是什么。总是感觉这门语言有其隐蔽的一面，如果能够掌握将会功力大涨，但讽刺的是我始终无法掌握其中的门道。还记得我曾经大量阅读早期框架的源码，试图能够理解闭包的工作原理。现在还能回忆起我的脑海中第一次浮现出关于“模块模式”相关概念时的激动心情。

那时我无法理解并且倾尽数年心血来探索的，也就是我马上要传授给你的秘诀：JavaScript 中闭包无处不在，你只需要能够识别并拥抱它。闭包并不是一个需要学习新的语法或模式才能使用的工具，它也不是一件必须接受像 Luke 一样的原力训练才能使用和掌握的武器。

闭包是基于词法作用域书写代码时所产生的自然结果，你甚至不需要为了利用它们而有意识地创建闭包。闭包的创建和使用在你的代码中随处可见。你缺少的是根据你自己的意愿是识别、拥抱和影响闭包的思维环境。

最后你恍然大悟：原来在我的代码中已经到处都是闭包了，现在我终于能理解它们了。理解闭包就好像 Neo 第一次见到矩阵一样。
# 5.2 实质问题

好了，夸张和浮夸的电影比喻已经够多了。

下面是直接了当的定义，你需要掌握它才能理解和识别闭包“

当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。

下面用一些代码来解释这个定义：
```javascript
function foo() {
	var a = 2;
	
	function bar() {
		console.log(a); // 2
	}
	
	bar();
}

foo();
```
这段代码看起来和嵌套作用域中国的示例代码很相似。基于词法作用域的查找规则，函数 bar() 可以访问外部作用域中的变量 a（这个例子中的是一个 RHS 引用查询）。

这是闭包吗？

技术上来讲，也许是。但根据前面的定义，确切地说并不是。我认为最准确地用来解释 bar() 对 a 的引用的方法是词法作用域的查找规则，而这些规则只是闭包的一部分。（但却是非常重要的一部分）。

从纯学术的角度说，在上面的代码片段中，函数 bar() 具有一个涵盖 foo() 作用域的闭包（事实上，涵盖了它能访问的所有作用域，比如全局作用域）。也可以认为 bar() 封闭在了 foo() 的作用域中。为什么呢？原因简单明了，因为 bar() 嵌套在 foo() 内部。

但是通过这种方式定义的闭包并不能直接进行观察，也无法明白在这个代码片段中闭包是如何工作的。我们可以很容易地理解词法作用域，而闭包则隐藏在代码之后的神秘阴影里，并不那么容易理解。

下面我们来看一段代码，清晰地展示了闭包：
```javascript
function foo() {
	var a = 2;
	
	function bar() {
		console.log(a);
	}
	
	return bar;
}

var baz = foo();

baz();
```
函数 bar() 的词法作用域能够访问 foo() 的内部作用域。然后我们将 bar() 函数本身当作一个值类型进行传递。在这个例子中，我们将 bar 所引用的函数对象本身当作返回值。

在 foo() 执行后，其返回值（也就是内部的 bar() 函数）赋值给变量 baz 并调用 baz()，实际上只是通过不同的标识符引用调用了内部的函数 bar()。

bar() 显然可以被正常执行、但是在这个例子中，它在自己定义的词法作用域以外的地方执行。

在 foo() 执行后，其返回值（也就是内部的 bar() 函数）赋值给变量 baz 并调用 baz()，实际上只是通过不同的标识符引用调用了内部的函数 bar()。

bar() 显然可以被正常执行。但是在这个例子中，它在自己定义的词法作用域以外的地方执行。

在 foo() 执行后，通常会期待 foo() 的整个内部作用域都被销毁，因为我们知道引擎有垃圾回收器用来释放不再使用的内存空间。由于看上去 foo() 的内容不会再被使用，所以很自然地会考虑对其进行回收。

而闭包的”神奇“之处正是可以阻止这件事情的发生。事实上内部作用域依然存在，因此没有被回收。谁在使用这个内部作用域？原来是 bar() 本身在使用。

拜 bar() 所声明的位置所赐，它拥有涵盖 foo() 内部作用域的闭包，使得该作用域能够一直存活，以供 bar() 在之后任何时间进行引用。

bar() 依然持有对该作用域的引用，而这个引用就叫作闭包。

因此，在几微秒之后变量 baz 被实际调用（调用内部函数 bar），不出意外它可以访问定义时的词法作用域，因此它也可以如预期般访问变量 a。

这个函数在定义时的词法作用域以外的地方被调用。闭包使得函数可以继续访问定义时的词法作用域。

当然，无论使用何种方式对函数类型的值进行传递，当函数在别处被调用时都可以观察到闭包。
```javascript
function foo() {
	var a = 2;
	
	function baz() {
		console.log(a); // 2
	}
	
	bar(baz);
}

function bar(fn) {
	fn(); // 闭包
}

foo();
```
把内部函数 baz 传递给 bar，当调用这个内部函数时（现在叫作 fn），它涵盖的 foo(内部作用域的闭包就可以观察到了，因为它能够访问 a)。

传递函数当然也可以是间接的。
```javascript
var fn;

function foo() {
	var a = 2;
	
	function baz() {
		console.log(a);
	}
	
	fn = baz; // 将 baz 分配给全局变量
}

function bar() {
	fn(); // 闭包
}

foo();

bar(); // 2     
```
# 5.3 现在我懂了

前面的代码片段有点死板，并且为了解释如何使用闭包而人为地在结构上进行了修饰。但我保证闭包绝不仅仅是一个好玩的玩具。你已经写过的代码中一定到处都是闭包的身影。现在让我们来搞懂这个事实。
```javascript
function wait(message) {
	setTimeout(function timer() {
		console.log(message);
	}, 1000);
}

wait("Hello, closure");
```
将一个内部函数（名为 timer）传递给 setTimeout(..)。timer 具有涵盖 wait(..) 作用域的闭包，因此还保有对变量 message 的引用。

wait(..) 执行 1000 毫秒后，它的内部作用域并不会消失，timer 函数依然保有 wait(..) 作用域的闭包。

在引擎内部，内置的工具函数 setTimeout(..) 持有对一个参数的引用，这个参数也许叫作 fn 或者 func，或者其他类似的名字。引擎会调用这个函数，在例子中就是内部的 timer 函数。而词法作用域在这个过程中保持完整。

这就是闭包。

或者，如果你熟悉 jQuery（或者其他能说明这个问题的 JavaScript 框架），可以思考下面的代码：
```javascript
function setupBot(name, selector) {
	$(selector).click(function activator() {
		console.log("Activating: " + name);
	});
}

setupBot("Closure Bot 1", "#bot_1");
setupBot("Closure Bot 2", "#bot_2");
```
我不知道你会写什么样的代码，但是我写的代码负责控制由闭包机器人组成的整个全球无人机大军，这是完全可以实现的。

玩笑开完了，本质上无论何时何地，如果将（访问它们各自词法作用域的）函数当作第一级的值类型并到处传递，你就会看到闭包在这些函数中的应用。在定时器、事件监听器、Ajax 请求、跨窗口通信、Web Workers 或者任何其他的异步（或者同步）任务中，只要使用了回调函数，实际上就是在使用闭包。
>第 3 章介绍了 IIFE 模式。通常认为 IIFE 是典型的闭包例子，但根据先前对闭包的定义，我并不是很同意这个观点。

```javascript
var a = 2;

(function IIFE() {
	console.log(a); // 2
})();
```
虽然这段代码可以正常工作，但严格起来讲不是闭包。为什么？因为函数（示例代码中的 IIFE）并不是在它本身的词法作用域以外执行的。它在定义时所在的作用域中执行（而外部作用域，也就是全局作用域也持有 a）。a 是通过普通的词法作用域查找而非闭包被发现的。

尽管技术上来讲，闭包是发生在定义时的，但并不非常明显，就好像六祖慧崩所说：既非风动，亦非幡动，仁者心动耳。

尽管 IIFE 本身并不是观察闭包的恰当例子，但它的确创建了闭包，并且也是最常用来创建可以被封闭起来的闭包的工具。因此 IIFE 的确同作用域息息相关，即使本身并不会真的创建作用域。

亲爱的读者，现在把书放下，我有一个任务要交给你。打开你最近写的 JavaScript 代码，找到其中的函数类型的或者并指出哪里已经使用了作用域，即使你以前可能并不知道这就是闭包。

等你呦。

现在你懂了吧。
# 5.4 循环和闭包

要说明闭包，for 循环是最常见的例子。
```javascript
for (var i = 1; i <= 5; i++) {
	setTimeout(function timer() {
		console.log(i);
	}, i * 1000);
}
```
>由于很多开发者对闭包的概念认识得并不是很清楚，因此当循环内部包含函数定义时，代码格式检查器经常发出警告。我们在这里介绍如何才能正确地使用闭包并发挥它的威力，但是代码格式检查器并没有那么灵敏，它会假设你并不真正了解自己在做什么，所以无论如何都会发出警告。

正常情况下，我们对这段代码行为的预期是分别输出数字 1~5，每秒一次，每次一个。

但实际上，这段代码在运行时会以每秒一次的频率输出五次 6。

这是为什么？

首先解释 6 是从哪里来的。这个循环的终止条件是 i 不再 <=5。条件首次成立时 i 的值是 6。因此，输出显示的是循环结束时 i 的最终值。

仔细想一下，这好像又是显而易见的，延迟函数的回调会在循环结束时才执行。事实上，当定时器运行时即使每个迭代中执行的是 setTimeout(.., 0)，所有的回调函数依然是在循环结束后才会被执行，因此会每次输出一个 6 出来。

这里引申出一个更深入的问题，代码中到底有什么缺陷导致它的行为同语义所暗示的不一致呢？

缺陷是我们试图假设循环中的每个迭代在运行时都会给自己”捕获“一个 i 的副本。但是根据作用域的工作原理，实际情况是尽管循环中的五个函数是在各个迭代中分别定义的，但是它们都被封闭在一个共享的全局作用域中，因此实际上只有一个 i。

这样说的话，当然所有函数共享一个 i 的引用。循环结构让我们误认为背后还有更复杂的机制在起作用，但实际上没有。如果将延迟函数的回调重复定义五次，完全不使用循环，那它同这段代码是完全等价的。

下面回到正题。缺陷是什么？我们需要更多的闭包作用域，特别是在循环过程中每个迭代都需要一个闭包作用域。

第 3 章介绍过，IIFE 会通过声明并立即执行一个函数来创建作用域。

我们来试一下：
```javascript
for (var i = 1; i <= 5; i++) {
	(function () {
		setTimeout(function timer() {
			console.log(i);
		}, i * 1000);
	})();
}
```
这样能行吗？试试吧，我等着你。

我不卖关子了。这样不行。但是为什么呢？我们现在显然拥有更多的词法作用域了。的确每个延迟函数都会将 IIFE 在每次迭代中创建的作用域封闭起来。

如果作用域是空的，那么仅仅将它们进行封闭是不够的。仔细看一下，我们的 IIFE 只是一个什么都没有的空作用域。它需要包含一点实质内容才能为我们所用。

它需要有自己的变量，用来在每个迭代中储存 i 的值：
```javascript
for (var i = 1; i <= 5; i++) {
	(function () {
		var j = i;
		setTimeout(function timer() {
			console.log(j);
		}, j * 1000)
	})();
}
```
行了，它能正常工作了。

可以对这段代码进行一些改进：
```javascript
for (var i = 1; i <= 5; i++) {
	(function(j) {
		setTimeout(function timer() {
			console.log(j);
		}, j * 1000);
	})(i)
}
```
当然，这些 IIFE 也不过就是函数，因此我们可以将 i 传递进去，如果愿意的话可以将变量名定为 j，当然也还可以叫作 i。无论如何这段代码现在可以工作了。

在迭代内使用 IIFE 会为每个迭代都生成一个新的作用域，使得延迟函数的回调可以将新的作用域封闭在每个迭代内部，每个迭代中都会含有一个具有正确值的变量供我们访问。

问题解决啦。
## 重返块作用域

仔细思考我们对前面的解决方案的分析。我们使用 IIFE 在每次迭代时都创建一个新的作用域。换句话说，每次迭代我们都需要一个块作用域。第 3 章介绍了 let 声明，可以用来劫持块作用域，并且在这个块作用域中声明一个变量。

本质上这是将一个块转换成一个可以被关闭的作用域。因此，下面这些看起来很酷的代码就可以正常运行了：
```javascript
for (var i = 1; i <=5; i++) {
	let j = i; // 闭包的块作用域
	setTimeout(function timer() {
		console.log(j);
	}, j * 1000);
}
```
但是，这还不是全部。（我用 Bob Barker 的声音说道）for 循环头部的 let 声明还会有一个特殊的行为。这个行为指出变量在循环过程中不止被声明一次，每次迭代都会声明。随后的每个迭代都会使用上一个迭代结束时的值来初始化这个变量。
```javascript
for (let i = 1; i <=5; i++) {
	setTimeout(function timer() {
		console.log(i);
	}, i * 1000);
}
```
很酷是吧？块作用域和闭包联手便可天下无敌。不知道你是什么情况，反正这个功能让我成为了一名快乐的 JavaScript 程序员。
# 5.5 模块

还有其他的代码模式利用闭包的强大威力，但从表面上看，它们似乎与回调无关。下面一起来研究其中最强大的一个：模块。
```javascript
function foo() {
	var something = "cool";
	var another = [1, 2, 3];
	
	function doSomething() {
		console.log(something);
	}
	
	function doAnother() {
		console.log(another.join(" ! "));
	}
}
```
正如在这段代码中所看到，这里并没有明显的闭包，只有两个私有数据变量 something 和 another，以及 doSomething() 和 doAnother() 两个内部函数，它们的词法作用域（而这就是闭包）也就是 foo() 的内部作用域。

接下来考虑以下代码：
```javascript
function CoolModule() {
	var something = "cool";
	var another = [1, 2, 3];
	
	function doSomething() {
		console.log(something);
	}
	
	function doAnother() {
		console.log(another.join(" ! "));
	}
	
	return {
		doSomething: doSomething,
		doAnother: doAnother
	};
}

var foo = CoolModule();

foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
```
我们将模块函数转换成了 IIFE（参见第 3 章），立即调用这个函数并将返回值直接赋值给单例的模块实例标识符 foo。

模块也是普通的函数，因此可以接受参数：
```javascript
function CoolModule() {
	function identify() {
		console.log(id);
	}
	
	return {
		identify: identify;
	};
}

var foo1 = CoolModule("foo 1");
var foo2 = CoolModule("foo 2");

foo1.identify(); // "foo 1"
foo2.identify(); // "foo 2"
```
模块模式另一个简单但强大的用法是命名将要作为公共 API 返回的对象：
```javascript
var foo = (function CoolModule(id) {
	function change() {
		// 修改公共 API
		publicAPI.identify = identify2;
	}
	
	function identify() {
		console.log(id);
	}
	
	function identify2() {
		console.log(id.toUpperCase());
	}
	
	var publicAPI = {
		change: change,
		identify: identify
	};
	
	return publicAPI;
})("foo module");

foo.identify(); // foo module
foo.change();
foo.identify(); // FOO MODULE
```
通过在模块实例的内部保留对公共 API 对象的内部引用，可以从内部对模块实例进行修改，包括添加或删除方法和属性，以及修改它们的值。
## 5.5.1 现代的模块机制

大多数模块依赖加载器、管理器本质上将这种模块定义封装进一个友好的 API。这里并不会研究某个具体的库，为了宏观了解我会简单地介绍一些核心概念：
```javascript
var MyModules = (function Manager() {
	var modules = {};
	
	function define(name, deps, impl) {
		for (var i = 0; i < deps.length; i++) {
			deps[i] = modules[deps[i]];
		}
		modules[name] = impl.apply(impl, deps);
	}
	
	function get(name) {
		return modules[name];
	}
	
	return {
		define: define,
		get: get
	}
})();
```
这段代码的核心是 modules[name] = impl.apply(impl,deps)。为了模块的定义引入了包装函数（可以传入任何依赖），并且将返回值，也就是模块的 API，储存在一个根据名字来管理的模块列表中。

下面展示了如何使用它来定义模块：
```javascript
MyModule.define("bar", [], function() {
	function hello(who) {
		return "Let me introduce: " + who;
	}
	
	return {
		hello: hello
	};
});

MyModules.define("foo", ["bar"], function(bar) {
	var hungry = "hippo";
	
	function awesome() {
		console.log(bar.hello(hungry).toUpperCase());
	}
	
	return {
		awesome: awesome
	};
});

var bar = MyModules.get("bar");
var foo = MyModules.get("foo");

console.log(bar.hello("hippo"));
// Let ne introduce: hippo

foo.awesome(); // LET ME INTRODUCE: HIPPO
```
"foo" 和 "bar" 模块都是通过一个返回公共 API 的函数来定义的。"foo" 甚至接受 "bar" 的实例作为依赖参数，并能相应地使用它。

为我们自己着想，应该多花一点时间来研究这些示例代码并完全理解闭包的作用吧。最重要的是要理解模块管理器没有任何特殊的“魔力”。它们符合前面列出的模块模式的两个特点：调用包装了函数定义的包装函数，并且将返回值作为该模块的 API。

换句话说，模块就是模块，即使在它们外层加上一个友好的包装工具也不会发生任何变化。
## 5.5.2 未来的模块机制

ES6 中为模块增加了一级语法支持。在通过模块系统进行加载时，ES6 会将文件当作独立的模块来处理。每个模块都可以导入其他模块或特定的 API 成员，同样也可以导出自己的 API 成员。
>基于函数的模块并不是一个能被静态识别的模式（编译器无法识别），它们的 API 语义只有在运行时才会被考虑进来。因此可以在运行时修改一个模块的 API（参考前面关于 public API 的讨论）。

相比之下，ES6 模块 API 是静态的（API 不会在运行时改变）。由于编辑器知道这一单，因此可以在（的确也这样做了）编译期检查对导入模块的 API 成员的引用是否真实存在。如果 API 引用并不存在，编译器会在编译时就抛出“早期”错误，而不会等到运行期再动态解析（并且报错）。

ES6 的模块没有 “行内” 格式，必须被定义在独立文件中（一个文件一个模块）。浏览器或引擎有一个默认的“模块加载器”（可以被重载，但这远超出了我们的讨论范围）可以在导入模块时同步地加载模块文件。

考虑以下代码：
bar.js
```javascript
function hello(who) {
	return "Let me introduce: " + who;
}

export hello;
```
foo.js
```javascript
// 仅从 "bar" 模块导入 hello()
import hello from "bar";

var hungry = "hippo";

function awesome() {
	console.log(hello(hungry).toUpperCase());
}

export awesome;
```
baz.js
```javascript
// 导入完整的 "foo" 和 "bar" 模块
module foo from "foo";
module bar from "bar";

console.log(bar.hello("rhino")); // Let me introduce: rhino

foo.awesome(); // LET ME INTRODUCE: HIPPO
```
>需要用前面两个代码片段中的内容分别创建文件 foo.js 和 bar.js。然后如第三个代码片段中展示的那样，baz.js 中的程序会加载或导入这两个模块并使用它们。

import 可以将一个模块中的一个或多个 API 导入到当前作用域中，并分别绑定一个变量上（在我们的例子里是 hello）。module 会将整个模块的 API 导入并绑定到一个变量上（在我们的例子里是 foo 和 bar）。export 会将当前模块的一个标识符（变量、函数）导出为公共 API。这些操作可以在模块定义中根据需要使用任意多次。

模块文件中的内容会被当作好像包含在作用域闭包中一样来处理，就和前面介绍的函数闭包模块一样。













































































































