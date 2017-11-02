# 命令模式
命令模式的定义是：
> 是一种数据驱动的设计模式，它属于行为型模式。请求以命令的形式包裹在对象中，并传给调用对象。调用对象寻找可以处理该命令的合适的对象，并把该命令传给相应的对象，该对象执行命令。  

说人话就是，命令模式中的命令指的是一个执行某些特定事情的指令。  

### 命令模式使用场景
有时候需要向某些对象发送请求,但是并不知道请求的接收者是谁,也不知道被请求的操作是什么。此时希望用一种松耦合的方式来设计程序,使得请求发送者和请求接收者能够消除彼此之间的耦合关系。 
``` JavaScript
const bindClick = function ( button, func ) {
  button.onclick = func;
};
const MenuBar = {
  refresh: function(){
    console.log( '刷新菜单界面' );
  }
};
const SubMenu = {
  add: function(){
    console.log( '增加子菜单' );
  },
  del: function(){
    console.log( '删除子菜单' );
  }
};
bindClick(button1,MenuBar.refresh);
```  

命令模式在JavaScript中似乎看不出和平时使用的有什么不同，因为JavaScript作为将函数作为一等对象的语言, 命令模式也早已融入到了JavaScript语言之中。