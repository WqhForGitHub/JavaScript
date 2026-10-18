适配器模式（Adapter Pattern）是一种结构型设计模式，它的作用是：将一个接口转换成客户端期望的另一个接口，让原本不兼容的对象能够一起工作。

## 一、JavaScript 实现适配器模式

假设我们有一个旧的支付系统，它使用 `payMoney()` 方法，但新系统要求统一使用 `pay()` 方法。

我们可以通过适配器来兼容旧系统，而不需要修改旧系统的代码。

### 1. 完整代码

```javascript
// 1. 旧系统：使用 payMoney 方法
class OldPayment {
  payMoney(amount) {
    console.log(`旧系统支付：${amount} 元`);
  }
}

// 2. 适配器：将旧接口转换成新接口
class PaymentAdapter {
  constructor(oldPayment) {
    this.oldPayment = oldPayment;
  }

  pay(amount) {
    this.oldPayment.payMoney(amount);
  }
}

// 3. 新系统：统一调用 pay 方法
function checkout(payment, amount) {
  payment.pay(amount);
}

// 4. 使用
const oldPayment = new OldPayment();
const adapter = new PaymentAdapter(oldPayment);

checkout(adapter, 100);

// 输出：旧系统支付：100 元
```

### 2. 代码执行过程

![适配器模式](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/JavaScript%20%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B8%8E%E5%BC%80%E5%8F%91%E5%AE%9E%E8%B7%B5/%E7%AC%AC17%E7%AB%A0%EF%BC%9A%E9%80%82%E9%85%8D%E5%99%A8%E6%A8%A1%E5%BC%8F/%E9%80%82%E9%85%8D%E5%99%A8%E6%A8%A1%E5%BC%8F.png)

理解这三个角色：

- 客户端（checkout）：只知道 `pay()`，不关心具体支付系统。
- 适配器（PaymentAdapter）：负责转换接口。
- 被适配者（OldPayment）：提供旧接口 `payMoney()`。

## 二、再看一个更实际的例子

假设不同天气服务返回的数据格式不同。

一个服务返回：

```javascript
const oldWeatherService = {
  getWeather() {
    return {
      cityName: "东京",
      temperature: 25
    };
  }
};
```

但我们的应用统一要求天气数据格式为：

```
{
  city: "东京",
  temp: 25
}
```

可以通过适配器统一数据格式。

```javascript
// 旧天气服务
const oldWeatherService = {
  getWeather() {
    return {
      cityName: "东京",
      temperature: 25
    };
  }
};

// 适配器
class WeatherAdapter {
  constructor(service) {
    this.service = service;
  }

  getWeather() {
    const data = this.service.getWeather();

    return {
      city: data.cityName,
      temp: data.temperature
    };
  }
}

// 客户端
function showWeather(weatherService) {
  const weather = weatherService.getWeather();

  console.log(`${weather.city}：${weather.temp}℃`);
}

// 使用
const adapter = new WeatherAdapter(oldWeatherService);

showWeather(adapter);

// 输出：东京：25℃
```

这里适配器做了两件事：

1. 将旧服务的调用方式包装成统一的 `getWeather()` 接口。
2. 将旧服务的数据字段 `cityName`、`temperature` 转换成 `city`、`temp`。

## 三、适配器模式的核心

可以用一句话记忆：

不修改原有代码，通过增加一个适配器，让不兼容的接口变得兼容。

适配器模式特别适合以下场景：

- 接入第三方 API，统一不同服务的数据格式。
- 兼容旧代码，避免大规模修改已有系统。
- 统一不同支付渠道、日志系统或存储服务的调用接口。

需要注意的是，如果可以直接修改接口且不会影响其他代码，未必需要引入适配器。设计模式的目的是解决实际问题，而不是单纯增加类的数量。