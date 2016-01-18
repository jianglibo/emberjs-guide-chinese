# URL的类型

* hash，/#/posts/new 这样子
* /posts/new 这样子，看起来更友好，如果有服务器一侧渲染的话，SEO也ok了。

可以在主route里面定义类型：

app/router.js
```javascript
Ember.Router.extend({
  location: 'history'
});
```

用ember-cli的话，可能是这样：

```javascript
import Ember from 'ember';
import config from './config/environment';

var Router = Ember.Router.extend({
  location: config.locationType
});
```

可以设置成auto，这样Ember会帮你选择。

可以设置成none，这样URL的变化和你的程序就脱钩了。这个使用场景好像不多，除非你的ember程序仅仅接管了页面的某一块内容，而其它部分采用了其它的技术。
