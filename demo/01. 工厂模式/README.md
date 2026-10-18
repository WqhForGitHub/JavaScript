```javascript
// 1. 定义不同类型的用户
class User {
  constructor(name) {
    this.name = name;
  }

  sayHello() {
    console.log(`我是普通用户：${this.name}`);
  }
}

class Admin {
  constructor(name) {
    this.name = name;
  }

  sayHello() {
    console.log(`我是管理员：${this.name}`);
  }
}

// 2. 创建工厂
function UserFactory(type, name) {
  switch (type) {
    case 'user':
      return new User(name);
    case 'admin':
      return new Admin(name);
    default:
      throw new Error('未知的用户类型');
  }
}

// 3. 使用工厂创建对象
const user = UserFactory('user', '张三');
const admin = UserFactory('admin', '李四');

user.sayHello();  // 我是普通用户：张三
admin.sayHello(); // 我是管理员：李四
```