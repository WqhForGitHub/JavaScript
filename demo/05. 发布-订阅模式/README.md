JavaScript 实现发布-订阅模式（Publish-Subscribe Pattern），核心思想是：发布者不直接通知某个订阅者，而是通过一个事件中心来管理订阅和通知。

## 一、基本实现

```javascript
class EventBus {
  constructor() {
    this.events = {};
  }

  // 订阅事件
  on(event, callback) {
    if (!this.events[event]) {
      this.events[event] = [];
    }

    this.events[event].push(callback);
  }

  // 发布事件
  emit(event, ...args) {
    if (!this.events[event]) return;

    this.events[event].forEach(callback => {
      callback(...args);
    });
  }

  // 取消订阅
  off(event, callback) {
    if (!this.events[event]) return;

    this.events[event] = this.events[event].filter(
      cb => cb !== callback
    );
  }
}
```

## 二、使用示例

```javascript
const eventBus = new EventBus();

// 定义订阅者的回调函数
function handler1(data) {
  console.log("订阅者1：", data);
}

function handler2(data) {
  console.log("订阅者2：", data);
}

// 1. 订阅事件
eventBus.on("message", handler1);
eventBus.on("message", handler2);

// 2. 发布事件
eventBus.emit("message", "Hello World");

// 3. 取消订阅
eventBus.off("message", handler1);

// 4. 再次发布
eventBus.emit("message", "JavaScript");
```

输出结果：

```
订阅者1： Hello World
订阅者2： Hello World
订阅者2： JavaScript
```

## 三、逐个理解核心方法

### 1. `on()`：订阅事件

```javascript
on(event, callback) {
  if (!this.events[event]) {
    this.events[event] = [];
  }

  this.events[event].push(callback);
}
```

例如：

```javascript
eventBus.on("message", handler1);
```

内部会形成这样的数据结构：

![内部会形成这样的数据结构：](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/JavaScript%20%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B8%8E%E5%BC%80%E5%8F%91%E5%AE%9E%E8%B7%B5/%E7%AC%AC8%E7%AB%A0%EF%BC%9A%E5%8F%91%E5%B8%83-%E8%AE%A2%E9%98%85%E6%A8%A1%E5%BC%8F/%E5%86%85%E9%83%A8%E4%BC%9A%E5%BD%A2%E6%88%90%E8%BF%99%E6%A0%B7%E7%9A%84%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84.png)

同一个事件可以有多个订阅者。

### 2. `emit()`：发布事件

```javascript
emit(event, ...args) {
  if (!this.events[event]) return;

  this.events[event].forEach(callback => {
    callback(...args);
  });
}
```

例如：

```javascript
eventBus.emit("message", "Hello World");
```

执行过程：

1. 找到 `message` 对应的回调函数数组。
2. 使用 `forEach()` 遍历数组。
3. 依次执行每个回调函数。
4. 将 `"Hello World"` 传递给每个订阅者。

注意：这里的 `emit()` 是同步执行的，除非回调函数本身使用异步逻辑。

### 3. `off()`：取消订阅

```javascript
off(event, callback) {
  if (!this.events[event]) return;

  this.events[event] = this.events[event].filter(
    cb => cb !== callback
  );
}
```

例如：

```javascript
eventBus.off("message", handler1);
```

`filter()` 会保留所有不等于 `handler1` 的回调函数。

原来：

```
[handler1, handler2]
```

取消订阅后：

```
[handler2]
```

注意：取消订阅时，需要传入原来的回调函数引用。 如果传入一个新创建的匿名函数，通常无法匹配原来的函数。

## 四、发布-订阅模式的执行流程

![发布-订阅模式的执行流程](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/JavaScript%20%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B8%8E%E5%BC%80%E5%8F%91%E5%AE%9E%E8%B7%B5/%E7%AC%AC8%E7%AB%A0%EF%BC%9A%E5%8F%91%E5%B8%83-%E8%AE%A2%E9%98%85%E6%A8%A1%E5%BC%8F/%E5%8F%91%E5%B8%83-%E8%AE%A2%E9%98%85%E6%A8%A1%E5%BC%8F%E7%9A%84%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B.png)

发布者只需要知道事件名称，不需要知道有哪些订阅者；订阅者也不需要知道是谁发布了事件。这就是解耦。

## 五、和观察者模式有什么区别？

| 对比         | 观察者模式               | 发布-订阅模式          |
| ------------ | ------------------------ | ---------------------- |
| 通知方式     | 目标对象直接通知观察者   | 通过事件中心通知       |
| 是否有中间层 | 通常没有                 | 通常有                 |
| 耦合程度     | 观察者与目标对象存在关联 | 发布者与订阅者相互独立 |
| 常见场景     | 状态变化通知             | 全局事件总线、消息系统 |

记忆方法：

- 观察者模式：目标对象直接通知观察者。
- 发布-订阅模式：发布者 → 事件中心 → 订阅者。

以上实现已经包含三个核心功能：`on()`、`emit()` 和 `off()`。实际项目中还可以增加 `once()` 方法，让事件只触发一次。