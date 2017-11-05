# 享元模式
> 享元(flyweight)模式是一种用于性能优化的模式，享元模式的核心是运用共享技术来有效支持大量细粒度的对象。

享元模式的目标是尽量减少共享对象的数量，它要求将对象的属性划分为内部状态与外部状态(状态在这里通常指属性)：
- 内部状态存储于对象内部
- 内部状态可以被一些对象共享
- 内部状态独立于具体的场景,通常不会改变
- 外部状态取决于具体的场景,并根据场景而变化,外部状态不能被共享  

可以被对象共享的属性通常被划分为内部状态，不能被一些对象共享只能被划分为外
部状态。  

### 对象池
对象池是另外一种性能优化方案,它跟享元模式有一些相似之处,但没有分离内部状态和外部状态这个过程。用对象池+事件委托也可以达到享元模式效果。  
  
利用对象池实现的共享技术的工作流程：对象池维护一个装载空闲对象的池子,如果需要对象的时候,不是直接 new,而是转从对象池里获取。如果对象池里没有空闲对象,则创建一个新的对象,当获取出的对象完成它的职责之后, 再进入池子等待被下次获取。  
  
应用举例：  
创建通用对象池
``` JavaScript
const objectPoolFactory = function (createObjFn) {
  const objectPool = [];
  
  return {
    create: function (...args) {
      const obj = objectPool.length === 0 ?
        createObjFn.apply(this, args) : objectPool.shift();
      return obj;
    },
    recover: function (obj) {
      objectPool.push(obj);
    }
  }
};
```
利用objectPoolFactory来创建一个装载一些iframe的对象池
``` javascript
const iframeFactory = objectPoolFactory( function () {
  const iframe = document.createElement('iframe');
  document.body.appendChild(iframe);

  iframe.onload = function () {
    iframe.onload = null;
    iframeFactory.recover(iframe);
  }

    return iframe;
});

const iframe1 = iframeFactory.create();
iframe1.src = 'https://baidu.com';
const iframe2 = iframeFactory.create();
iframe2.src = 'https://bing.com';
```

### 总结
在一个存在大量相似对象的系统中,享元模式可以很好地解决大量对象带来的性能问题。可以用对象池+事件委托达到享元模式效果。
