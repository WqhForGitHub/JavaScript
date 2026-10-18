JavaScript 实现观察者模式（Observer Pattern），核心思想是：一个对象的状态发生变化时，自动通知所有观察者。

## 一、基本实现

```javascript
class Subject {
  constructor() {
    this.observers = [];
  }

  // 添加观察者
  subscribe(observer) {
    this.observers.push(observer);
  }

  // 移除观察者
  unsubscribe(observer) {
    this.observers = this.observers.filter(
      item => item !== observer
    );
  }

  // 通知所有观察者
  notify(data) {
    this.observers.forEach(observer => {
      observer.update(data);
    });
  }
}

class Observer {
  constructor(name) {
    this.name = name;
  }

  update(data) {
    console.log(`${this.name} 收到通知：${data}`);
  }
}

// 创建被观察者
const subject = new Subject();

// 创建观察者
const observer1 = new Observer("观察者1");
const observer2 = new Observer("观察者2");

// 订阅
subject.subscribe(observer1);
subject.subscribe(observer2);

// 发布通知
subject.notify("数据发生变化");

// 取消订阅
subject.unsubscribe(observer1);

subject.notify("第二次数据变化");
```

运行结果：

```
观察者1 收到通知：数据发生变化
观察者2 收到通知：数据发生变化
观察者2 收到通知：第二次数据变化
```

## 二、逐个解释核心方法

### 1. `subscribe()`：添加观察者

```javascript
subscribe(observer) {
  this.observers.push(observer);
}
```

将观察者添加到数组中。

例如：

```javascript
subject.subscribe(observer1);
subject.subscribe(observer2);
```

此时，`observers` 数组中有两个观察者。

### 2. `notify()`：通知所有观察者

```javascript
notify(data) {
  this.observers.forEach(observer => {
    observer.update(data);
  });
}
```

遍历所有观察者，逐个调用它们的 `update()` 方法。

例如：

```javascript
subject.notify("数据发生变化");
```

两个观察者都会收到通知。

### 3. `unsubscribe()`：移除观察者

```javascript
unsubscribe(observer) {
  this.observers = this.observers.filter(
    item => item !== observer
  );
}
```

使用 `filter()` 保留其他观察者，将指定观察者移除。

移除之后，该观察者就不会再收到通知。

## 三、观察者模式的执行流程

![观察者模式的执行流程](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/JavaScript%20%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B8%8E%E5%BC%80%E5%8F%91%E5%AE%9E%E8%B7%B5/%E8%A7%82%E5%AF%9F%E8%80%85%E6%A8%A1%E5%BC%8F/%E8%A7%82%E5%AF%9F%E8%80%85%E6%A8%A1%E5%BC%8F%E7%9A%84%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B.png)

## 四、实际应用：模拟用户订阅消息

比如一个网站有两个用户订阅了消息通知。当有新消息时，系统自动通知他们。

```javascript
class NewsPublisher {
  constructor() {
    this.subscribers = [];
  }

  subscribe(callback) {
    this.subscribers.push(callback);
  }

  publish(news) {
    this.subscribers.forEach(callback => {
      callback(news);
    });
  }
}

const publisher = new NewsPublisher();

publisher.subscribe(news => {
  console.log(`用户 A 收到：${news}`);
});

publisher.subscribe(news => {
  console.log(`用户 B 收到：${news}`);
});

publisher.publish("今天发布了一条新消息");
```

输出：

```
用户 A 收到：今天发布了一条新消息
用户 B 收到：今天发布了一条新消息
```

这里直接使用函数作为观察者，不需要专门定义 `Observer` 类。

## 五、观察者模式与发布-订阅模式的区别

| 对比     | 观察者模式             | 发布-订阅模式                  |
| -------- | ---------------------- | ------------------------------ |
| 核心角色 | 被观察者、观察者       | 发布者、订阅者、事件中心       |
| 通知方式 | 被观察者直接通知观察者 | 通常通过事件中心转发           |
| 耦合程度 | 被观察者知道观察者     | 发布者和订阅者通常互不直接依赖 |
| 常见场景 | 状态变化通知           | 事件总线、消息系统             |

记忆重点：

- `subscribe()`：订阅。
- `unsubscribe()`：取消订阅。
- `notify()`：通知观察者。
- `update()`：观察者收到通知后执行的操作。

观察者模式的关键就是：建立一对多的依赖关系，当一个对象发生变化时，所有依赖它的对象都会收到通知。