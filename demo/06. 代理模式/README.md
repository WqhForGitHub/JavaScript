JavaScript 中的代理模式（Proxy Pattern）：为一个对象提供一个代理对象，由代理对象控制对原对象的访问。

举个例子：你想访问一个用户的信息，但访问前需要进行权限检查，就可以通过代理对象来实现。

## 一、最简单的代理模式

假设有一个真实对象 `RealUser`，我们通过 `UserProxy` 代理它。

```javascript
// 真实对象
class RealUser {
  getUserInfo() {
    return "用户信息：张三";
  }
}

// 代理对象
class UserProxy {
  constructor() {
    this.realUser = new RealUser();
  }

  getUserInfo() {
    // 访问前进行权限检查
    console.log("检查用户权限...");

    // 调用真实对象的方法
    return this.realUser.getUserInfo();
  }
}

const user = new UserProxy();

console.log(user.getUserInfo());

// 检查用户权限...
// 用户信息：张三
```

### 执行过程

![代理模式执行过程](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/JavaScript%20%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B8%8E%E5%BC%80%E5%8F%91%E5%AE%9E%E8%B7%B5/%E7%AC%AC6%E7%AB%A0%EF%BC%9A%E4%BB%A3%E7%90%86%E6%A8%A1%E5%BC%8F/%E4%BB%A3%E7%90%86%E6%A8%A1%E5%BC%8F%E6%89%A7%E8%A1%8C%E8%BF%87%E7%A8%8B.png)

核心思想：客户端不直接访问真实对象，而是通过代理对象访问。

## 二、使用 ES6 Proxy 实现代理

JavaScript 还内置了 `Proxy` 构造函数，可以代理对象的属性读取、修改等操作。

```javascript
const user = {
  name: "张三",
  age: 18
};

const proxy = new Proxy(user, {
  get(target, key, receiver) {
    console.log(`读取属性：${key}`);
    return Reflect.get(target, key, receiver);
  },

  set(target, key, value, receiver) {
    console.log(`修改属性：${key}`);
    return Reflect.set(target, key, value, receiver);
  }
});

console.log(proxy.name);
// 读取属性：name
// 张三

proxy.age = 20;
// 修改属性：age

console.log(user.age);
// 20
```

这里有几个关键点：

- `target`：被代理的原对象，也就是 `user`。
- `key`：当前访问或修改的属性名。
- `Reflect.get()`：执行默认的属性读取操作。
- `Reflect.set()`：执行默认的属性设置操作。

注意：`proxy` 和 `user` 指向的不是同一个对象，但代理操作会影响原对象。

## 三、代理模式的典型应用：图片懒加载

假设图片很大，我们希望真正需要显示图片时才加载它。

```javascript
// 真实图片对象
class RealImage {
  constructor(filename) {
    this.filename = filename;
    this.load();
  }

  load() {
    console.log(`加载图片：${this.filename}`);
  }

  display() {
    console.log(`显示图片：${this.filename}`);
  }
}

// 图片代理
class ImageProxy {
  constructor(filename) {
    this.filename = filename;
    this.realImage = null;
  }

  display() {
    // 第一次显示时才创建真实图片对象
    if (!this.realImage) {
      this.realImage = new RealImage(this.filename);
    }

    this.realImage.display();
  }
}

const image = new ImageProxy("photo.jpg");

// 此时还没有加载图片
console.log("图片代理创建完成");

// 第一次显示：加载并显示
image.display();

// 第二次显示：直接显示，不再加载
image.display();
```

输出：

```
图片代理创建完成
加载图片：photo.jpg
显示图片：photo.jpg
显示图片：photo.jpg
```

代理对象延迟了真实图片对象的创建，实现了按需加载。

## 四、代理模式的常见应用

| 应用场景         | 代理的作用                                |
| ---------------- | ----------------------------------------- |
| 权限控制         | 检查是否有权限访问资源                    |
| 图片懒加载       | 延迟创建和加载图片                        |
| 缓存代理         | 缓存计算结果，避免重复计算                |
| 日志代理         | 记录方法调用和执行情况                    |
| 网络请求代理     | 统一处理请求、重试或缓存                  |
| Vue 3 响应式系统 | 利用 `Proxy` 拦截属性读写，追踪和触发更新 |

## 五、代理模式和 ES6 Proxy 的区别

这两个概念容易混淆：

- 代理模式：一种设计思想，可以通过普通类、对象等实现。
- ES6 `Proxy`：JavaScript 提供的语言特性，用来拦截对象操作，是实现代理模式的一种工具。

记忆重点： 代理模式的核心是控制访问；ES6 `Proxy` 的核心是拦截操作。

如果你在学习 GoF 设计模式，可以重点掌握第一个权限检查示例和第三个图片懒加载示例。它们更容易体现代理模式的设计思想。