# Web API Design Matters

```md
该篇是我分享 Web API 设计 的PPT。先发布下文档，后续对章节做一些详细说明。
本文主要针对 RESTful API 来做讲解。
```

### Agenda  
* General
> * What’s a good API
> * Design Methodology

* RESTful
> * Code Design

### What’s a Good API  
```md
完备但不冗余（Be Complete but Minimal）
简单清晰（Be Simple & Clear）
符合直觉（Be Intuitive）
易于学习（Be Easy to Learn & Use）
可扩展（Be Extensible）
```
### Design Methodology
> * 专注于用户场景的接口设计
```md
与重点客户沟通用户场景
先基于典型用户场景编写“示例代码”和接口原型
```
> * 让用户参与开发过程
```md
第一个测试版本就“发布”给用户
尊重用户反馈，迭代改进
```
> * 满足不同层次用户的需求
```md
面向不同的Personas分析用户场景
分层设计，低层接口保证功能性、灵活性，高层接口提高易用性
```
> * 尊重用户的使用习惯
```md
设计和实现中合理应用设计模式
参考甚至兼容竞争对手的设计（不是山寨）
```
### RESTful
#### What is REST
> * Representational State Transfer
> * REST是一种软件架构模式、风格
```md
强调HTTP应当以资源为中心，并且规范了资源URI的风格
规范了HTTP请求动作（PUT，POST等）的使用，具有对应的语义
如果一个架构符合REST原则，就称它为RESTful架构
```
> * REST的初衷
```md
改变应用程序的互动风格比改变互动协议，对整体表现有更大的影响
```
> * REST的目的
```md
“建立十年内不会过时的软件系统架构”
```
#### REST - 核心思想
```md
以资源为第一位的设计方法，每一个URI代表一种资源
客户端和服务器之间，传递资源的某种表现层
客户端通过四个HTTP动词，对服务器端资源进行操作，实现“表现层状态转化”
```
#### REST - 核心概念
> * 资源（Resources）
```md
在REST中，资源可以简单的理解为URI，表示一个网络实体，
可以是一段文本、一张图片、一首歌曲、一种服务，总之就是一个具体的实在。
```
> * 表现层（Representation） 
```md
“资源”具体呈现出来的形式
文本可以用txt格式表现，也可以用HTML格式、XML格式、JSON格式表现
URI只代表资源的实体，不代表它的形式。
```
> * 状态转化（State Transfer） 
```md
服务端数据和状态的变化
客户度通过HTTP协议操作服务器，使服务器状态发生变化
转化是建立在表现层上
```
#### REST – 特点
```md
状态无关 — 确保系统的横向拓展能力，保证服务端演化
对 resource 相关的模型建立统一的原语 — 确保系统能够接纳多样而又标准的客户端 ，保证客户端演化
超文本驱动 —保证应用本身的演化
```
#### Resource Design – URI 
```md
URI代表一种资源（resource），只能有名词，对于资源的具体操作类型，由HTTP动词表示。
应该尽量将API部署在专用域名之下，如果确定API很简单，不会有进一步扩展，可以考虑放在主域名下。
```
```http
  https://api.example.com
  https://example.org/api
```
```md
应该将API的版本号放入URL，或放在HTTP头信息的Accept字段中
```
> > [https://developer.github.com/v3](https://developer.github.com/v3)

#### Using HTTP Methods
* 使用标准的HTTP方法
```md
GET（SELECT）：从服务器取出资源（一项或多项）
POST（CREATE）：在服务器新建一个资源
PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）
PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）
DELETE（DELETE）：从服务器删除资源
HEAD：获取资源的元数据
OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的
```

> * 示例 ：
```md
GET /zoos：列出所有动物园
GET /zoos/ID：获取某个指定动物园的信息
GET /zoos/ID/animals：列出某个指定动物园的所有动物
POST /zoos：新建一个动物园
PUT /zoos/ID：更新某个指定动物园的信息（提供该动物园的全部信息）
PATCH /zoos/ID：更新某个指定动物园的信息（提供该动物园的部分信息）
```

*  如果某些动作是HTTP动词表示不了的，你就应该把动作做成一种资源  
```md
网上汇款，从账户1向账户2汇款500元
POST /accounts/1/transfer/500/to/2

POST /transaction HTTP/1.1
Host: 127.0.0.1
　　
from=1&to=2&amount=500.00
```
#### Using HTTP Parameter  
* 过滤信息（Filtering）
```md
如果记录数量很多，服务器不可能都将它们返回给用户。API应该提供参数，过滤返回结果。 
```
```http
?limit=10：指定返回记录的数量
?offset=10：指定返回记录的开始位置
?page=2&per_page=100：指定第几页，以及每页的记录数
?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
?animal_type_id=1：指定筛选条件
```

### Question
* 能否在GET、HEAD、DELETE方法中使用请求内容（消息体）？

* URL路径还是参数？
```md
GET /zoo/ID/animals 与 GET /animals?zoo_id=ID
```

### HTTP Status Code  
```md
200 OK - [GET]
服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）
201 CREATED - [POST/PUT/PATCH]：
用户新建或修改数据成功。
202 Accepted - [*]
表示一个请求已经进入后台排队（异步任务）
204 NO CONTENT - [DELETE]
用户删除数据成功。
400 INVALID REQUEST - [POST/PUT/PATCH]：
用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的
401 Unauthorized - [*]
表示用户没有权限（令牌、用户名、密码错误）
403 Forbidden - [*] 
表示用户得到授权（与401错误相对），但是访问是被禁止
404 NOT FOUND - [*]
用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的
406 Not Acceptable - [GET]
用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）
410 Gone -[GET]
用户请求的资源被永久删除，且不会再得到的
422 Unprocesable entity - [POST/PUT/PATCH] 
当创建一个对象时，发生一个验证错误
500 INTERNAL SERVER ERROR - [*]
服务器发生错误，用户将无法判断发出的请求是否成功。
```

#### API Response
```md
GET /collection：
返回资源对象的列表（数组）
GET /collection/resource：
返回单个资源对象
POST /collection：
返回新生成的资源对象
PUT /collection/resource：
返回完整的资源对象
PATCH /collection/resource：
返回完整的资源对象
DELETE /collection/resource：
返回一个空文档
```

* Hypermedia API (超媒体作为应用状态引擎)
```md
提供链接，连向其他API方法，
使得用户不查文档，也知道下一步应该做什么。
```

####  REST 最佳实践
```md
1. API的身份认证应该使用OAuth 2.0框架
2. 服务器返回的数据格式，应该尽量使用JSON，避免使用XML
3. 如何定义资源？一般对应数据库实体（在传统的关系型数据库中）
4. 恰当地理解和返回Http Status（状态码）
5. 一般要求DELETE、GET、PUT 三个操作是幂等的。
```

### *注*
> 部分内容来至网络，在此表示感谢！












