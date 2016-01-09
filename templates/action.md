# 动作助手

{% raw %}

将action翻译成动作，是希望和事件（event）有所区别。事件发生在爱因斯坦的量子世界，动作发生在牛顿的物理世界。

先看一段代码：

app/templates/post.hbs：
```html
<div class='intro'>
  {{intro}}
</div>

{{#if isExpanded}}
  <div class='body'>{{body}}</div>
  <button {{action 'contract'}}>收起</button>
{{else}}
  <button {{action 'expand'}}>更多...</button>
{{/if}}

```
app/controllers/post.js:
```javascript
export default Ember.Controller.extend({
  intro: Ember.computed.alias('model.intro'),
  body: Ember.computed.alias('model.body'),

  // initial value
  isExpanded: false,

  actions: {
    expand() {
      this.set('isExpanded', true);
    },

    contract() {
      this.set('isExpanded', false);
    }
  }
});
```
将action放入html的节点，该节点就具备了响应动作能力。上面的代码中没有显式指定动作类型，默认就是动词“点”，click。

当点击“收起”按钮的时候，posts.js里面的actions.contract方法得到执行，isExpanded成false，UI自动同步。

action可放入到任何html节点，有些浏览器对```<div>```，没有href属性的```<a>```等节点__不会__发出click事件，对于这种情况可以给节点加一个data-ember-action属性：
```css
[data-ember-action] {
  cursor: pointer;
}
```

## 动作的冒泡

![action的冒泡路径][action-bubbling]

从图中可以看出：

* 先查找模板对应的controller的响应代码，有就执行
* 模板对应的router，有就执行，注意this变成了该uote
* 沿着route的面包屑，从叶子开始直到applicationRoute
* 出错

冒泡的过程中，通过代码返回true，可以人为地继续冒泡。从这个冒泡的模式可以发现一些有趣的功能，这里仅举一例。

比如你的侧栏（通常是一个组件）有一个按钮，这个按钮在不同的路由下，呈现不同的功能。只需在对应的route里面加入不同的响应代码即可。

## 动作的参数
把更多的参数放在{{action}}助手后边，都会忠实的传递给响应者。
```html
<p><button {{action "select" post}}>✓</button> {{post.title}}</p>
```
```javascript
export default Ember.Controller.extend({
  actions: {
    select(post) {
      console.log(post.get('title'));
    }
  }
});
```
## 指定动作的出发事件

本章开头的比喻在这里说明，{{action}}助手就是将机器世界的术语翻译成程序所表达领域的术语。

```html
<p>
  <button {{action "select" post on="mouse-up"}}>✓</button>
  {{post.title}}
</p>
```

请你选择：

* 在代码里面处理mouse-up（鼠标点击松手事件）
* 处理“选定”

显然处理“选定”才是关注焦点。

## 指定组合键
```html
<button {{action 'anActionName' allowedKeys="alt"}}>
  click me
</button>
```
这段代码中，要触发action，必须在按下alt键的同时点击鼠标。

## 浏览器的默认行为

默然情况下，action助手拦截浏览器的原生事件。要关闭这个功能，加入```preventDefault=false```作为参数。

比如：
```html
<a href="另一个网站">用来点的文字</a>
```

在preventDefault=false的情况下，点击之后就会离开当前页面。


## 禁绝浏览器事件冒泡

在允许冒泡的情况下，如果冒泡的路上遇到多个action，所有的action都会被触发。有时候可能不是你希望的结果，可以通过bubbles=false来停止冒泡。
```html
{{#link-to 'post'}}
  Post
  <button {{action 'close' bubbles=false}}>✗</button>
{{/link-to}}
```
一个link-to里面有一个button，当✗被点击时，link-to就不会被触发了。


{% endraw %}

[action-bubbling]:{{book.imgbase}}/action-bubbling.png "action的冒泡路径"
