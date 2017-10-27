# 策略模式
策略模式的定义是：
> 定义一系列的算法，把它们一个个封装起来，并且使它们可以相互替换。

一个基于策略模式的程序至少由两部分组成：
1. 策略类：封装了具体的算法，并负责具体的计算过程
2. 环境类Context：接受客户的请求，并将请求委托给某一个策略类，所以Context要维持对某个策略对象的引用

### 应用场景举例：  
在程序设计中，要实现某一个功能有多种方案（算法）可以选择。这些方案（算法）灵活多样，并且可以随意互相替换。  

### 简单实现策略模式
场景：根据绩效发放年终奖，绩效为S的人年终奖有4倍工资，绩效为A的人年终奖有3倍工资，而绩效为B的人年终奖是2倍工资。  
``` JavaScript
const strategies = {
  'S': function (salary) {
    return salary * 4;
  },
  'A': function (salary) {
    return salary *3;
  },
  'B': function (salary) {
    return salary * 2;
  }
}

const calculateBonus = function (level, salary) {
  return strategies[level](salary);
};

console.log(calculateBonus('S', 2000)); // 8000
console.log(calculateBonus('A', 1000)); // 3000
```

### 策略模式应用
策略模式可以用来封装算法，在实际开发中，还可以用来封装一系列的”业务规则“。只要这些业务规则指向的目标一致，并且可以被替换使用，就可以用策略模式来封装它们。  
表单校验：
- 用户名不能为空，且不能小于10位
- 密码长度不能少于6位
- 手机号码必须符合格式

代码实现：
``` HTML
<!-- HTML结构 -->
<form action="http://example.com/register" id="registerForm" method="post">
  请输入用户名：<input type="text" name="username" />
  请输入密码：<input type="text" name="password" />
  请输入手机号码：<input type="text" name="phoneNumber" />
  <button>提交</button>
</form>
```

实现策略对象：
``` javascript
const strategies = {
  isNonEmpty: function (value, errorMsg) {
    if (value === '') {
      return errorMsg;
    }
  },
  minLength: function (value, length, errorMsg) {
    if (value.length < length) {
      return errorMsg;
    }
  },
  isMobile: function (value, errorMsg) {
    if (!/(^1[3|5|7|8][0-9]{9}$)/.test(value)) {
      return errorMsg;
    }
  };
}
```
实现Context：
``` JavaScript
let Validator = function () {
  this.cache = [];
};
Validator.prototype.add = function (dom, rules) {
  var self = this;

  for (var i = 0, rule; rule = rules[i++];) {
    (function (rule) {
      var strategyAry = rule.strategy.split(':');
      var errorMsg = rule.errorMsg;

      self.cache.push(function () {
        var strategy = strategyAry.shift();
        strategyAry.unshift(dom.value);
        strategyAry.push(errorMsg);
        return strategies[strategy].apply(dom, strategyAry);
      });
    })(rule);
  }
};

Validator.prototype.start = function () {
  for (var i = 0, validatorFunc; validatorFunc = this.cache[i++];) {
    var errorMsg = validatorFunc();
    if (errorMsg) {
      return errorMsg;
    }
  }
};
```
客户调用代码：
``` JavaScript
var registerForm = document.getElementById('registerForm');

var validataFunc = function () {
  var validator = new Validator();

  validator.add(registerForm.userName, [{
    strategy: 'isNonEmpty',
    errorMsg: '用户名不能为空'
  }, {
    strategy: 'minLength',
    errorMsg: '用户名长度不能小于10位'
  }]);

  validator.add(registerForm.password, [{
    strategy: 'minLength',
    errorMsg: '密码长度不能小于6位'
  }]);

  validator.add(registerForm.phoneNumber, [{
    strategy: 'isMobile',
    errorMsg: '手机号码格式不对'
  }]);

  var errorMsg = validator.start();
  return errorMsg;
}

registerForm.onSubmit = function () {
  var errorMsg = validataFunc();

  if (errorMsg) {
    alert(errorMsg);
    return false;
  }
}
```

### 策略模式优点
- 策略模式利用组合、委托等技术思想，可以有效避免多重条件选择语句
- 策略模式中的算法也可以用在系统其他地方，可以避免许多重复的复制粘贴工作

### 策略模式缺点
- 要使用策略模式，必须了解所有的strategy及strategy之间的不同点
- 策略模式使程序中增加许多策略类或者策略对象