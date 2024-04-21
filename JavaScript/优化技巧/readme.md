### 数组优化

```javascript
// 清空数组不建议用直接赋空这种方法
array = []
array.length = 0
```

```javascript
// 链式调用
let oper = {
  add: function() {
    return this
  },
  test: function() {

  }
}
oper.add().test()
```

```javascript
// apply是将数组发方法依次传入，不是整块传入

function test(a,b,c,d) {

}
test.apply(window, [1,2,3,4])
// 而不是
function test(array) {

}
test.apply(window, [1,2,3,4])
```
```javascript
arguments不是数组，只是一个类似数组的对象
{
  '0': 1,
  '1': 2,
  '2': 3,
  length: 3
}
//arguments.callee指函数本身
//递龟函数最好用这个调用，但是严格模式不能用
var fac = function fn(num) {
  fn(num)
}
```

```javascript
函数有一个length属性，指代的设置的参数个数

function add(num1, num2) {
  if(arguments.length !== add.length)
}
```




