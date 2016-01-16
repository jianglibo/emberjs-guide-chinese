# 指定路由的模型

模板需要消费模型，模型由路由提供。Ember通过钩子的方式获取指定的模型。
{% raw %}

这是一个路由的定义：
app/routes/photos.js
```javascript
export default Ember.Route.extend({
  model() {
    return [{
      title: "Tomster",
      url: "http://emberjs.com/images/about/ember-productivity-sm.png"
    }, {
      title: "Eiffel Tower",
      url: "http://emberjs.com/images/about/ember-structure-sm.png"
    }];
  }
});
```

只要在model()方法里面返回对象即可。你可以返回promise，Ember能够聪明识别。


返回promise（如果不知到promise，请bing，google，yahoo，好搜），Ember会保证模板获取的model是resolved（一个实体）。

```javascript
export default Ember.Route.extend({
  model() {
    return Ember.$.getJSON('https://api.github.com/repos/emberjs/ember.js/pulls');
  }
});
```

也可以自己resolve好。
```javascript
export default Ember.Route.extend({
  model() {
    var url = 'https://api.github.com/repos/emberjs/ember.js/pulls';
    return Ember.$.getJSON(url).then(function(data) {
      return data.splice(0, 3);
    });
  }
});
```

## 设置控制器的模型

路由将model属性加到controller去，模板通过获取controller.get('model')就可以得到model。或者直接在模板里面引用model。

## 变化中的模型

上面的代码中，model()都没有携带参数，或者说没有用到参数，考虑一下下面的URL：

```html
/photos/47
```

47是一个变化的值，我们不可能为每个值硬编码一个路由。通常是这样定义路由的。
app/router.js
```javascript
Router.map(function() {
  this.route('photo', { path: '/photos/:photo_id' });
});
```

app/routes/photo.js
```javascript
export default Ember.Route.extend({
  model(params) {
    return Ember.$.getJSON('/photos/'+params.photo_id);
  }
});
```

这个params是一个pojo对象，因为在reoute.js里面定义了占位符```:photo_id```，这个params就会携带一个photo_id的属性。

这里需要强调一下，如果你同时定义了queryParams，最好将占位符的名称和queryParams参数加以区分。两者都加入这个params中，如果重名的话，会引发问题。

你或许会去尝试下面的代码：
```javascript
Router.map(function() {
  this.route('photo', { path: '/photos/:photo_id/:another_placeholder' });
});
```

这样会发生什么情况？诚如你所想象，params会携带```photo_id, another_placeholder```两个参数，利用这两个参数可以完成model的构造。

但是，在Ember里面，路由不单单是从这个方向使用，它还会用在link-to，transitionTo，transitionToRoute等场合，此时你必须慎重考虑这个问题，可能会导致使用上的不方便。


## 关于占位符的高级话题

如果你没有开始用Ember真正写程序，请略过。

这是一个示例route的设置：
app/routes.js
```javascript
  this.route('learning', function(){
    this.route('index', {path: '/:id1/:id2'}, function() { //index route got params: id1,id2,not id3
      this.route('another', {path: '/:id3'}); //another route got id3,not id1 and id2.
    });
  });
```
导航到```/learning/65/45/3```时，就像之前一再提到的，Ember会将此URL“解码”成route。

app/routes/learning/index.js
```javascript
import Ember from 'ember';

export default Ember.Route.extend({
  model(params) {
    console.log("from learning.index route.");
    console.log(params);
    return {id: 55};
  },
  serialize(model, placeholderNames) {
    console.log('from learning.index serialize.');
    console.log(arguments.length);
    console.log(model);
    console.log(placeholderNames);
    return {id1: model.id1.id, id2: model.id2.id};
  }
});
```
此route的model(params)的params包含id1和id2，但没有id3。


app/routes/learning/index/another.js
```javascript
import Ember from 'ember';

export default Ember.Route.extend({
  setupController(controller, model) {
    controller.set("id1", {id1: 65});
    controller.set("id2", {id2: 45});
    controller.set("ids", {id1: {id: 65}, id2: {id: 45}});
    controller.set('model', model);
  },
  model(params) {
    console.log("from learning.another route.");
    console.log(params);
    return {id3: 55};
  }
});
```
此route收到的是id3，但没有id1和id2。

现在在another.hbs里面增加一行内容：
```hbs
{{#link-to "learning.index.another" ids model}}hello{{/link-to}}
```

再次提到，link-to相当于将路由“编码”成URL。但是参数的数目****不是和占位符一致，而是和路由的层级数一致****，Ember将URL“解码”成路由，每个路由处理自己的占位符。对Ember来说，这种处理规则是非常清晰的。

{% endraw %}
