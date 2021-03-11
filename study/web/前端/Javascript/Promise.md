# Promise

1. 问题引入
2. Promise

## 问题引入

### 异步编程：代码逻辑不连贯

![](images/Promise/1-1.png)

上图是一个**异步回调**的示意图，页面主线程发起一个耗时的任务，并将任务交给其他进程来处理，
页面主线程会继续执行消息队列中的任务，等该进程处理完这个任务后，会将该任务添加到渲染进程的消息队列中，等待循环系统来处理。

假设有一个XMLHTTPRequest任务：
```javascript
//执行状态
function onResolve(response){console.log(response) }
function onReject(error){console.log(error) }

let xhr = new XMLHttpRequest()
xhr.ontimeout = function(e) { onReject(e)}
xhr.onerror = function(e) { onReject(e) }
xhr.onreadystatechange = function () { onResolve(xhr.response) }

//设置请求类型，请求URL，是否同步信息
let URL = 'https://time.geekbang.com'
xhr.open('Get', URL, true);

//设置参数
xhr.timeout = 3000 //设置xhr请求的超时时间
xhr.responseType = "text" //设置响应返回的数据格式
xhr.setRequestHeader("X_TEST","time.geekbang")

//发出请求
xhr.send();
```
这段传统的五步请求方法并没有什么问题，可以正常获取结果，但是回调函数太多，造成逻辑不连贯，那么该如何解决这个问题呢？

### 封装异步代码，让处理流程变得线性

![](images/Promise/1-2.png)

1. 把输入的请求信息全部保存在一个request对象中，里面包含了请求方式，请求地址，请求头，引用地址，同步还是异步，安全设置等信息
```javascript
//makeRequest用来构造request对象
function makeRequest(request_url) {
    let request = {
        method: 'Get',
        url: request_url,
        headers: '',
        body: '',
        credentials: false,
        sync: true,
        responseType: 'text',
        referrer: ''
    }
    return request
}
```
2. 封装请求过程， 将所有的请求细节封装在一个XFetch函数中
```javascript
//[in] request，请求信息，请求头，延时值，返回类型等
//[out] resolve, 执行成功，回调该函数
//[out] reject  执行失败，回调该函数
function XFetch(request, resolve, reject) {
    let xhr = new XMLHttpRequest()
    xhr.ontimeout = function (e) { reject(e) }
    xhr.onerror = function (e) { reject(e) }
    xhr.onreadystatechange = function () {
        if (xhr.status = 200)
            resolve(xhr.response)
    }
    xhr.open(request.method, URL, request.sync);
    xhr.timeout = request.timeout;
    xhr.responseType = request.responseType;
    //补充其他请求信息
    //...
    xhr.send();
}
```
3. 业务代码
```javascript
XFetch(makeRequest('https://time.geekbang.org'),
    function resolve(data) {
        console.log(data)
    }, function reject(e) {
        console.log(e)
    })
```

### 新的问题：回调地狱

```javascript
XFetch(makeRequest('https://time.geekbang.org/?category'),
      function resolve(response) {
          console.log(response)
          XFetch(makeRequest('https://time.geekbang.org/column'),
              function resolve(response) {
                  console.log(response)
                  XFetch(makeRequest('https://time.geekbang.org'),
                      function resolve(response) {
                          console.log(response)
                      }, function reject(e) {
                          console.log(e)
                      })
              }, function reject(e) {
                  console.log(e)
              })
      }, function reject(e) {
          console.log(e)
      })
```

造成上述情况的两个原因：
1. **嵌套调用**，下面的任务依赖上个任务的请求结果，并在上个任务的回调函数内部执行新的业务逻辑。
   这样当嵌套层次多了之后，代码的可读性就变得非常差了
2. **任务的不确定性**，执行每个任务都有两种可能的结果（成功或者失败），
   所以体现在代码中就需要对每个任务的执行结果做两次判断，这种对每个任务都要进行一次额外的错误处理的方式，
   明显增加了代码的混乱程度

## Promise

