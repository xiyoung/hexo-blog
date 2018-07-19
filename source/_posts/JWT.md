---
title: JWT
date: 2018-07-17 11:51:50
tags: 
  - jwt
  - 安全
top: 
copyright: true
comments: true
---

### 加密算法

#### 可逆

- 对称性加密算法：加密和解密使用同一个密钥，常见的有AES、DES、3DES。


- 非对称算法：密和解密所使用的不是同一个密钥，通常有两个密钥，称为"公钥"和"私钥"，它们两个必需配对使用，常见的有RSA-X系列。
- 其他，如base64。

<!--more-->

#### 不可逆

- 散列算法：是一种单向加密算法，对不同长度的输入消息，产生固定长度的输出，常用来签名，常见的有HMAC-X系列、SHA-X系列、MD5。

#### 三种常见加密算法

- SHA：作用与MD5类似



- RSA：由公钥(pub_key)和私钥(pri_key)组成，成对出现(可由工具生成)，任一方作为key加密message，可通过对方key解密得到message。


- base64：不算加密算法，只能算是一个编码算法，通常用于把二进制数据编码为可写的字符形式的数据，编码后的数据是一个字符串，其中包含的字符为：A-Z、a-z、0-9、+、/，共64个字符(其实还有一个“=“用作填充字符)
- HMAC：以一个key(密钥)和一个message(消息)为输入，结合一种加密散列函数(如MD5、SHA1等)生成一个消息摘要。形式为Hmac-X

### JWT介绍：

一种开放式的标准，用于把以json对象传输的信息通过数字签名进行验证和信任，签名方式可使用HMAC(哈希算法)或RSA(公钥/私钥)。

常见有：

| HMAC算法      | 消息摘要长度 |
| ----------- | ------ |
| Hmac-MD5    | 128    |
| Hmac-SHA1   | 160    |
| Hmac-SHA256 | 256    |
| Hmac-SHA384 | 384    |
| Hmac-SHA512 | 512    |

### 相比session认证的优点

1. session保存在内存中，用户量增多以后会占用大量内存

2. 服务器集群要做共享session--可用redis解决

3. session是基于cookie来识别用户的，cookie被劫持，服务器会受到跨站请求伪造攻击

4. 对于ios/android应用，不方便用session


### JWT的构成

- 第一部分我们称它为头部（header)

  ```javascript
  //定义一个header，例：
  {
    'typ': 'JWT',
    'alg': 'HS256'
  }
  ```

- 第二部分我们称其为载荷（payload)

  1. 标准中注册的声明

     ```javascript
     iss(Issuer): //jwt签发者
     sub(Subject): //jwt所面向的用户
     aud(Audience): //接收jwt的一方
     exp(Expiration time): //jwt的过期时间，这个过期时间必须要大于签发时间
     nbf(Not before): //定义在什么时间之前，该jwt都是不可用的.
     iat(Issued at): //jwt的签发时间
     jti(JWT ID): //jwt的唯一身份标识，主要用来作为一次性token,从而回避重放攻击。
     ```

  2. 公共的声明

     一般添加用户的相关信息或其他业务需要的必要信息，但不建议添加敏感信息，因为该部分可解密

  3. 私有的声明

     私有声明是提供者和消费者所共同定义的声明

  ```javascript
  //定义一个payload，例：
  {
    "sub": "1234567890",
    "name": "John Doe",
    "admin": true
  }
  ```

- 第三部分是签名（signature)

  1. header (base64后的)
  2. payload (base64后的)
  3. secret(也可为base64后的)------只能保存在服务器端切不可泄露

  ```javascript
  //定义一个signature，例
  // base64加密后的header和base64加密后的payload
  var encodedString = base64UrlEncode(header) + '.' + base64UrlEncode(payload);
  //这里使用HMACSHA256算法签名
  var signature = HMACSHA256(encodedString, 'secret'); 
  ```

### token应用流程

1. 登录：用户登录，输入用户名密码
2. 密码验证：服务器从数据库取出用户名和密码进行验证
3. 生成JWT：服务器端验证通过，根据从数据库返回的信息，以及预设规则，生成JWT
4. 返还JWT：服务器的HTTP RESPONSE中将JWT返还
5. 带JWT的请求：以后客户端发起请求，HTTP REQUEST HEADER中的Authorizatio字段都要有值，为JWT
6. 验证JWT：服务端验证JWT，获取用户信息，如不通过则请求异常