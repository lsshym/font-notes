被面到new的具体过程，心里打了个问号，做个整理
![](https://user-gold-cdn.xitu.io/2019/10/7/16da66b764362990?w=155&h=175&f=png&s=69955)
## new是干什么用的？
其实就是新建一个对象，可能有些老哥没怎么用过new，但是这一定用过
```javascript
// 字面量创建对象 
let t1 = {
    name: 'li'
}
// 用new创建对象
// 这里有个Object函数，Js自带的
let t2 = new Object();
t2.name = 'li'
```
## 为什么要用new
那么问题来了，字面量创建用的好好的，没事闲的用new创建干什么？
举个栗子

#### 字面量创建多个相似的对象
```javascript
// 创建十个对象
let obj1 = {
    num: 1,
    getNum: function() {
        console.log(this.num);
    }
}
...
// 这里有十个
let obj10 = {
    num: 2,
    getNum: function() {
        console.log(this.num);
    }
}
```
这种创建方式，就很low，每个对象的getNum都是一样的，没必要去创建10份，那怎么办，用new会怎么样?
#### new创建多个对象
```javascript
function Test(num) {
    this.num = num;
}
Test.property.getNum = function() {
   console.log(this.num) 
}
let array = [];
for(let i = 0; i < 10; i++) {
    array.push(new Test(i))
}
```
这样用new创建的方式，相当于为定制对象创建一个公共类库，每个新建出来的对象都自带这个公共类库，节省一部分内存，而且B格会高很多，至于为什么不给Object.prototype添加，个人觉得还是因为要用构造函数定制。
## new的具体过程
所以，new是怎么做到的？或者说，字面量创建的时候发生了什么，这一块网上的老哥已经分析很多了，直接上结论

1 新建一个内部对象

2 给这个对象指定一个原型链，对象的__proto__指向构造函数的prototype

3 返回这个内部对象

简单的说就是这么三步，new帮你干的，可以简单的理解new就是个语法糖，自己实现个类似new的函数，上代码
```javascript
function newFun(cont, ...args) {
    // 新建一个对象 
    let obj = {};
    // 给这个对象指定原型链，不要用obj.__proto = cont.prototype，__proto__这种写法并不是很好
    
    Object.setPrototypeOf(obj, cont.prototype)
    // 或者用 let obj = Object.create(ctor.prototype); 把上边那个obj删掉
    
    // 这一步挺神奇的，说一下自己的理解
    let result = cont.apply(obj, args)
    
    // 
    return result instanceof Object ? result : obj
}

```
`let result = cont.apply(obj, args)`其实真实的new我觉得应该不需要这步，因为这个的newFun是个函数，这一步就是把你构造函数的调用对象通过apply挂到这个内部对象上，现在构造函数内的this.num什么的都会挂到obj这个临时对象，but还有一个问题，apply会把cont这个函数执行一遍，但是apply本身，并没有返回结果，举个例子
```javascript
// 对吧，平时咱们构造函数就是这么写的
function Test() {
    this.name = '1'
}
let obj = {};
let t1  = Test.apply(obj)
console.log(t1) //undefined
```
那么问题来了，上边的这个`let result = cont.apply(obj, args)`是干什么的，result搞个undefined出来也没意义，其实跟最后一步return有关，前端的老哥们应该都知道如果在构造函数重写了return，new出来的结果可能有点变化
```javascript
function Test() {
    this.test = 666
    return { //如果return的是基础类型，那就不影响
        name: 'sixsixsix'
    }
}
console.log(new Test()) //sixsixsix
```
这时候new出来的就不是我们想要的了，这个result就是用来干这个的，如果重写了return并且返回的是一个对象，那么`cont.apply`运行结果就不是undefined，并且`return result instanceof Object ? result : obj`这个的返回结果也是重写之后的对象，没有重写return，返回的结果才是我们想要的