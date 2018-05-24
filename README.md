# 说说我认为的RESTful

我们经常谈前后端分离，谈json交互，但谈来谈去，总绕不开一个词，RESTful。那么,什么是RESTful? 

## REST(Representational State Transfer)

表现层的 状态 转移，这几个单词组合起来什么意思，听起来很拗口。确实很拗口，因为他没有主语，他缺一个Resource(主语)。

资源表现层的状态转移，下面我们把这几个单词拆开来讲。

## 资源
什么是资源？想想url是什么（统一资源定位符），url完成了由符号到实际资源的映射，能够用url表示的都是资源。

另外，资源只能是一个名词。

## 表现层的状态
什么是资源表现层的状态？

我的数据库里存了一只狗的信息，狗的信息是一个资源，他在数据库里面是二维表的形式。

我现在想获得这个信息。
```http request
GET /dogs/1 Http/1.0
Accept: application/json
```
我通过HTTP请求获得了这个狗的信息(id为1的狗)，他是一个json格式的信息，很明显，格式不同（数据库是二维表），但狗的信息还是狗的信息。

我现在又想获得这个狗的信息，但是要求变了。
```http request
GET  /dogs/1 Http/1.0
Accept: application/xml
```
我又获得了同一个信息，但是他是xml的。

相信你懂了，一个信息可以有不同的格式表示，但他们的本质是不变的。我们说这些不同的表示方法（格式）是 资源不同的表现层的状态 。

## 转移
接着上面那个例子，我从数据库一个二维表形式的信息（表现层a）,通过请求得到了一个json形式的信息(表现层b)。这个过程是什么？ 表现层的状态发生转移 。

那我们怎么实现状态转移呢？ HTTP方法。

我们通过GET方法从数据库的二维表转换到了json，也可以通过put把一个资源从json转换回数据库的二维表。同理，我们使用GET，PUT，DELETE，POST不同的方法来实现不同状态间的转移。

# 综上所述

## 什么是REST架构？
用名词形式的url表达一种资源，用不同的HTTP方法作为转换资源表现层状态的方式。

## 什么是正确的restfulAPI？
/dogs/1

/users/1 .........

它们是一个名词，能够确定一种资源。而不像/login是一个动词。

## 什么是优雅的API？
/animals/cats/1 这肯定是一个RESTful接口，但它肯定不是优雅的。

那你说如果我v0的时候只有animals这种资源，v1才扩展到cats，dogs，怎么办？

你应该 .... /v0/animals/1    /v1/dogs/1  /v1/cats/2

## 那么遵守了这种接口风格，就算REST架构吗？
不是，不是说你设计了RESTful的API,你的项目就是一个REST架构。因为REST架构还有一个很重要的点，它是无状态的（stateless）。

### 无状态是什么?

并不是说，访问一个资源有一定的权限要求，它就是有状态的。想想HTTP协议的401响应码，HTTP本身是一个无状态的协议，但它并没有排斥对某个资源的权限要求。

无状态是说，有权访问某个资源（或者说某个url）的每个客户端，你响应的资源必须是一样的，当然它可以是不同的表现层状态。

讲一个例子来理解这个问题。一个文章的资源，如/articles/1,有两个用户a和b，假如a是这篇文章的作者。那么，你可以以文章有作者访问权限为由，拒绝向b响应这个资源，这并不违反无状态；但是如果这个文章是公开的呢？你不能说，因为是a创建的文章，所以你对a的响应就和对b的响应不同。

延伸一下，为什么说REST架构一般不使用Session呢？想想你了解的Session第一个实例是干什么，购物车对吧。假设这有一个资源/userInfos/1,这个资源包含用户的购物车信息。那么，我从另一个电脑请求这个资源将不会含有购物车信息，这很明显是违背无状态的。
Session和REST架构本身并不排斥，如果你不将资源或资源的一部分存储在session中，或者你只用session来存储用户信息并通过这个信息来判断对资源的访问权限。

### 欢迎指正。
