# 替补者

根据Ember的结构，在页面的某个槽里面显示一块内容，需要具备：

* 路由，准备模型
* 控制器， 修饰模型
* 模板， 使用模型

比方说，在app/routes.js定义了一个路由：
app/router.js
```javascript
Router.map(function() {
  this.route('posts');
});
```

你可以不写任何代码，直接导航到/posts，路由会正常工作。从内部来说，Ember提供了默认的替补者。
