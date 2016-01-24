# 异步的路由

准备数据是路由的一个重要功能，但数据大多不在浏览器里面，而是在服务器上。在js开发中，异步是常态，Ember必须面对这个问题，不然所有的钩子（hook）都必须写同步代码。

## Promise（承诺）简述

Promise听起来很高大上，其实概念很简单。它是一个最终会有结论的回答，可能马上给你，可能等会儿给你，也可能告诉你因为什么原因给不了。就像=符号用于赋值，Promise也有一个类似的符号（或者模式）。

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

Promise的串联特性，使得自己处理问题也不会引起语法的太大变化。
