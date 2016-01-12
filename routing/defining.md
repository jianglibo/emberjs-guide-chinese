# 路由的定义

Ember程序就是根据当前的URL计算出对应的路由，由这些路由完成数据加载，模板显示，设定程序的状态。这是从URL到路由的方向，也会有路由到URL的方向。内心知道存在这样的双向性，考虑程序逻辑的时候只需要思考一个方向，从URL到路由的方向。因为反方向是一个同步的过程，是Ember自动完成的。

看一个路由的定义：
app/router.js
```javascript
Router.map(function() {
  this.route('about', { path: '/about' });
  this.route('favorites', { path: '/favs' });
});

//一样的。
Router.map(function() {
  this.route('about'); //如果path和名称一致，可以不写。
  this.route('favorites', { path: '/favs' });
});

```

在不写其它代码的情况下，根据潜规则，URL指向/about时：

* 对应的路由是/routes/about.js
* 对应的控制器/controller/about.js
* 对应的模板是/templates/about.hbs

URL指向/favs时：

* 对应的路由是/routes/favorites.js
* 对应的控制器/controller/favorites.js
* 对应的模板是/templates/favorites.hbs

可以不写这些文件，Ember也会动态产生，当然空白的页面是没有什么意思的。

指向/about，不仅仅从地址栏输入，大多数是从某个界面的某个元素操作产生。常见的是链接。

链接可不是一个简单的href，在大多数面向对象的框架中，它都是一个组件。比如，tapestry，wicket。

{% raw %}
```handlebars
{{#link-to 'index'}}<img class="logo">{{/link-to}}

<nav>
  {{#link-to 'about'}}About{{/link-to}}
  {{#link-to 'favorites'}}Favorites{{/link-to}}
</nav>
```

上面的代码中，你不用考虑程序的根目录，只要给出路由的名称即可。当然link-to所做的远不止这些，在下面更复杂的场景中会释放更多的能量。

## 套娃的路由

app/router.js
```javascript
Router.map(function() {
  this.route('posts', { path: '/posts' }, function() {
    this.route('new');
  });
});
```
这个路由器产生下列路由:
![嵌套的路由][nested-route]

这里强调一点，有的路由器对应了2个route，2个controller，两个template，到底执行哪一个呢？两个都执行！

比如：/posts/index.js和posts/new.js有共同的父，index和new输出的结果都放在posts.hbs的槽中，如果要在/posts/index和/posts/new凸显两者共有的特性，那就再posts.hbs里面作文章。

## 重置路由的名字空间
按照上面的套娃设计，会导致名称和路径过于繁琐，Ember已经考虑了这个问题。
```javascript
Router.map(function() {
  this.route('post', { path: '/post/:post_id' }, function() {
    this.route('edit');
    this.route('comments', { resetNamespace: true }, function() {
      this.route('new');
    });
  });
});
```

图：

![路由的空间重置][route-name-reset]

这些图有点复杂，作为指南请在你真正碰到问题的时候，再回头来看，来品味。

## 多词的名称
多词的名称采用驼峰法。比如，BigMac

## 路由中的占位符
app/router.js
```javascript
Router.map(function() {
  this.route('posts');
  this.route('post', { path: '/post/:post_id' });
});
```

app/routes/post.js
```javascript
export default Ember.Route.extend({
  model(params) {
    return $.getJSON("/url/to/some/posts/" + params.post_id + ".json");
  }
});
```
这里有个命名的约定，```:post_id```，这种情况下，route通过model的id获取占位符的值。通常这样就够了，如果你非要改变这个规则，可以这样：
```javascript
Router.map(function() {
  this.route('post', { path: '/posts/:post_slug' });
});
// app/routes/post.js
export default Ember.Route.extend({
  model(params) {
    // the server returns `{ slug: 'foo-post' }`
    return Ember.$.getJSON('/posts/' + params.post_slug);
  },

  serialize(model) {
    // this will make the URL `/posts/foo-post`
    return { post_slug: model.get('slug') };
  }
});

```
通过serialize钩子来实现，Ember大量使用这种钩子的方式来满足个性化的需求。

## 预置的路由

预置的路由不需要定义，直接可用：

* ```route:application```在启动时进入，绘制对应的application.hbs
* ```route:index```是程序首页（根目录）的路由。

## 带通配符的路由

就是没有定死的路由，可以自由的路由到它。我没有查看实现代码，它的表现是这样的：

app/router.js
```javascript
Router.map(function() {
  this.route('catchall', { path: '/*wildcard' });
});
```

app/routes/application.js
```javascript
export default Ember.Route.extend({
  actions: {
    error() {
      this.transitionTo('catchall', 'application-error');
    }
  }
});
```

在appliction层面定义了一个捕获所有冒泡上来的error，通过transition，程序进入catchall路由，显示的url是/application-error.


{% endraw %}

[nested-route]:{{book.imgbase}}/nested_route.png "嵌套的路由"
[route-name-reset]:{{book.imgbase}}/route_name_reset.png "路由的空间重置"
