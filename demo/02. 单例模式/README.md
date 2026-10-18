1. 闭包
```javascript
const Singleton = (function () {
  let instance;

  return function () {
    if (!instance) {
      instance = { name: "singleton" };
    }

    return instance;
  };
})();

const s1 = Singleton();
const s2 = Singleton();

console.log(s1 === s2); // true
console.log(s1.name);   // singleton
```

2. 使用 ES6 Class 实现
```javascript
class Singleton {
  static instance;

  constructor() {
    if (Singleton.instance) {
      return Singleton.instance;
    }

    this.name = "singleton";
    Singleton.instance = this;
  }
}

const s1 = new Singleton();
const s2 = new Singleton();

console.log(s1 === s2); // true
console.log(s1.name);   // singleton
```