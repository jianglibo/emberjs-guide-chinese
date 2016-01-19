# URL参数

{% raw %}

Ember对参数的处理几近完美，再也不用拼接，便利超乎想象。

用参数还是占位符？两者都是对程序状态的编码。通常直接和模型（model）关联的织入占位符，其它的织入到参数中。

```html
http://example.com/articles/5?projection=verydetail
```

上述url中，5代表了id为5的article的模型，所以使用占位符；projection描述了服务器返回的对象的详细程度，采用参数。这个规则描述起来不清晰，实际使用上还是可以鉴别的。

常见的参数有分页，过滤，排序等。

## 指定参数

参数写在路由驱动的控制器中，一旦申明，控制器中的参数值就会和URL中的值双向绑定，一端的变化会自动映射到另一端。

app/controllers/articles.js
```javascript
export default Ember.Controller.extend({
  queryParams: ['category'],
  category: null,

  filteredArticles: Ember.computed('category', 'model', function() {
    var category = this.get('category');
    var articles = this.get('model');

    if (category) {
      return articles.filterBy('category', category);
    } else {
      return articles;
    }
  })
});
```

上述代码中，申明了一个category参数。和url建立了如下关系：

* 导航到/articles，catetory的值是null，显示全部articles
* 到/articles?category=recent，category的值变成recent，articles会被过滤
* 假设页面上有一个html选择控件，不同的选择会引起catetory值得变化，那么这个变化会反过来更新url，保持一致。默认情况下，参数值的变化不会引发route的model()钩子的执行，但可以改变这个默认行为。

## {{link-to}}助手

具备上述的特征。

```handlebars
// 注意direction的只是常数，生产出来的链接是固定的。
{{#link-to 'posts' (query-params direction="asc")}}Sort{{/link-to}}

// 现在这个direction的值是变量，如果otherDirection是绑定参数，或者是通过参数计算而来的话，这个链接就会动态更新。
{{#link-to 'posts' (query-params direction=otherDirection)}}Sort{{/link-to}}
```

## 转向

和{{link-to}}助手一样。

app/routes/some-route.js
```javascript
this.transitionTo('post', object, {queryParams: {showDetails: true}});
this.transitionTo('posts', {queryParams: {sort: 'title'}});

//仅仅更新参数，route不变。
this.transitionTo({queryParams: {direction: 'asc'}});
```

## 策划一次完全的转向

所谓完全的转向，就是Ember预定的钩子逐一得到执行的转向。那么，“不完全转向”就意味着部分钩子被忽略了。

参数的变化默认导致不完全转向，它仅仅更新了控制器的参数的值，路由上的model(),setupController()等钩子都没有得到执行。

也可以通过参数的变化引发一次完全的转向。

app/routes/articles.js
```javascript
export default Ember.Route.extend({
  queryParams: {
    category: {
      refreshModel: true //确保model()钩子执行。
    }
  },
  model(params) {
    // This gets called upon entering 'articles' route
    // for the first time, and we opt into refiring it upon
    // query param changes by setting `refreshModel:true` above.

    // params has format of { category: "someValueOrJustNull" },
    // which we can just forward to the server.
    return this.store.query('articles', params);
  }
});
```

app/controllers/articles.js
```javascript
export default Ember.Controller.extend({
  queryParams: ['category'],
  category: null
});
```

上述代码，每次category变化，都会执行model(params)方法，从服务器获取数据。

## 更新url，但不纪录浏览器历史

Ember默认每次将url的变化压入浏览器历史，这样通过浏览器的回退前进按钮时，可以再现历史。可以取消这个默认。

app/routes/articles.js
```javascript
export default Ember.Route.extend({
  queryParams: {
    category: {
      replace: true
    }
  }
});
```

在{{link-to}}助手中，递入replace=true选项，也可以达成这个效果。

## url参数名称和控制器的属性名称的对应

默认是同名，可以设置成不同名。

app/controllers/articles.js
```javascript
export default Ember.Controller.extend({
  queryParams: {
    category: "articles_category"
  },
  category: null
});
//或者

export default Ember.Controller.extend({
  queryParams: [ "page", "filter", {
    category: "articles_category"
  }],
  category: null,
  page: 1,
  filter: "recent"
});
```

## 参数的默认值和解码(反序列化)

app/controllers/articles.js
```javascript
export default Ember.Controller.extend({
  queryParams: 'page',
  page: 1
});
```
行为如下：

* 参数解码成默认值一样的类型。/?page=3，控制器的page属性是数字3，而不是字符串"3"。布尔值也可以。

* 默认值不会编码(序列化)到url中。你不会看到/?page=1出现在url中。

## 参数和路由的粘连

参数会自动和路由粘连。离开某个路由的时刻的参数会被记住，当你再次回到这个路由的时候，参数还是原来的参数。

不要这么绕口，打个比方好了。你的页面左边的导航栏有一个链接：
```html
{{#link-to 'articles'}}articles{{/link-to}}
```
点击之后来到文章列表页面，然后你翻页，过滤，形成了这样一个url，```/articles?page=5&catetory=sports```。然后你离开了这个页面，进入用户管理界面。

这时，再次点击导航栏的articles按钮，你回到文章列表页面，还是第二页，还是sports。

spring容器默认注入单例（singleton）对象，估计Ember也是注入单例，既然是单例，在App的容器中，只要没有被销毁，状态自然会得以保留。

可以显式的消除这种记忆。

* link-to或者transitonTo的时候递入新的参数。
* 使用Route.resetController钩子。

既然是单例模式，下面提到的scope也很好理解了，scope是决定将参数粘连到route或者controller。如果粘连到controller，那么model的变化也不会引起参数的复位。

比如：```/articles/:id?projection=verydetail```，意思是得到一篇文章的详细。如果scope设置成controller，那么:id的虽然变化，project=verydetail，始终会粘着。

app/controllers/articles.js
```javascript
export default Ember.Controller.extend({
  queryParams: [{
    showMagnifyingGlass: {
      scope: "controller"
    }
  }]
});
```
app/controllers/articles.js

```javascript
export default Ember.Controller.extend({
  queryParams: [ "page", "filter",
    {
      showMagnifyingGlass: {
        scope: "controller",
        as: "glass",
      }
    }
  ]
});
```


{% endraw %}
