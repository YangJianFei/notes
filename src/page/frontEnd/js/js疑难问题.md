### js代码没有经过人为点击更换hash值的浏览器历史不会记录。

### js原型的理解
https://yujiangshui.com/javascript-prototype-and-create-object/ 

https://yujiangshui.com/javascript-inheritance/

### js闭包
```
var db = (function() {
// 创建一个隐藏的object, 这个object持有一些数据
// 从外部是不能访问这个object的
var data = {};
// 创建一个函数, 这个函数提供一些访问data的数据的方法
return function(key, val) {
    if (val === undefined) { return data[key] } // get
    else { return data[key] = val } // set
    }
// 我们可以调用这个匿名方法
// 返回这个内部函数，它是一个闭包
})();

db('x'); // 返回 undefined
db('x', 1); // 设置data['x']为1
db('x'); // 返回 1
// 我们不可能访问data这个object本身
// 但是我们可以设置它的成员
```