# Vue的nextTick是个啥玩意

面试还挺爱问，做个正式的整理，以备无患，后面会直接说结论，不行的话照着背

如果哪里说的不对，麻烦直接指出下，我整理下修改

![图片没加载出来呗](https://mmbiz.qpic.cn/mmbiz_png/HCyqJSXNAiaRRYnvFMqZLvotzicOGxFibVjQFAjib9W22iaf8FoZtibtXZ1geH3Mca1k4jI4kvtibHQ42ClyhziamMBHSA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

### 使用方法

简单的很，当你更新数据后想获取dom元素时，这时候拿到的是未更新时的数据，如果想要拿到最新的数据，需要用nextTick方法，多说无益，[看代码](http://jsrun.pro/kWfKp/edit)

顺便，这时候如果你打印`this.$refs.next`在控制台找到innerText时，会发现打印的是**更改**，因为console.log打印的其实是个引用，等你点开控制台看的时候早就变成更改后的数据了，所以用console.log打印对象的时候最好转换成字符串，或者用console.table

------

### 为什么nextTick就能拿到更新的数据

当说完第一个问题，面试官就开始问我为什么nextTick就可以拿到最新的数据，问题不大

因为Vue的数据更新是异步的，这也就是为什么直接操作dom时拿不到最新数据的原因，顺带在把Vue为什么需要异步也答了，这个很好理解，当更新数据的时候dom肯定要变，当时如果做一个修改就去更新dom，讲道理性能消耗有点大，所以vue专门做了一个队列，统一处理，这一点在官网也有提及，只不过我估计很多人都没想起来。

### nextTick是怎么实现的

很好，当我自信的说出以上答案时，视频里的老哥微笑的看了看我，打出了最终一击，nextTick是怎么实现的，我知道面试官是想考我JavaScript异步的相关知识，但是，**我忘了**

![](https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=1314372085,2322740341&fm=26&gp=0.jpg)

nextTick的实现，得先了解JavaScript的异步以及Event Loop以及异步的宏任务和微任务，nextTick就是根据这个来了，[可以看下源码](https://github.com/vuejs/vue/blob/dev/src/core/util/next-tick.js)





