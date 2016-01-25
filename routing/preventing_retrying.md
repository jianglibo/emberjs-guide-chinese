# 终止和重试转换

这里把URL的变化（对Ember来说，路由的变化和URL的变化是约等于）称作“转换”，“转换”听起来是一个动词，为了更容易表述，引入一个新的名词：变形者(transitiong)，它承载了要转换的信息。在路由的许多钩子里面会传入这个变形者，你也可以保存这个变形者，放来重新播放转换。

* transition.abort()，终止转换
* transition.retry(),重新播放转换

## 在willTransition钩子里抑制转换
{% raw %}
不管通过{{link-to}}, transitionTo,或者是人工URL的变化，对应的路由链条的willTransition钩子会被触发，可以在路由链条的任意一个决定是否允许转换。

假想一个场景，用户正在书写一篇博客，大概写了1000个字，不小心触到了浏览器的回退键，结果页面退出，1000个字就白写了，非常令人懊恼。终止转换可以消除这个尴尬。
app/routes/form.js
```js
export default Ember.Route.extend({
  actions: {
    willTransition(transition) {
      //如果用户已经输入了数据
      if (this.controller.get('userHasEnteredData') &&
          !confirm("真的要离开?")) {
        transition.abort();//终止转换。
      } else {
        // 把 `willTransition` 冒泡上去，上端的路由链条中任何一个都可以决定是否终止转换
        return true;
      }
    }
  }
});
```
## 在model，beforeModel,afterModel里终止转换
app/routes/disco.js
```js
export default Ember.Route.extend({
  beforeModel(transition) {
    //如果当前时间是1980年之前。
    if (new Date() < new Date("January 1, 1980")) {
      alert("进入此路由，你需要一台时光机。");
      transition.abort();
    }
  }
});
```
## 保存变形者以备后用

保存用户尝试登陆的路径，然后跳转到登陆页面，等用户登录之后，再回到当初尝试的页面。（这个例子出现好多次了。）
app/routes/some-authenticated.js
```js
export default Ember.Route.extend({
  beforeModel(transition) {
    //如果没有登陆
    if (!this.controllerFor('auth').get('userIsLoggedIn')) {
      var loginController = this.controllerFor('login');
      loginController.set('previousTransition', transition); //将变形者保存在login控制器中。
      this.transitionTo('login'); //转到login页面。
    }
  }
});
```
app/controllers/login.js
```js
export default Ember.Controller.extend({
  actions: {
    login() {
      // 登陆之后，发现有先前保存的变形者的话。
      var previousTransition = this.get('previousTransition');
      if (previousTransition) {
        this.set('previousTransition', null); // 不再保留变形者
        previousTransition.retry(); // 重新播放变形者承载的转化。
      } else {
        // 默认回到首页
        this.transitionToRoute('index');
      }
    }
  }
});
```

{% endraw %}
