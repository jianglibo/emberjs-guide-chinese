# 在模板里输出模板的助手

{% raw %}
有时候会有一些模板内容需要不断重复，或者基于代码清晰的考虑，Emer提供了相应的方法。

## {{partial}}助手

{{partial}}以模板名称作为参数，将该模板绘制在当前位置，***不改变上下文和作用域***。

app/templates/author.hbs
```html
Written by {{author.firstName}} {{author.lastName}}
```
app/templates/post.hbs
```html
<h1>{{title}}</h1>
<div>{{body}}</div>
{{partial "author"}}
```
输出：
```html
<div>
  <h1>Why You Should Use Ember.js</h1>
  <div>Because it's awesome!</div>
  Written by Yehuda Katz
</div>
```

## {{render}}助手

{{render}}的参数：

* 首参数描述上下文
* 可选的第二参数是model，会传递给模板对应的controller，如果存在的话。

{{render}}干了：
* 没有model提供，供给模板的是对应的单例（singleton）controller
* 提供了model，供给模板的是唯一new出来的对应的controller
* 用上面两种方式之一获取的controller绘制模板。
* 设置controller的model

举例：
app/templates/author.hbs
```html
作者： {{firstName}} {{lastName}}.
发帖数: {{postCount}}
```
app/templates/post.hbs
```html
<h1>{{title}}</h1>
<div>{{body}}</div>
{{render "author" author}}
```
app/controllers/author.js

```javascript
export default Ember.Controller.extend({
  postCount: Ember.computed('model.posts.[]', function() {
    return this.get('model.posts.length');
  })
})
```
代码解释：

* {{render "author" author}}，潜规则作用下，模板就是author.hbs

* 产生一个AuthorControllr，看author.js。
* 设置controller的model，这里是author，因为提供了第二参数
* 用上述步骤产生的controller指挥绘制，换句话说就是以该controller为上下文。

和槽{{outlet}}相比，{{render}}不需要对应的route，但是从内部处理机制是一致的，差异实在环境的准备步骤和来源。

对比表：
### 类似的
Helper |	Template	|	Model	|	Controller
---- |	----	|	----	|	----
{{partial}}	|	Specified |	 Template |		Current Model	Current Controller
{{render}}	|	Template	|	Specified Model	|	Specified Controller

### 差异的

Helper |	Template | Model |Controller
---- |	----	|	----	|	----
{{partial "author"}} | templates/author.hbs |	models/post.js | controllers/post.js
{{render "author" author}} | templates/author.hbs	| models/author.js	| controllers/author.js


{% endraw %}
