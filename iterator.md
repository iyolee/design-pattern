# 迭代器模式

迭代器模式的定义是：
> 指提供一种方法顺序访问一个聚合对象中的各个元素，而又不需要暴露该对象的内部表示。

迭代器是一种相对简单的模式，绝大部分语言都内置了迭代器。

### 应用场景举例
使用迭代器模式实现jQuery中$.each方法来迭代类数组和object对象：  
检测是否是类数组
```javascript
// o is not null, undefined
// o is an object
// o.length is a finite number
// o.length is non-negative
// o.length is an integer
// o.length < 2^32

function isArrayLike(o) {
  if (o && typeof o === 'object' && isFinite(o.length) && o.length >= 0 && o.length === Math.floor(o.length) && o.length < 4294967296>) {
    return true;
  } else {
    return false;
  }
}
```

``` javascript
$.each = function( obj, callback ) {
var value,i = 0,
length = obj.length,
isArray = isArraylike( obj );

if ( isArray ) {
  // 迭代类数组
  for ( ; i < length; i++ ) {
    value = callback.call(obj[i], i, obj[i]);
    if ( value === false ) {
      break;
    }
  }
} else {
  for ( i in obj ) {
    // 迭代 object 对象
    value = callback.call(obj[i], i, obj[i]);
    if ( value === false ) {
      break;
    }
  }
}
return obj;
```