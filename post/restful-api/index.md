
## 一、概述：



RESTful：符合REST架构风格的网络API接口。

REST：Representational State Transfer  表现层状态转移，是一种架构约束规范和原则。

> REST本身并没有创造新的技术、组件或服务，而隐藏在RESTful背后的理念就是使用Web的现有特征和能力， 更好地使用现有Web标准中的一些准则和约束。虽然REST本身受Web技术的影响很深， 但是理论上REST架构风格并不是绑定在HTTP上，只不过目前HTTP是唯一与REST相关的实例。 所以我们这里描述的REST也是通过HTTP实现的REST。

<br/>

## 二、起源



REST这个词，是[Roy Thomas Fielding](http://en.wikipedia.org/wiki/Roy_Fielding)在他2000年的[博士论文](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)中提出的。

RESTful架构的本意是在符合架构原理的前提下，理解和评估以网络为基础的应用软件的架构设计，得到一个功能强、性能好、适宜通信的架构。（My work is motivated by the desire to understand and evaluate the architectural design of network-based application software through principled use of architectural constraints, thereby obtaining the functional, performance, and social properties desired of an architecture. ）

<br/>

## 三、Richardson Maturity Model（理查德森模型）



> 理查德森模型原地址：http://martinfowler.com/articles/richardsonMaturityModel.html





![img](https://upload-images.jianshu.io/upload_images/7143349-e62020a3891b8428.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/673/format/webp)



1）Level0（The Swamp of POX）

不算REST的一部分。

根据交换的XML内容操作所有的资源，类似[SOAP](http://www.w3school.com.cn/soap/soap_intro.asp)（SOAP 是基于 XML 的简易协议，可使应用程序在 HTTP 之上进行信息交换）。

2）Level 1 Resources

拥有了面向资源的特征，每个资源都有自己相对应的URI。

3）Level 2 HTTP verbs

使用了HTTP verbs （GET、POST、PUT、DELETE）对各种资源进行CRUD操作，使得应用程序的接口更加统一，语义更明确，遵循HTTP规范

4）Level 3 Hypermedia Controls

这一层想解决的问题是 service discoverability 和 self-documenting。

REST架构希望赋予标准的, 高度可扩展的标准语义及表现形式。RESTful API最好做到Hypermedia，即返回结果中提供链接，连向其他API方法，使得用户不查文档，也知道下一步应该做什么。​

Hypermedia API的设计被称为[HATEOAS](http://en.wikipedia.org/wiki/HATEOAS)(Hypermedia As The Engine Of Application State )，Github的API就是这种设计。大部分的RESTful接口只做到了Level2 的层次。



举例：访问[api.github.com](https://api.github.com/)会得到一个所有可用API的网址列表。

```javascript
 {
   "current_user_url": "https://api.github.com/user",
   "authorizations_url": "https://api.github.com/authorizations",
   // ...
 }
```

从上面可以看到，如果想获取当前用户的信息，应该去访问[api.github.com/user](https://api.github.com/user)，然后就得到了下面结果。

```javascript
 {
   "message": "Requires authentication",
   "documentation_url": "https://developer.github.com/v3"
 }
```

上面代码表示，服务器给出了提示信息，以及文档的网址。

<br/>

## 四、理解REST

以下通过对REST（Representational State Transfer）每个单词的解析，了解什么是REST。

### 1、资源（Resources）

REST省略了主语，即资源表现层状态转移。

所谓的"资源"，就是网络上的一个实体，或是一个具体信息，可以用URI指向它，URI是每一个资源的地址或独一无二的标识。

所谓"上网"，就是与互联网上一系列的"资源"互动，调用资源的URI。

面向资源是REST最明显的特征，对于同一资源的一组不同的操作。



### 2、表现层（Representation）

"资源"是一种信息实体，它可以有多种外在表现形式。我们把"资源"具体呈现出来的形式，叫做它的"表现层"（Representation）。

比如，文本可以用txt格式表现，也可以用HTML格式、XML格式、JSON格式表现，甚至可以采用二进制格式；图片可以用JPG格式表现，也可以用PNG格式表现。它的具体表现形式，应该在HTTP请求的头信息中用Accept和Content-Type字段指定，这两个字段才是对"表现层"的描述。



### 3、状态转移（State Transfer）

互联网通信协议HTTP协议，是一个无状态协议。这意味着，所有的状态都保存在服务器端。因此，**如果客户端想要操作服务器，必须通过某种手段，让服务器端发生"状态转化"（State Transfer）。而这种转化是建立在表现层之上的，所以就是"表现层状态转化"。**

客户端用到的手段，只能是HTTP协议。具体来说，就是HTTP协议里面，四个表示操作方式的HTTP动词：GET、POST、PUT、DELETE。

<br/>

## 五、HTTP verbs

常用的HTTP方法：GET / POST / PUT / DELETE / PATCH / HEAD，

> w3c对HTTP请求的说明：<https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html>

<br/>

> 什么是幂等？
>
> Methods can also have the property of “idempotence” in that (aside from error or expiration issues) the side-effects of N > 0 identical requests is the same as for a single request.
> ——HTTP/1.1规范中幂等性的定义
>
> ​	从定义上看，HTTP方法的幂等性是指一次和多次请求某一个资源应该具有同样的副作用。简单来说就是，同一个请求，发送一次和发送N次效果是一样的！幂等性是分布式系统设计中十分重要的概念

<br/>

### 1）GET

向指定资源请求数据，一般用来获取，查询资源信息，安全，幂等。

- 200（OK） - 表示已在响应中发出

- 204（无内容） - 资源有空表示
- 301（Moved Permanently） - 资源的URI已被更新
- 303（See Other） - 其他（如，负载均衡）
- 304（not modified）- 资源未更改（缓存）
- 400 （bad request）- 指代坏请求（如，参数错误）
- 404 （not found）- 资源不存在
- 406 （not acceptable）- 服务端不支持所需表示
- 500 （internal server error）- 通用错误响应
- 503 （Service Unavailable）- 服务端当前无法处理请求

### 2）POST

- 向指定资源提交数据进行请求处理，一般用来更新资源信息

- 不幂等（POST请求服务器执行一个动作，多次请求会多次执行）
- 请求显示在请求体里面，不会被缓存，对请求长度无限制。
- 200（OK） - 如果现有资源已被更改
- 201（created） - 如果新资源被创建
- 202（accepted） - 已接受处理请求但尚未完成（异步处理）
- 301（Moved Permanently） - 资源的 URI 被更新
- 303（See Other） - 其他（如， 负载均衡）
- 400（bad request） - 指代坏请求
- 404 （not found） - 资源不存在
- 406 （not acceptable） - 服务端不支持所需表示
- 409 （conflict） - 通用冲突
- 412 （Precondition Failed） - 前置条件失败（如执行条件更新时的冲突）
- 415 （unsupported media type） - 接受到的表示不受支持
- 500 （internal server error） - 通用错误响应
- 503 （Service Unavailable） - 服务当前无法处理请求 

### 3）PUT

通过替换的方式更新资源，不安全，幂等。

- 200 （OK） - 如果已存在资源被更改
- 201 （created） - 如果新资源被创建
- 301（Moved Permanently） - 资源的 URI 已更改
- 303 （See Other） - 其他（如， 负载均衡）
- 400 （bad request） - 指代坏请求
- 404 （not found） - 资源不存在
- 406 （not acceptable） - 服务端不支持所需表示
- 409 （conflict） - 通用冲突
- 412 （Precondition Failed） - 前置条件失败（如执行条件更新时的冲突）
- 415 （unsupported media type） - 接受到的表示不受支持
- 500 （internal server error） - 通用错误响应
- 503 （Service Unavailable） - 服务当前无法处理请求 

### 4）DELETE

不安全、幂等，删除。

- 200 （OK） - 资源已被删除
- 301 （Moved Permanently） - 资源的 URI 已更改
- 303 （See Other） - 其他， 如负载均衡
- 400 （bad request） - 指代坏请求
- 404 （not found） - 资源不存在
- 409 （conflict） - 通用冲突
- 500 （internal server error） - 通用错误响应
- 503 （Service Unavailable） - 服务端当前无法处理请求 

### 5）PATCH

不幂等，理论上讲PUT所做的更新是对整个对象来说的，一般都是将整个对象传输到后台进行整体修改，如果我只有一个字段想要改变，也要这么做，有点浪费带宽，所以出现了PATCH：**局部更新**。 

例如: Github的API则支持使用PATCH方法来进行issue的更新

```
PATCH /repos/:owner/:repo/issues/:number
```

### 5）HEAD

与GET请求类似，但是HEAD只请求页面的首部，并不返回消息体。作用：

1. 检查超链接的有效性；

2. 检查网页是否被修改；

3. 多用于自动搜索机器人获取网页的标志信息，获取rss种子信息，或者传递安全认证信息等

<br/>

## 六、总结



1）面向资源，可以唯一标识和定位资源，每一个URI代表一种资源

2）将HTTP作为了一种传输协议，最直观的是使用了HTTP规范，使用标准的HTTP方法来操作资源，GET/PUT/DELETE/POST，实现"表现层状态转移"。

3）无状态连接，服务器端不应保存过多上下文状态，每个请求应该都是独立的。

4）通过XML，JSON进行数据传递（服务器返回的数据格式，应该尽量使用JSON，避免使用XML。）

5）RESTful 架构的好处：

​       a. 互联网环境下，任何应用的架构和API容易被理解

​	b. 分布式环境下，任何请求都可以被发送到任意服务器

​	c. 异构环境下， 任何环境的访问和使用方式都统一



<br/>

## 七、参考



RESTful 架构详解：<https://www.runoob.com/w3cnote/restful-architecture.html>

RESTful 架构（阮）：<http://www.ruanyifeng.com/blog/2011/09/restful.html>

<https://www.jianshu.com/p/e77d2f60aa5d>



<br/>

## 八、Demo

新增一个简单的符合RESTful风格的SpringBoot Demo ，详见[springboot_crud_restful](<https://github.com/orrrz/springboot_crud_restful>)

