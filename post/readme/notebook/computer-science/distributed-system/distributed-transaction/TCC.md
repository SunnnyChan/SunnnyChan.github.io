# TCC (Try-Confirm-Cancel)

## 执行阶段
* Try
```md
Try 操作一般都是锁定资源，设置预备类的状态，冻结部分数据等等此类操作。
```
* Confirm
```md
如果 Try 操作都成功，则进入 Confirm 操作阶段，完成各个服务的所有业务逻辑的执行。
```
* Cancel
```md
如果 Try 操作存在失败的情况，则进入 Cancel 操作阶段，对整个TCC分布式事务进行回滚。
```

## 可用性
* 分布式事务日志

## 应用方法
* TCC分布式事务框架
* 原本的一个接口，要改造为3个逻辑，Try-Confirm-Cancel
```md
1. 服务调用链路依次执行Try逻辑
2. 如果都正常的话，TCC分布式事务框架推进执行Confirm逻辑，完成整个事务
3. 如果某个服务的Try逻辑有问题，TCC分布式事务框架感知到之后就会推进执行各个服务的Cancel逻辑
```

## 参考
* [TCC分布式事务的实现原理](https://mp.weixin.qq.com/s?__biz=MzAwNTQ4MTQ4NQ==&mid=2453561898&idx=1&sn=95ed32b621b313b1a02be72e94ba5b4e&chksm=8cd13748bba6be5e14f26084f3f91fa0781524ec7da74442419480f4755ed881abe6de8d56bd&mpshare=1&scene=1&srcid=1207drV6397k0AYGTZTmcdOx&key=4a084d9cca9dd7f7768284374ab6c4564338fd435e6d307b9eed6e6ea52e349d0fde84a5572a34e8a26f5818a381aec5c40e17ed0269c5a7ee6ba37411cec05b32310eaba66d84a1870e2b7eedcfd759&ascene=0&uin=MjM2OTMwODMwMg%3D%3D&devicetype=iMac+MacBookPro12%2C1+OSX+OSX+10.12.5+build(16F73)&version=12010210&nettype=WIFI&lang=zh_CN&fontScale=100&pass_ticket=4NHuypRWPCD5zIKxoOl1bSqEqphrBD6h1E3mveXCTiWH6Y79%2F%2BIkhJiGaYp4%2BsBl)

