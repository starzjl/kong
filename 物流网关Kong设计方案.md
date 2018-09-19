背景
物流有两台微服务服务器，接口有两个：

根据物流订单查询物流信息接口 logistics.startdtapi.com/logistics/query/detail
订阅接口 logistics.startdtapi.com/subscribe
期望
kong的域名是 gateway.startdtapi.com，期望是所有访问kong的 logistics.startdtapi.com/logistics/query/detail（再带一些参数post的）其实都跳转到真实的微服务的controller里

方案
创建一个upstream
配置两个target指向我的微服务2台机器
创建一个service指向upstream
配置一个route，hosts[]=logistics.startdtapi.com
更为规范的方案
支持精确到controller级别的限流

创建一个upstream
配置两个target指向我的微服务2台机器
创建一个service指向upstream
配置三个route，hosts[]=logistics.startdtapi.com path[]=/subscribe ；hosts[]=logistics.startdtapi.com path[]=/A/B/C;hosts[]=logistics.startdtapi.com path[]=/D
给三个route增加plugin
