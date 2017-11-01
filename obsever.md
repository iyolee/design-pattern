# 发布—订阅模式
发布—订阅模式又叫观察者模式，它的定义是：
> 定义对象间的一种一对多的依赖关系,当一个对象的状态发生改变时,所有依赖于它的对象都将得到通知。

### 应用场景举例
实现同步滚动，引入观察者解耦：
``` JavaScript
function Editor(input, preview) {
  this.update = function () {
    preview.innerHTML = markdown.toHTML(input.value);
  };
  input.editor = this;
  this.update();
}
new Editor(editor, preview);

// 引入观察者
function setObserverBehavior(subjects){
  if(!Array.isArray(subjects)){
    if(subjects.length) subjects = Array.from(subjects);
    else subjects = [subjects];
  }
  
  subjects.forEach(function(subject){
    subject.watchBy = function(target, type){
      subject.addEventListener(type, function(evt){
        evt.sender = subject;
        evt.receiver = target;
        target.notice && target.notice(type, evt);
      });
    }
  });
}

setObserverBehavior(editor);
editor.watchBy(preview, 'scroll');
editor.watchBy(hintbar, 'scroll');

preview.notice = function(type, evt){
  var sender = evt.sender,
      receiver = evt.receiver;
  var scrollRange = sender.scrollHeight - sender.clientHeight,
      p = sender.scrollTop / scrollRange;  
  
  receiver.scrollTop = p * (receiver.scrollHeight - receiver.clientHeight);  
}

hintbar.notice = function(type, evt){
  var sender = evt.sender,
      receiver = evt.receiver;
  var scrollRange = sender.scrollHeight - sender.clientHeight,
      p = sender.scrollTop / scrollRange;  
  
  receiver.innerHTML = Math.round(100 * p) + '%';
}
```

### 发布—订阅模式作用
- 同步多个对象的状态变化
- 解耦各个对象之间的依赖