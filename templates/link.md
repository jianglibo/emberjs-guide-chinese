# 链接助手

{% raw %}

Ember的链接输出类似html的```<a>```元素，a元素和url相关，就像在前面的章节中提到的，Ember将url看作一等公民，非常重要的角色。它实际达成的功能和controller的transitonToRoute以及route的transitionTo是一样的，它是在模板中呈现，而且从名称到语法都符合大多数人的习惯。

```javascript
Router.map(function() {
  this.route("photos", function(){
    this.route("edit", { path: "/:photo_id" });
  });
});
```

```html
<ul>
  {{#each photos as |photo|}}
    <li>{{#link-to 'photos.edit' photo}}{{photo.title}}{{/link-to}}</li>
  {{/each}}
</ul>
```

```html
<ul>
  <li><a href="/photos/1">Happy Kittens</a></li>
  <li><a href="/photos/2">Puppy Running</a></li>
  <li><a href="/photos/3">Mountain Landscape</a></li>
</ul>
```

从上面的代码可以看到，link-to不是一个孤立的对象，它指向一个存在的route(路径，url)。从上面的route定义中，可以想象出url的样子，approot/photos/1。那么这个1从哪里来，根据潜规则，来自photo的id属性。

也可以直接传入id值```#link-to 'photos.edit' 1```，产生同样的模板输出。但是两者是有差异的，会改变模板对应的route，controller的处理流程。

这里就讲一个差异，如果传入photo，那么对应route的model()钩子就不再执行，model()一般是从服务器获取photo对象。


## route里面有更多的占位符呢？
app/router.js：
```javascript
Router.map(function() {
  this.route("photos", function(){
    this.route("photo", { path: "/:photo_id" }, function(){
      this.route("comments");
      this.route("comment", { path: "/comments/:comment_id" });
    });
  });
});
```
app/templates/photo/index.hbs：
```html
<div class="photo">
  {{body}}
</div>

<p>{{#link-to 'photos.photo.comment' primaryComment}}Main Comment{{/link-to}}</p>
```
这里的link-to只给了一个占位符的值，该值赋给最末端的占位符，那么```:photo_id```的值从哪里来呢？首先根据route定义，推算出显示上面模板的路径应该是/photos/1，是的从这里来。

也可以直接将占位符参数给齐了。
```html
<p>
  {{#link-to 'photo.comment' 5 primaryComment}}
    Main Comment for the Next Photo
  {{/link-to}}
</p>
```

## 不用块状语法，使用内嵌语法

```html
A link in {{#link-to 'index'}}Block Expression Form{{/link-to}},
and a link in {{link-to 'Inline Form' 'index'}}.
```
内嵌情况下，第一个参数成了link-to的显示文本。

## 递入额外的参数
```html
<p>
  {{link-to 'Edit this photo' 'photo.edit' photo class="btn btn-primary"}}
</p>
```

加上link-to生产的属性之外，html<a>元素可以使用的属性。

## 覆盖浏览器的历史纪录

加上replace=true即可。
```html
<p>
  {{#link-to 'photo.comment' 5 primaryComment replace=true}}
    Main Comment for the Next Photo
  {{/link-to}}
</p>
```


{% endraw %}