### 解决上述问题

如何解决：
1. 消灭嵌套
2. 合并多个任务的错误处理

1. 引入Promise
```javascript
function XFetch(request) {
  function executor(resolve, reject) {
      let xhr = new XMLHttpRequest()
      xhr.open('GET', request.url, true)
      xhr.ontimeout = function (e) { reject(e) }
      xhr.onerror = function (e) { reject(e) }
      xhr.onreadystatechange = function () {
          if (this.readyState === 4) {
              if (this.status === 200) {
                  resolve(this.responseText, this)
              } else {
                  let error = {
                      code: this.status,
                      response: this.response
                  }
                  reject(error, this)
              }
          }
      }
      xhr.send()
  }
  return new Promise(executor)
}
```
2. 重构业务代码
```javascript
XFetch(
    makeRequest('https://time.geekbang.org/?category')
).then(value => {
    console.log(value)
    return XFetch(makeRequest('https://www.geekbang.org/column'))
}).then(value => {
    console.log(value)
    return XFetch(makeRequest('https://time.geekbang.org'))
}).catch(error => {
    console.log(error)
})
```

Promise逻辑：
1. 在实现XFetch的时候我们引入了Promise，调用XFetch就会返回一个promise对象
2. 构建promise的时候，传入一个executor回调函数，主要业务流程都放在这个函数里面
4. 在executor函数中调用resolve函数，会触发then里的回调函数，如果reject，则会调用catch方法

Promise主要通过下面两步解决了嵌套回调的问题：
1. Promise实现了回调函数的延时绑定，具体实现就是先创建Promise对象x1，
   通过Promise的构造函数excutor来执行业务代码逻辑，然后使用x1.then来设置回调函数，示例代码如下：
```javascript
//创建Promise对象x1，并在executor函数中执行业务逻辑
function executor(resolve, reject){
    resolve(100)
}
let x1 = new Promise(executor)


//x1延迟绑定回调函数onResolve
function onResolve(value){
    console.log(value)
}
x1.then(onResolve)
```
2. 需要将回调函数onResolve的返回值穿透到最外层

![](images/Promise/2-1.png)

### Promise手动实现

```javascript
function Bromise(executor) {
    this.doneList = [];
    this.failList = [];
    this.errFunc = () => {};
    this.done = (doneFunc) => {
        if (!typeof doneFunc === "function") {
            throw new Error("传入doneFunc的参数必须是函数！");
        }
        else {
            this.doneList.push(doneFunc);
        }
    };
    this.fail = (failFunc) => {
        if (!typeof failFunc === "function") {
            throw new Error("传入failFunc的参数必须是函数！");
        }
        else {
            this.failList.push(failFunc);
        }
    }
    this.then = (onResolve, onReject) => {
        this.done(onResolve || function(){});
        this.fail(onReject || function(){});
        return this;
    };
    this.catch = (errFunc) => {
        if (!typeof errFunc === "function") {
            throw new Error("传入errFunc的参数必须是函数！");
        }
        else {
            this.errFunc = errFunc;
        }
    };
    function resolve(value) {
        this.doneList.forEach(doneFunc => {
            doneFunc(value);
        });
    }
    function reject(error) {
        this.failList.forEach(failFunc => {
            failFunc(error);
        })
        this.errFunc(error);
    }

    setTimeout(() => {
        try {
            executor(resolve.bind(this), reject.bind(this))
        } catch (error) {
            this.errFunc(error);
        }
    }, 0);
}
// test
new Bromise((resolve, reject) => {
    // resolve(1);
    reject(1);
}).then((value) => {
    console.log("then1 resolve " + value);
}, (error) => {
    console.log("then1 reject " + error);
}).then((value) => {
    console.log("then2 resolve " + value);
}, (error) => {
    console.log("then2 reject " + error);
}).catch((error) => {
    console.log("catch" + error);
});
```

## 参考链接

1. [浏览器工作原理(19) - 使用promise， 告别回调函数](https://blog.csdn.net/u013448372/article/details/108674747)