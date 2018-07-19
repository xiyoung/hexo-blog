---
title: Mocha&Chai
date: 2018-07-17 11:53:13
tags: 
    - mocha 
    - chai
top: 
copyright: true
comments: true
---
For example
```javascript
// add.test.js
const mocha = require('mocha');
const expect = require('chai').expect;

//要测试的函数
function add(x, y) {
  return x + y;
}

//测试函数 describe 表示测试套件，是一序列相关程序的测试
describe('加法函数的测试', function() {
  //it表示单元测试(unit test)，也就是测试的最小单位。
  it('1 加 1 应该等于 2', function() {
    //断言
    expect(add(1, 1)).to.be.equal(2);
  });
});
```
<!--more-->

- **Mocha**: 现在最流行的JavaScript**测试框架**之一，在`浏览器`和`Node环境`都可以使用。

  - 常见测试类型

    - 常规函数测试

    - 异步函数测试(demo05)

      - timeout类型 
      - async类
      - promise

    - http 测试

      ```javascript
      //在工程中可用chai搭配chai-htt chai.use(chai-http);

      //亦可另开启一个服务监听另一个端口 let server = app.listen(9900);
      ```

  - 常用命令行参数

    - –reporter :用来指定报告的格式

      ```javascript
      //用法：mocha --reporter spec 默认spec格式，亦可npm其他格式，如：dot matrix, nyan, mochawesome(第三方)等
      ```

    - –watch :参数用来监视指定的测试脚本。只要测试脚本有变化就自动运行mocha

    - –bail：参数指定只要有一个测试用例没有通过，就停止执行后面的测试用例

    - –grep：参数用来搜索单元测试用例的名称,然后运行符合搜索条件的测试用例,支持正则表达

      ```javascript
      //用法：mocha --grep /2/   ./demo*/*.test.js
      ```

    - –invert：参数表示只运行不符合条件的测试脚本，必须与–grep参数配合使用。

    -  --recursive

      ```javascript
      //一般如果运行mocha，会执行当前目录下的test目录的一级层级的所有js文件，但是test下的更多层级却没办法运行，这时就需要参数–recursive，这时test子目录下面所有的测试用例----不管在哪一    层----都会执行。
      ```

  - mocha的生命钩子

    - before()：在该区块的所有测试用例之前执行

    - after()：在该区块的所有测试用例之后执行

    - beforeEach()：在每个单元测试前执行

    - afterEach()：在每个单元测试后执行

      ```javascript
      describe('hooks', function () {
          let i = 1
          let j = 1
          let m = 1
          let n = 1
          before(function () {
              console.log("the " + i++ + " start")
          });

          after(function () {
              console.log("the " + j++ + " end")
          });

          beforeEach(function () {
              console.log("the " + m++ + " start")
          });

          afterEach(function () {
              console.log("the " + n++ + " start")
          });

          it('one', function (done) {
              done()
          })
          it('two', function (done) {
              done()
          })
      });

      //测试可以出现在before,after或者和你的钩子函数交替出现。钩子函数会按照它们被定义的顺序运行。一般就是，
      //before()(只运行一次)->beforeEach()->afterEach()->after()(只运行一次)。
      ```

      ​

  - 浏览器测试(demo08)

    ```javascript
    mocha init demo08
    ```

  - 配置文件mocha.opts的配置

    ```javascript
     // mocha.opts
    ```
     --recursive
     --growl
     --reporter tap
     ```

     ```

    > 关于什么时候用done()？
    >
    > 在测试异步代码的时候，需要在测试完成的时候调用一下回调函数即可。通过添加一个回调函数(通常命名为done)给it()方法，Mocha就会知道，它应该等这个函数被调用的时候才能完成测试，否则，Mocha就无法知道，测试是否结束，会一直等到超时报错。
    >
    > 摩卡测试用例时间限制？
    >
    > mocha默认每个测试用例最多执行2000毫秒，如果到时没有得到结果，就报错。所以我们在进行异步操作的时候，需要额外指定timeout时间
    >
    > ```javascript
    > mocha --timeout 5000 user.test.js
    > ```

    ​

- **Chai**: 一个比较优秀的**断言库**，包含三种断言style,即`assert`，`expect`或`should`，本文采用**expect**。所谓"断言"，就是判断源码的实际执行结果与预期结果是否一致，如果不一致就抛出一个错误。

  - 语法：==》 官网http://www.chaijs.com/api/bdd/


