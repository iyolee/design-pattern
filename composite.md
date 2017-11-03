# 组合模式

组合模式的定义是：  
> 请求以命令的形式包裹在对象中，并传给调用对象。调用对象寻找可以处理该命令的合适的对象，并把该命令传给相应的对象，该对象执行命令。 

组合模式中，当**宏命令**和普通子命令接收到执行方法的请求时,宏命令和普通子命令都会做它们各自认为正确的事情,而无需关注背后的细节。  
  
**宏命令**：宏命令是一组命令的集合,通过执行宏命令的方式,可以一次执行一批命令。  

### 组合模式应用举例
``` JavaScript
const Folder = function (name) {
  this.name = name;
  this.parent = null;
  this.files = [];
};

Folder.prototype.add = function (file) {
  file.parent = this;
  this.files.push(file);
}

Folder.prototype.scan = function () {
  console.log('开始扫描文件：' + this.name);
  for (let i = 0, file; file = this.files[i++];) {
    file.scan()
  }
};

Folder.prototype.remove = function () {
  if (!this.parent) {
    return;
  }
  for (let files = this.parent.files, i = files.length - 1; i> 0; i--) {
    let file = files[i];
    if (file === this) {
      files.splice(i, 1);
    }
  }
};

const folder = new Folder('folder');
const folder1 = new Folder('folder1');
folder.add(folder1);
folder.scan();
```

### 何时使用组合模式
- 表示对象的部分整体层次结构
- 希望统一对待树中的所有对象