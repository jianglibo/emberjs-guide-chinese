# 转向(变形)

{% raw %}

转向和{{link-to}}助手完成一样的功能。转向一般发生在代码中，{{link-to}}则由用户点击而动。

在route中呼叫transitionTo，在controller中呼叫transitionToRoute，即可达成转向的目标。当呼叫这两个方法时，进行中的变形就停止，开始一个新的变形。

* 静态route的变形，route的model()钩子始终执行
* 动态的route的变形，可以传入model，或者model的id。model情况下，route的model()钩子不再执行。

在能够传递model的情况下，尽量传递model，通常情况下系统的model都是单例（singleton）的，这样就能够方便的比较，或者从集合中增删。

## 在模型(model)显现之前

使用route的beforeModel钩子。

app/router.js
```javascript
Router.map(function() {
  this.route('posts');
});
```
app/routes/index.js
```javascript
export default Ember.Route.extend({
  beforeModel() { //model()钩子尚未执行
    this.transitionTo('posts');
  }
});
```

## 在模型（model）显现之后

如果重定向需要从model获取信息，然后才能决定的话。使用route的afterModel或redirect钩子。两者都是以解析出来的model为第一个参数，变形（名词）为第二参数。

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
  afterModel(posts, transition) {
    if (posts.get('length') === 1) {
      this.transitionTo('post', posts.get('firstObject'));
    }
  }
});
```
本初的目标是显示post的列表，当发现列表只有一个post的时候，转而显示这个post（详细页）了。

## 基于其它状态信息决定转向。

Ember的风格是将状态织入URL，凡事总有例外。比如，对于首页来说，默认的/路径一般没有参数。

app/router.js
```javascript
Router.map(function() {
  this.route('topCharts', function() {
    this.route('choose', { path: '/' });
    this.route('albums');
    this.route('songs');
    this.route('artists');
    this.route('playlists');
  });
});
```
这里首页对应的route是choose，这个choose路由不显示内容，它仅仅是根据程序的状态切换转向。

app/routes/top-charts/choose.js
```javascript
export default Ember.Route.extend({
  beforeModel() {
    var lastFilter = this.controllerFor('application').get('lastFilter');
    this.transitionTo('topCharts.' + (lastFilter || 'songs'));
  }
});
```
根据你最后的选择，切换到对应的页面。最后选择的是歌手，那么你访问/时，就切到歌手页面。

app/routes/filter.js
```javascript
// 这是歌手，专辑，歌曲，播放列表的父类，当这些子类被激活（activate）的时候。
export default Ember.Route.extend({
  activate() {
    var controller = this.controllerFor('application');
    controller.set('lastFilter', this.templateName); //将状态保存到application controller。
  }
});
```
## 选择性转向

在选择转向的代码中存在分支的话，没有做出转向动作的分支将按照既定方针工作。


{% endraw %}
