版本选择
kong 0.13.1

kong的配置全是restful的api，dashboard和kong是两套开源体系，kong官方没有提供监控页面，三方提供了12版本的UI，13版本删除了api，12版本的dashboard和13版本不兼容。 可以直接看PG等，或者自己搭建一套CURD后台管理界面。

13版本API已经废弃了，要用ROUTE（路由）和SERVICE（应用），ROUTE代表一个对外的入口，ROUTE会对应到一个SERVICE，SERVICE和ROUTE的关系是一对多的。service+routes=以前的apis

技术要求
懂一点lua

zuul和kong对比
zuul只能称得上是框架，或者叫jar包；

kong是成熟的产品，它有企业版和社区版（社区版基本包括所有应该有的功能基本都覆盖，如负载均衡、路由、权限、JWT/OAUTH2）
kong背后是Nginx,二次开发使用lua脚本修改比较方便
Nginx+lua性能非常好
kong插件式设计比较好，粒度细，灵活
kong支持跨域插件，比如前后端分离项目，如果使用zuul还需要自己去编写filter
金丝雀发布、灰度发布等一系列kong插件支持
kong插件定制灵活，可以自定义插件
认证
网关上做认证，如JWT做认证，给kong的service或者route上面加插件，如果不携带JWT，网关不放行。 kong的认证也可以选择OAUTH2的插件。kong拦在前端，需要定一下使用哪种鉴权方式。 kong属于认证服务器，还需要一个授权服务器，通常和用户表结合的比较紧。 JWT不太适合做微服务之间的认证，能做认证，不太好做授权，长远角度OAUTH2更加合适。

使用企业和功能
千米网：鉴权、路由、灰度

默认端口
nginx：80
kong: 8000
kong管理端口：8001

待开发
13版本如果不想直接看数据库的话，需要一个管控界面
