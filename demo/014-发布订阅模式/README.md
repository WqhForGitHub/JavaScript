# 014 - 发布订阅模式（EventEmitter）

> 发布订阅模式：订阅者（Subscriber）通过事件中心（EventEmitter）订阅事件，
> 发布者（Publisher）在合适时机发布事件，事件中心通知所有订阅者。
> 典型应用：Vue 的事件总线、Node.js 的 EventEmitter、DOM 事件。

## 代码实现

```js
class EventEmitter {
  constructor() {
    this.events = {};
  }

  on(event, fn) {
    (this.events[event] ||= []).push(fn);
  }

  emit(event, ...args) {
    this.events[event]?.forEach((fn) => fn(...args));
  }

  off(event, fn) {
    this.events[event] = this.events[event]?.filter((f) => f !== fn);
  }

  once(event, fn) {
    const wrapper = (...args) => {
      fn(...args); // 执行原函数
      this.off(event, wrapper); // 执行完就移除自己
    };
    this.on(event, wrapper);
  }
}
```

## 使用示例

```js
const bus = new EventEmitter();

function fn(user) {
  console.log('普通：', user.name);
}

bus.on('login', fn);

bus.once('login', (user) => {
  console.log('一次性：', user.name);
});

bus.emit('login', { name: '张三' });
// 普通：张三
// 一次性：张三

bus.emit('login', { name: '李四' });
// 普通：李四
```
