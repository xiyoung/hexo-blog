---
title: Promise的实现
date: 2018-07-16 15:53:22
comments: true #是否可评论
toc: true #是否显示文章目录
categories: es6 #分类
top: true
tags:   #标签
    - promise
copyright: true #显示版权信息
---

#### 准备工作

- 回调函数

  ```javascript
  //回掉函数的参数为常量
  function Cc_1 (callback){
      callback(50);
  }
  new Cc_1(function(a){
      console.log(a)
  })

  //回调函数的参数为方法
  function Cc_2(callback) {
      function resolve(value) {
          console.log(value)
      }
      callback(resolve)
  }
  new Cc_2(function(a){
      a(100)
  })
  ```
<!--more-->

- 函数的链式调用

  ```javascript
  var obj = {
      step1:function(){
        console.log('a');
        return this;
      },
      step2:function(){
        console.log('b');    
        return this;
      },
      step3:function(){
        console.log('c');
        return this;
      },
      step4:function(){
        console.log('d');
        return this;
      }
    }
    
    console.log('-----\n');
    obj.step1().step2().step3();
    console.log('-----\n');
    obj.step4().step2().step1();
  ```

#### Promise极简雏形

```javascript
function Promise(fn){
  var callback;
  this.then = function(done){
    callback = done;//储存then传入的回调函数
  }
  function resolve(value){
    callback(value);//执行回调函数
  }
  fn(resolve);
}

new Promise((resolve) => {
        setTimeout(() => {
            resolve(50)
        },0)
    })
    .then((a) => {
        console.log(a)
	})
//问题1：当promise实例处理的不是一个异步函数的时候，会报错，原因是执行回调函数的代码比储存回调函数的代码		 先执行
//问题2：无法链式调用
//问题3：没有状态，无法控制状态的单向转化
//问题4：链式传值
//问题5：不能串行执行promise
//问题6：失败处理(reject)
......
......
```

#### 解决问题 1 2 --异步执行回调函数 链式调用

```javascript
function Promise(fn) {
    var promise = this,
        value = null;
        promise._resolves = []; //初始化储存then中回调函数的数组

    this.then = function (onFulfilled) {
        promise._resolves.push(onFulfilled);
        return this; //返回当前promise对象
    };

    function resolve(value) {
      	setTimeout(() => { //确保then中的回调函数已经被注册
            promise._resolves.forEach(function (callback) {//依次执行
            callback(value);
        	});
        },0)
       
    }
    fn(resolve);
}

new Promise(function(resolve){
        setTimeout(() => {
            resolve(50)
        },0)
    })
    .then(function (a) {
        console.log(a)
        return 2*a;
    })
    .then(function(b){
        console.log(b)
    })
```

#### 解决问题 3 4 -- 加入状态 链式传值

```javascript
function Promise(fn) {
    var promise = this,
        value = null;
        promise._resolves = [];
        promise._status = 'PENDING';//初始状态

    this.then = function (onFulfilled) {
        if (promise._status === 'PENDING') {
            promise._resolves.push(onFulfilled);
            return this;
        }
        onFulfilled(value);//如果不是初始状态则直接执行then中的回调函数
        return this;
    };


  function resolve(value) {
        setTimeout(function(){
            promise._status = "FULFILLED";
            promise._resolves.forEach(function (callback) {
                value = callback(value); //将回调函数的返回值赋给value
            })
        },0);
    }

    fn(resolve);
}

new Promise(function(resolve){
        setTimeout(() => {
            resolve(50)
        },0)
    })
    .then(function (a) {
        console.log(a)
        return 2*a;
    })
    .then(function(b){
        console.log(b)
    })
```

#### 解决问题	5 串行执行promise

```javascript
function Promise(fn) {
    var promise = this,
        value = null;
        promise._resolves = [];
        promise._status = 'PENDING';//初始状态
   //then函数改变有二：一是then函数不再返回this，而是一个新的promise实例；
                    //二是_resolves数组中储存的不再是回调函数了，而是handle函数，这个handle函数对上一                       个 promise 的 then 中的回调函数进行了处理，并调用新的promise实例中的resolve方                       法，以便处理then 中的回调函数返回值为Promise的情况
   this.then = function (onFulfilled) {
        return new Promise(function(resolve) {
            function handle(value) {
                var ret = typeof onFulfilled === 'function' && onFulfilled(value) || value;
                if( ret && typeof ret ['then'] == 'function'){//如果then回调函数中返回的是promise对象
                    ret.then(function(value){//执行promise对象使其状态变为非'pending'状态
                       resolve(value);
                    });
                } else {
                    resolve(ret);
                }
            }
            if (promise._status === 'PENDING') {
                promise._resolves.push(handle);
            } else if(promise._status === 'FULFILLED'){
                handle(value);
            }
        })
    }; 


  function resolve(value) {
        setTimeout(function(){
            promise._status = "FULFILLED";
            promise._resolves.forEach(function (callback) {
                value = callback(value); //将回调函数的返回值赋给value
            })
        },0);
    }

    fn(resolve);
}

new Promise(function(resolve){
        setTimeout(() => {
            resolve(50)
        },0)
    })
    .then(function (a) {
        console.log(a)
        return new Promise(function(resolve){
            setTimeout(() => {
                resolve(2*a)
            },0)
        });
    })
    .then(function(b){
        console.log(b)
    })
```

#### 其他问题

- 失败处理(reject) 以及实现 .catch()
- 封装Promise静态方法  
  - Promise.all()
  -  Promise.race()
  - Promise.resolve()
  - Promise.reject()



```javascript
function fn1(resolve, reject) {
    setTimeout(function() {
        console.log('步骤一：执行');
        resolve('1');
    },500);
}
function fn2(data) {
    console.log(data)
    return data;
}
function fn3(data) {
    console.log(data)
}

new Promise(fn1).then(fn2).then(fn3)

//这段代码的执行过程：

//首先我们创建了一个 Promise 实例，这里叫做 promise1；接着会运行 fn1(resolve);

//但是 fn1 中有一个 setTimeout 函数，于是就会先跳过这一部分，运行后面的第一个 then 方法;

//then 返回一个新的对象 promise2,  promise2 对象的 resolve 方法和 then 方法的中回调函数 fn2 都被封装在 handle 中， 然后 handle 被添加到 　　promise1._resolves 数组中。

//接着运行第二个 then 方法，同样返回一个新的对象 promise3, 包含 promise3 的 resolve 方法和 回调函数 fn3 的 handle 方法被添加到 promise2._resolves 数组中。

//到此两个 then 运行结束。 setTimeout 中的延迟时间一到，就会调用 promise1的 resolve方法。

//resolve 方法的执行，会调用 promise1._resolves 数组中的回调，之前我们添加的 handle 方法就会被执行； 也就是 fn2 和 promsie2 的 resolve 方法，都被调用了。

//以此类推，fn3 会和 promise3 的 resolve 方法 一起执行，因为后面没有 then 方法了，promise3._resolves 数组是空的 。

//至此所有回调执行结束
```

