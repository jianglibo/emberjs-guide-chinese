# 绘制模板

{% raw %}
路由的主要功能是选择一个合适的模板，放入合适的位置。

默认的行为是将模板绘制结果放在最近的父亲路由对应的模板中。

## 绘制其它模板，不是路由默认关联的模板

使用renderTemplate钩子。

app/routes/post.js
```javascript
export default Ember.Route.extend({
  renderTemplate() {
    this.render('favoritePost'); //不是对应的post.hbs
  }
});
```

## 使用其它控制器

app/routes/post.js
```javascript
export default Ember.Route.extend({
  renderTemplate() {
    this.render({ controller: 'favoritePost' }); //不是默认的post控制器
  }
});
```

## 有名字的槽

Ember允许对槽命名，这样可以选择将模板置入哪个槽。

app/templates/application.hbs

```handlebars
<div class="toolbar">{{outlet "toolbar"}}</div>
<div class="sidebar">{{outlet "sidebar"}}</div>
```

app/routes/post.js

```javascript
export default Ember.Route.extend({
  renderTemplate() {
    this.render({ outlet: 'sidebar' }); //将此post绘制在sidebar。
  }
});
```

## 上述个性化行为的混合

上面提到的个性化行为可以任意组合。这是一个例子：

app/routes/post.js

```javascript
export default Ember.Route.extend({
  renderTemplate() {
    var controller = this.controllerFor('favoritePost');

    // 绘制`favoritePost`模板到`post`槽, 使用`favoritePost`控制器
    this.render('favoritePost', {
      outlet: 'post',
      controller: controller
    });
  }
});
```
app/routes/post.js

```javascript
export default Ember.Route.extend({
  renderTemplate() {
    this.render('favoritePost', {   // 哪个模板
      into: 'posts',                // 绘制到哪里
      outlet: 'post',              // 用哪个槽
      controller: 'blogPost'        // 哪个控制器
    });
    this.render('comments', {
      into: 'favoritePost',
      outlet: 'comment',
      controller: 'blogPost'
    });
  }
});
```

{% endraw %}
