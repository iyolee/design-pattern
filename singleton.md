# 单例模式
### 单例模式的定义是：  
> 保证一个类仅有一个实例，并提供一个访问它的全局访问点。

### 应用场景举例：  
点击登录按钮时，页面会出现一个登录浮窗，而这登录浮窗是唯一的，无论点多少次登录按钮，这个浮窗只被创建一次，该登录浮窗就适合使用单例模式来创建。  

### 简单实现单例模式：
``` JavaScript
const Singleton = function (name) {
  this.bame = name;
  this.instance = null;
}

Singleton.prototype.getName = function () {
  console.log(this.name);
};

Singleton.getInstance = function (name) {
  if (!this.instance) {
    this.instance = new Singleton(name);
  }
  return this.instance;
};

const foo = Singleton.getInstance('lee');
const bar = Singleton.getInstance('leeper');

console.log(foo === bar);
```

### 创建通用单例模式
``` JavaScript
const getSingle = function (fn) {
  let result;
  return function (...args) {
    return result || (result = fn.apply(this, ...args));
  }
};
```
``` getSingle ```使用例子：
``` JavaScript
const createLoginLayer = function () {
  let oDiv = document.createElement('div');
  oDiv.innerHTML = '登录浮窗';
  oDiv.style.display = 'none';
  document.body.appendChild(oDiv);
  return oDiv;
}

const createSingleLoginLayer = getSingle(createLoginLayer);

document.getElementById('loginBtn').onclick = function () {
  let loginLayer = createSingleLoginLayer();
  loginLayer.style.display = 'block';
}
```