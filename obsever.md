# 发布—订阅模式
发布—订阅模式又叫观察者模式，它的定义是：
> 定义对象间的一种一对多的依赖关系,当一个对象的状态发生改变时,所有依赖于它的对象都将得到通知。

### 应用场景举例
实现同步滚动，引入观察者解耦：
``` JavaScript
const Event = (function () {
  const clientList = {};
  let listen, trigger, remove;

  listen = function (key, fn) {
    if (!clientList[key]) {
      clientList[key] = [];
    }
    clientList[key].push(fn);
  };

  trigger = function (...args) {
    let key = Array.prototype.shift.call(args);
    let fns = clientList[key];
    if (!fns || fns.length === 0) {
      return false;
    }
    for (let i = 0, fn; fn = fns[i++];) {
      fn.apply(this, args);
    }
  };

  remove = function (key, fn) {
    let fns = clientList[key];
    if (!fns) {
      return false;
    }
    if (!fn) {
      fns && (fns.length = 0);
    } else {
      for (let i = fns.length - 1; i >= 0; i--) {
        let _fn = fns[i];
        if (_fn === fn) {
          fns.splice(i, 1);
        }
      }
    }
  };

  return {
    listen: listen,
    trigger: trigger,
    remove: remove
  }
})();

Event.listen('outputHello', () => {
  console.log('say Hello');
});
Event.trigger('outputHello');
```

### 发布—订阅模式作用
- 同步多个对象的状态变化
- 解耦各个对象之间的依赖