# 基于 Nginx 实现并行测试场景下的流量隔离

## 背景
```md
并行开发过程中，免不了出现测试环境挣用的情况。
如果依赖一套测试环境，肯定会造成相互影响，前后端可能发生不一致。
```

## 思路
* 后端部署多个实例
```md
服务端使用不用端口，搭建多个实例来独立测试，这是必要的，但还不能完全解决问题。
首先是前端需要修改访问的端口，对代码由侵入性或者需要动态配置，前端一般情况下是不愿意配合的，
如果是通过 域名 来访问，那前端配置端口的方式也就不奏效的。
```
* Nginx 分流
```md
主要基于 Nginx 来识别流量属于哪个服务。
这就需要一个标志，通常有两种方式：
```
* > 通过Cookie分流
```md
优势：不需要对当前环境做改造

缺点：
1. PC端请求可以采用这种方式，无线端没有Cookie无法使用。
2. 一个用户需要访问不同实例时，需要修改Nginx的配置。
```

* > 通过Header分流
```md
优势：可以自动以header来实现分流，灵活

缺点：需要客户端发送请求时携带特定的header
```

## 实现
```md
实现过程，就是如何配置 Nginx，从 Cookie 中 读取信息，然后判断，最后做流量的转发。
```

* 第一步.  搭建自己的API测试实例
```md
(假设 新部署的服务实例的监听端口为 8545 8546，原来的默认端口为8541、8542)
```

* 第二步.  配置Nginx
* > 设置用户分组 
```md
创建配置文件：
conf/group.conf

写入：
```
```nginx
map $COOKIE_username $group {
    user1 group1; //登录用户名
    user2 group1;
    user3 group2;
    default default_group; //不属于任何分组的用户的请求转发至默认实例
}
```
```md
上面的配置用于判断当前的请求属于哪个分组，首先读取 Cookie 中 username 的值，然后做匹配，
如果是 user1 或 user2 则归属 group1；
如果是 user3 则归属 group2；
没有匹配项，则归属 default_group。

当然，你可以 对变量 命名为更有实际意义的 名称，方便理解。
```
* >  配置代理
```nginx
server {
  listen 8541;
  location / {
    proxy_pass "http://${group}_8541";
    proxy_set_header Host $host:$server_port;
  }
}
server {
  listen 8542;
  location / {
    proxy_pass "http://${group}_8542";
    proxy_set_header Host $host:$server_port;
  }
}
```
```md
以上配置表示分别监听 8541 和 8542 端口，
8541的所有请求转发至 ${group}_8541 代理分组，
8542 的所有请求转发至 ${group}_8542 代理分组。

${group} 的值 就是 上一步 map 之后设置的 group 的值，
如果是 group1 则转发至 group1_8541 代理分组 和 group1_8542 代理分组。
```
* >  配置转发
> conf/upstream.conf
```nginx
upstream group1_8541 {
server 127.0.0.1:8545;
}
upstream group1_8542 {
server 127.0.0.1:8546;
}
```
```md
这里就很明显了，分组 加上 端口号，转发至 不同用户的实例 + 新的 端口号。
```

> * 注意：
```md
1. 一个用户只能属于一个用户分组，测试完成后要记得注销用户分组，否则可能导致无法访问服务。
2. 前端 的不同代码版本 实际上也可以通过这种方式来配置，形成前后端是同一个项目的闭环测试。
```
