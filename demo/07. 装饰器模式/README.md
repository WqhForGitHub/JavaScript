JavaScript 中的\*\*装饰器模式（Decorator Pattern）\*\*属于 GoF 23 种设计模式中的结构型模式。

它的核心思想是：在不修改原有对象代码的情况下，动态地给对象添加额外功能。

## 一、先看一个简单例子

假设我们有一个普通的咖啡对象，现在希望给咖啡添加牛奶、糖等配料。

```javascript
class Coffee {
  cost() {
    return 10;
  }
}

const coffee = new Coffee();

console.log(coffee.cost()); // 10
```

现在要给咖啡添加牛奶，如果直接修改 `Coffee` 类，就需要改动原有代码。

使用装饰器模式，我们可以这样实现。

## 二、使用 JavaScript 实现装饰器模式

```javascript

class Coffee {
  cost() {
    return 10;
  }
}

// 牛奶装饰器
class MilkDecorator {
  constructor(coffee) {
    this.coffee = coffee;
  }

  cost() {
    return this.coffee.cost() + 2;
  }
}

// 糖装饰器
class SugarDecorator {
  constructor(coffee) {
    this.coffee = coffee;
  }

  cost() {
    return this.coffee.cost() + 1;
  }
}

// 原始咖啡
const coffee = new Coffee();
console.log(coffee.cost()); // 10

// 加牛奶
const milkCoffee = new MilkDecorator(coffee);
console.log(milkCoffee.cost()); // 12

// 加牛奶，再加糖
const sweetMilkCoffee = new SugarDecorator(milkCoffee);
console.log(sweetMilkCoffee.cost()); // 13

```

## 三、逐步理解代码

### 1. 原始对象：Coffee

```javascript
class Coffee {
  cost() {
    return 10;
  }
}
```

这是最基本的咖啡，价格为 10 元。

它不需要知道牛奶、糖等配料的存在。

### 2. 装饰器：MilkDecorator

```javascript
class MilkDecorator {
  constructor(coffee) {
    this.coffee = coffee;
  }

  cost() {
    return this.coffee.cost() + 2;
  }
}
```

重点看这两处：

- `this.coffee = coffee`：保存被装饰的对象。
- `this.coffee.cost() + 2`：调用原对象的方法，并在此基础上增加牛奶的价格。

因此，装饰器不是直接修改原来的 `Coffee`，而是包装原对象，并扩展它的功能。

### 3. 装饰器可以嵌套

```javascript
const coffee = new Coffee();

const milkCoffee = new MilkDecorator(coffee);

const sweetMilkCoffee = new SugarDecorator(milkCoffee);

console.log(sweetMilkCoffee.cost()); // 13
```

执行过程如下：

![装饰器可以嵌套执行过程](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/JavaScript%20%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B8%8E%E5%BC%80%E5%8F%91%E5%AE%9E%E8%B7%B5/%E8%A3%85%E9%A5%B0%E5%99%A8%E6%A8%A1%E5%BC%8F/%E8%A3%85%E9%A5%B0%E5%99%A8%E5%8F%AF%E4%BB%A5%E5%B5%8C%E5%A5%97.png)

最终计算：

```
10 + 2 + 1 = 13
```

每个装饰器只负责自己的额外功能，多个装饰器可以灵活组合。

## 四、实际开发中的例子：给函数添加日志

装饰器模式不一定需要使用 `class`，也可以通过高阶函数实现。

例如，我们想在函数执行前后打印日志，但又不想修改原函数。

```javascript
function sayHello(name) {
  return `你好，${name}`;
}

// 日志装饰器
function withLog(fn) {
  return function (...args) {
    console.log("函数开始执行");

    const result = fn.apply(this, args);

    console.log("函数执行结束");

    return result;
  };
}

const decoratedHello = withLog(sayHello);

console.log(decoratedHello("小明"));

// 函数开始执行
// 函数执行结束
// 你好，小明
```

这里的关键是：

- `fn`：原始函数。
- `...args`：接收原函数的参数。
- `fn.apply(this, args)`：保留调用时的 `this`，并执行原函数。
- `return result`：保留原函数的返回值。

这种方式可以给函数添加日志、计时、权限检查等功能，而不必改动原函数。

注意：这个简单版本适用于同步函数；如果要准确记录异步函数的结束时间或异常，还需要额外处理 `Promise`。

## 五、装饰器模式的优缺点

| 优点                       | 缺点                                 |
| -------------------------- | ------------------------------------ |
| 不修改原有代码即可扩展功能 | 装饰器过多时，调用链会变复杂         |
| 可以自由组合多个功能       | 调试时需要追踪多层包装               |
| 比继承更灵活               | 需要注意参数、返回值和 `this` 的保留 |

## 六、装饰器模式与继承的区别

假设需要给咖啡添加牛奶和糖。

继承方式：

```javascript
class MilkCoffee extends Coffee {}
class SugarCoffee extends Coffee {}
```

如果还需要牛奶加糖，就可能继续创建更多子类。

装饰器方式：

```javascript
const coffee = new Coffee();

const result = new SugarDecorator(
  new MilkDecorator(coffee)
);
```

不需要为每一种组合创建一个新类，想加什么功能就包装什么功能。

记住一句话：装饰器模式 = 包装原对象 + 扩展功能 + 不修改原对象。