# 路由简介

用户和程序交互，不停的改变程序的状态，Ember为此提供了优秀的支持。

再次强调URL在Emberjs中的重要性，程序的状态是和URL对应的。

在任何时候，程序都保有一个或多个激活的“路由处理者”，以下两个原因可以导致激活的“路由处理者”的变化：

* 用户和页面上的组件交互，产生事件导致URL变化
* 用户手动的改变URL。通过前进后退键，F5，地址栏输入并回车等

URL变化之后，新激活的“路由处理者”可以做：

* 重定向到新的URL。
* 更新控制器，来表现新的（变化的）模型。
* 更新模板，或放入新的模板到槽中。

## 日志路由的变化

当程序趋向复杂时，为了形象的了解变化，可以开启路由日志。

config/environment.js

```javascript
ENV.APP.LOG_TRANSITIONS = true;
```

## 指定程序的根路径

在一个域名上只运行一个Ember程序的话，可以从根目录开始服务。如果有多个应用，需要为每个应用配置根目录。

app/router.js
```javascript
Ember.Router.extend({
  rootURL: '/blog/'
});
```
