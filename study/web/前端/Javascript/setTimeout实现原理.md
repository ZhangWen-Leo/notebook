# `setTimeout`实现原理

```javascript
function showName(){
  console.log("小明")
}
var timerID = setTimeout(showName,200);
```

## 延迟队列

要执行一段异步任务，需要先将任务添加到消息队列中，但是用定时器设置的回调函数不太一样，
需要在指定的时间间隔内被调用，但消息队列中的任务是按照顺序来执行的，所以并不能将定时器的回调函数直接放在消息队列中

chrome中除了正常使用的消息队列之外，还有一个消息队列，里面维护的都是需要延迟执行的任务列表，
比如setTimeout这个任务，创建的定时器，渲染进程会将该定时器的回调任务添加到**延迟队列**中

循环执行消息队列的函数中，是**先执行普通任务，然后执行延迟任务**

## 使用setTimeout的一些注意事项

### 1. 如果当前任务执行很久，会影响定时器任务的执行

```javascript
function bar() {
    console.log('bar');
}
function foo() {
    setTimeout(bar, 0);
    for (let i = 0; i < 5000; i++) {
        let i = 5+8+8+8;
        console.log(i);
    }
}
foo();
```

### 2. 如果 setTimeout 存在嵌套调用，那么系统会设置最短时间间隔为 4 毫秒

```javascript
function cb() { 
    setTimeout(cb, 0); 
}
setTimeout(cb, 0);
```

### 3. 未激活的页面，setTimeout 执行最小间隔是 1000 毫秒

未被激活的页面中定时器最小值大于 1000 毫秒，也就是说，如果标签不是当前的激活标签，
那么定时器最小的时间间隔是 1000 毫秒，目的是为了优化后台页面的加载损耗以及降低耗电量。
这一点在使用定时器的时候要注意。

### 4. 延时执行时间有最大值

Chrome、Safari、Firefox 都是以 32 个 bit 来存储延时值的，32bit 最大只能存放的数字是 2147483647 毫秒

如果 setTimeout 设置的延迟值大于 2147483647 毫秒（大约 24.8 天）时就会溢出，
那么相当于延时值被设置为 0 ，这导致定时器会被立即执行

### 5. 使用 setTimeout 设置的回调函数中的 this 不符合直觉

```javascript
var name= 1;
var MyObj = {
  name: 2,
  showName: function(){
    console.log(this.name);
  }
}
setTimeout(MyObj.showName,1000);
```
这里输出的是 1，因为这段代码在编译的时候，执行上下文中的 this 会被设置为全局 window，
如果是严格模式，会被设置为 undefined。

- 解决方法1
```javascript
//箭头函数
setTimeout(() => {
    MyObj.showName()
}, 1000);
//或者function函数
setTimeout(function() {
  MyObj.showName();
}, 1000);
```
- 解决方法2
```javascript
setTimeout(MyObj.showName.bind(MyObj), 1000);
```


