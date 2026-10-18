JavaScript 中的策略模式（Strategy Pattern），就是把不同的算法分别封装起来，让它们可以相互替换，而不需要修改使用算法的代码。

最经典的例子：根据不同会员等级计算折扣价格。

## 一、先看普通写法的问题

假设有三种会员：

- 普通会员：不打折
- VIP 会员：打 8 折
- SVIP 会员：打 5 折

```javascript
function getPrice(price, type) {
  if (type === "normal") {
    return price;
  } else if (type === "vip") {
    return price * 0.8;
  } else if (type === "svip") {
    return price * 0.5;
  }
}

console.log(getPrice(100, "normal")); // 100
console.log(getPrice(100, "vip"));    // 80
console.log(getPrice(100, "svip"));   // 50
```

问题在于：如果以后增加一个 `vvip` 会员，就需要继续修改 `getPrice()`，不断增加 `else if`。

## 二、使用策略模式实现

策略模式的核心是：每种算法单独封装，使用时选择对应的策略。

```javascript
// 1. 定义不同的策略
const strategies = {
  normal(price) {
    return price;
  },

  vip(price) {
    return price * 0.8;
  },

  svip(price) {
    return price * 0.5;
  }
};

// 2. 定义统一的调用入口
function getPrice(price, type) {
  const strategy = strategies[type];

  if (!strategy) {
    throw new Error("未知的会员类型");
  }

  return strategy(price);
}

// 3. 使用不同的策略
console.log(getPrice(100, "normal")); // 100
console.log(getPrice(100, "vip"));    // 80
console.log(getPrice(100, "svip"));   // 50
```

## 三、代码是如何运行的？

以这行代码为例：

```javascript
getPrice(100, "vip");
```

调用 getPrice(100, "vip")

查找 strategies["vip"]

获取 VIP 对应的计算函数

strategy(100)

执行 price \* 0.8

返回 80

关键在于这两行：

```javascript
const strategy = strategies[type];
return strategy(price);
```

`strategies[type]` 根据会员类型找到对应函数，`strategy(price)` 执行这个函数。

这里的 `normal`、`vip` 和 `svip` 就是三种不同的策略。

## 四、增加新策略有多方便？

假设现在增加 `vvip` 会员，打 3 折。

只需要在 `strategies` 中增加一个方法：

```javascript
const strategies = {
  normal: price => price,
  vip: price => price * 0.8,
  svip: price => price * 0.5,

  // 新增策略
  vvip: price => price * 0.3
};

console.log(strategies.vvip(100)); // 30
```

原来的 `getPrice()` 不需要修改。

这就是策略模式的优势：将变化的算法独立出来，降低代码之间的耦合。

## 五、策略模式的三个核心组成

| 组成              | 本例中的代码            | 作用                   |
| ----------------- | ----------------------- | ---------------------- |
| 策略（Strategy）  | `normal`、`vip`、`svip` | 封装不同的算法         |
| 策略容器          | `strategies`            | 保存所有策略           |
| 上下文（Context） | `getPrice()`            | 根据条件选择并调用策略 |

## 六、什么时候适合使用策略模式？

常见的应用场景：

- 价格计算： 不同会员等级、优惠券、促销活动。
- 表单校验： 手机号、邮箱、密码采用不同校验规则。
- 支付方式： 微信支付、支付宝、银行卡使用不同的支付逻辑。
- 排序算法： 根据不同需求选择不同的排序方式。

例如表单校验，也可以这样组织：

```javascript
const validators = {
  phone(value) {
    return /^1\d{10}$/.test(value);
  },

  email(value) {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value);
  }
};

console.log(validators.phone("13812345678")); // true
console.log(validators.email("test@example.com")); // true
```

记住一句话：策略模式 = 把多个可替换的算法封装起来，根据需要选择其中一个执行。

在 JavaScript 中，利用对象保存函数、利用函数作为一等公民的特性，就可以很自然地实现策略模式，不一定需要使用传统面向对象中的类和接口。