# 异步的路由

准备数据是路由的一个重要功能，但数据大多不在浏览器里面，而是在服务器上。在js开发中，异步是常态，Ember必须面对这个问题，不然所有的钩子（hook）都必须写同步代码。

## Promise（承诺）简述

Promise听起来很高大上，其实概念很简单。它是一个最终会有结论的回答，可能马上给你，可能等会儿给你，也可能告诉你因为什么原因给不了。就像=符号用于赋值，Promise也有一个类似的符号（模式，套路，伎俩）。

```js
var promise = fetchTheAnswer(); //我会回答你的一个承诺

//标准模式，两个方法，前者是有答案时调用，后者是无法回答时调用。
promise.then(fulfill, reject);

function fulfill(answer/*我的答案*/) {
  console.log("The answer is " + answer);
}

function reject(reason/*无法提供答案的原因*/) {
  console.log("Couldn't get the answer! Reason: " + reason);
}
```

好玩的地方是Promise的串联，只要不停的then，所有的异步都会一一兑现，知道给出最终的答案。
```js
// Note: jQuery AJAX methods return promises
var usernamesPromise = Ember.$.getJSON('/usernames.json');//从服务器获取文件。

//然后根据usernames.json的内容，继续从其它地方获取用户的图片，然后处理图片，再上传到其它服务器，最终给你一个成功与否的答案。
usernamesPromise.then(fetchPhotosOfUsers)
                .then(applyInstagramFilters)
                .then(uploadTrendyPhotoAlbum)
                .then(displaySuccessMessage, handleErrors);
```

对于Promise需要一点时间去适应，Ember使用的Promise库是[RSVP](https://github.com/tildeio/rsvp.js)。除了Promise之外，它还有其它功用哦。

## 路由为Promise而等待，我等你

当程序切换路由时，路由通过model()钩子收集所有的数据模型。为什么要说所有的呢？因为Ember有套娃的路由。以从根到叶子的顺序执行。

Ember看到model()返回的是一个Promise，就去解答这个Promise，切换处于暂停状态，直到这个Promise解答完毕，开始执行子路由的钩子，继续直到全部得到回答。

## 当Promise承认失败的时候

可以在route的actions属性里面定义一个error方法，当Promise失败的时候，error方法会执行，同时喂给它原因。
```js
export default Ember.Route.extend({
  model() {
    return Ember.RSVP.reject("FAIL");
  },

  actions: {
    error(reason) {
      alert(reason); // "FAIL"
      // 可以转向到其它路由
      // this.transitionTo('index');

      // 可以将事件冒泡上去。
      // return true;
    }
  }
});
```
## 当Promise失败时，自己解决问题。

Promise串联的特性，使得自己处理失败也不会引起语法的太大变化。
```js
export default Ember.Route.extend({
  model() {
    return iHopeThisWorks().then(null, function() {
      // Promise失败了，但还是继续。
      // use as the route's model and continue on with the transition
      return { msg: "Recovered from rejected promise" };
    });
  }
});
```
## beforeModel 和 afterModel
{% raw %}
route的model()钩子在某些条件下不会执行，在这种情况之下，如果还是希望介入路由的某个环节，beforeModel是一个合适的地方。比如，```{{#link-to 'article' article}}， this.transitionTo('article', article)```。因为已经准备好了model的缘故。

### beforeModel

beforeModel和model一样可以返回Promise。

以下是beforeModel常见的使用场景：
* 决定是否在解析模型之前转向。比如，知道没有登陆，就没有必要费心费力取服务器取数据了。
* 判断用户是否登陆。这个可以通过reopen route加入，先让所有的route必须先登陆才允许，然后逐个放开。
* 加载其它代码。
```js
export default Ember.Route.extend({
  beforeModel() {
    if (!this.controllerFor('auth').get('isLoggedIn')) {
      this.transitionTo('login');
    }
  }
});
```

### afterModel

如果逻辑依赖于model的话，必须在afterModel里面书写。
```js
export default Ember.Route.extend({
  model() {
    // `this.store.findAll('article')` returns a promise-like object
    // (it has a `then` method that can be used like a promise)
    return this.store.findAll('article');
  },
  afterModel(articles) {
    //只有articles解析之后，我才能知道是不是只有一条记录。
    if (articles.get('length') === 1) {
      this.transitionTo('article.show', articles.get('firstObject'));
    }
  }
});
```

既然Promise可以链条化，为何不在model()钩子里面添加一个then，而是要新加一个afterModel钩子呢？前面已经提到，___model()钩子有可能不会被执行。___

{% endraw %}
