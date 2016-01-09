# 处理HTML的属性

{% raw %}

Ember暖心的设计再次得到验证。

处理有值得属性。
```html
<div id="logo">
  <img src={{logoUrl}} alt="Logo">
</div>

输出：

<div id="logo">
  <img src="http://www.example.com/images/logo.png" alt="Logo">
</div>
```

处理有没有的属性。
```html
<input type="checkbox" disabled={{isAdministrator}}>

isAdministrator为真：
<input type="checkbox" disabled>

isAdministrator为假：
<input type="checkbox">
```

## 添加DATA属性

默认情况下，Ember的一些辅助方法不接受data属性，比如：
```html
{{#link-to "photos" data-toggle="dropdown"}}Photos{{/link-to}}

{{input type="text" data-toggle="tooltip" data-placement="bottom" title="Name"}}
产生的html不会包含data-开头的属性。
<a id="ember239" class="ember-view" href="#/photos">Photos</a>

<input id="ember257" class="ember-view ember-text-field" type="text"
       title="Name">

```

有2中方法可以帮助添加data属性。

[重塑](object/reopen.md)Ember.LinkComponent和Ember.TextField。
```html
export default Ember.LinkComponent.reopen({
  attributeBindings: ['data-toggle']
});

export default Ember.TextField.reopen({
  attributeBindings: ['data-toggle', 'data-placement']
});
```
通过在view类中添加初始化代码，自动绑定controller中以data-开头的属性，这样更自由一些，不用将每个data属性显式的列出来。
```html
export default Ember.View.reopen({
  init() {
    this._super();
    var self = this;

    // bind attributes beginning with 'data-'
    Ember.keys(this).forEach(function(key) {
      if (key.substr(0, 5) === 'data-') {
        self.get('attributeBindings').pushObject(key);
      }
    });
  }
});
```

哪里去放置这些代码呢？yourapproot/initializer目录是其中一个地方，Ember程序会自动执行该目录下的代码文件。

{% endraw %}

