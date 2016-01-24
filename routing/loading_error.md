# 加载中...，错误子状态


## 加载中子状态

程序在后台吃力工作时，显示一个好看的加载中页面，会显得比较有档次，不管你信不信，我反正信了。除了面子问题，子状态确实能够让界面更加友好。请看下面的场景。

app/router.js
```js
Router.map(function() {
  this.route('foo', function() {
    this.route('slow-model');
  });
});
```

app/routes/foo/slow-model.js
```js
export default Ember.Route.extend({
  model() {
    return somePromiseThatTakesAWhileToResolve();
  }
});
```

当在浏览器里面输入地址```/foo/slow-model```回车，假如mode()执行需要5秒钟，那么在这5秒里面用户看到的是一个空白的页面，5秒过去之后页面突然显示，当然没有耐性等5秒的人就没有福气欣赏你的杰作了。

如果是从另一个页面点击过来，那么用户点了之后5秒钟内没有反应（一直停留在原来的页面），5秒一到，刷的就显示新的页面。如果你觉得这是带给用户一个惊喜，请原谅我跟不上你的幽默。

Ember利用潜规则引入中间状态。
app/router.js
```js
Router.map(function() {
  this.route('foo', function() {
    this.route('bar', function() {
      this.route('baz');
    });
  });
});
```
一旦Ember发现model()钩子返回的是一个Promise，就会尝试去寻找一个名称是loading的路由。从叶子到根的方向。

* foo.bar.loading
* foo.loading
* loading

从位置上来说：
* app/routes/foo/bar/loading.js
* app/routes/foo/loading.js
* app/routes/loading.js

或者没有路由，只有模板也可以。
* app/templates/foo/bar/loading.hbs
* app/templates/foo/loading.hbs
* app/templates/loading.hbs

那有路由，没模板呢？好像没什么意思，显示不了什么啊。

## 心急和拖拉的异步转换

加载中间状态是可选的，一旦存在，会改变Ember切换路由的行为。

没有加载状态的存在，界面会拖拉地停留在当前路由，直到目的路由解析完所有Promise，才离开当前路由，进入新的路由（url才得以更新）。

存在加载状态的话，当前路由会心急地退出，同时撕掉屏幕上的显示，进入子状态的路由，url马上更新到新的路由的url（不是子状态的url，子状态没有url），这时槽内显示的是加载中的模板。然后等异步的目标路由解析妥当，退出子状态进入目标状态。

关于错误。在拖拉转换下，如果下一个路由出错，页面会停留在当前的路由页面，在心急转换下，原来的界面已经撕碎，这时出错，就会停留在“加载中...”。

## loading事件

除了上面提到的子状态，在同样条件下，Ember还会发出loading事件，并且会冒泡，让自由控制loading状态得以实现。比如在页面的最上面显示一小块转啊转的。

app/routes/foo-slow-model.js
```js
export default Ember.Route.extend({
  model() {
    return somePromiseThatTakesAWhileToResolve();
  },
  actions: {
    loading(transition, originRoute) {
      //displayLoadingSpinner();显示转啊转
      this.router.one('didTransition', function () {
        // hideLoadingSpinner();隐藏转啊转
      });

      // 返回true，继续冒泡
      // or `ApplicationRoute`.
      return true;
    }
  }
});
```

如果最后一个loading处理者没有定义或者返回true，那么上面提到的子状态还是会进行。反过来说，仅仅在application里面定义了处理者，并且返回false，那么子状态就不会执行。

app/routes/application.js

```js
export default Ember.Route.extend({
  actions: {
    loading(transition, originRoute) {
      displayLoadingSpinner();

      // substate implementation when returning `true`
      return true;
    }
  }
});
```

## error子状态和事件

和loading具有对等的概念，稍有区别。
