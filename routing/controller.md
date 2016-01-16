# 调教控制器

在Ember里面，model是特殊关注的数据，但不是全部。其它非焦点的数据也会大量使用，一些和model相关，一些则无关（关系不大），比如查询字符串。

Ember提供了钩子，满足对控制器的调教需求。

app/router.js
```javascript
Router.map(function() {
  this.route('post', { path: '/posts/:post_id' });
});
```

app/routes/post.js
```javascript
export default Ember.Route.extend({
  // 一下是默认的代码。不写这个代码，Ember执行的也是这个代码。
  setupController: function(controller, model) {
    controller.set('model', model);
  }
});
```

## 使用另一个控制器

添加一个controllerName属性即可。

app/routes/special-post.js
```javascript
export default Ember.Route.extend({
  controllerName: 'post' //虽然此route的名称是special-post。
});
```

## 操控另一个控制器

使用controllerFor。

app/routes/post.js
```javascript
export default Ember.Route.extend({
  setupController: function(controller, model) {
    this.controllerFor('topPost').set('model', model);
  }
});
```
