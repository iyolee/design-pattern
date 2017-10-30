# 代理模式
### 代理模式的定义是：
> 为一个对象提供一个代用品或占位符,以便控制对它的访问。

代理模式包括许多小分类,在 JavaScript 开发中最常用的是虚拟代理和缓存代理。

### 虚拟代理
虚拟代理把一些开销很大的对象,延迟到真正需要它的时候才去创建。  
应用场景：虚拟代理合并HTTP请求：  
``` HTML
<input type="checkbox" id="1">1
<input type="checkbox" id="2">2
<input type="checkbox" id="3">3
<input type="checkbox" id="4">4
<input type="checkbox" id="5">5
<input type="checkbox" id="6">6
<input type="checkbox" id="7">7
```
如果同时点击多个checkbox频繁的网络请求可能带来相当大的开销，那么可以通过一个代理函数proxySynchronousFile来收集一段时间之内的请求,最后一次性发送给服务器。

``` JavaScript
const synchronousFile = function (id) {
  console.log('开始同步文件，id为：' + id);
};

const proxySynchronousFile = (function () {
  var cache = [], // 缓存一段时间内需要同步的id
  timer;  // 定时器

  return function (id) {
    cache.push(id);
    if (timer) {
      return;
    }

    timer = setTimeout(function () {
      synchronousFile(cache.join(','));
      clearTimeout(timer);
      timer = null;
      cache.length = 0;
    }, 2000);
  }
})();

const checkbox = document.getElementsByTagName('input');
for (let i =0, c; c = checkbox[i++];) {
  c.onclick = function () {
    if (this.checked === true) {
      proxySynchronousFile(this.id);
    }
  }
};
```

### 缓存代理
缓存代理可以为一些开销大的运算结果提供暂时的存储,在下次运算时,如果传递进来的参数跟之前一致,则可以直接返回前面存储的运算结果。  
``` javascript
const mult = function (...args) {
  return Array.of(...args).reduce((a, b) => {
    return a * b;
  }, 1);
};

const plus = function (...args) {
  return Array.of(...args).reduce((a, b) => {
    return a + b;
  }, 0);
};

const createProxyFactory = function (fn) {
  let cache = {};
  if (typeof fn !== 'function') {
    return;
  }
  return function (...args) {
    let arg = Array.prototype.join.call(args, ',');
    if (arg in cache) {
      return cache[arg];
    }
    return cache[arg] = fn.apply(this, args);
  }
}

const proxyMult = createProxyFactory(mult);
const proxyPlus = createProxyFactory(plus);

console.log(proxyMult(1, 2, 3, 4));
console.log(proxyMult(1, 2, 3, 4));
console.log(proxyPlus(1, 2, 3, 4));
console.log(proxyPlus(1, 2, 3, 4));
```

### 总结
- 保持原有 API 功能不变，改变执行过程和结果
- 不改变底层库的基础上获得不同执行结果的手段