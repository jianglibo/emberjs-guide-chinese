# 表单元素助手

{% raw %}

{{input}}和{{textarea}}助手是对Ember内置view的包裹。

* {{input}} 包裹了 Ember.TextField和Ember.Checkbox
* {{textarea}} 包裹了 Ember.TextArea

利用这些助手产生出的表单元素，和用```<input> <textarea>```写出来的“几乎一样”。

除了外观一样，额外奉献的是数据的双向绑定。

```html
{{input value="http://www.facebook.com"}}
//变成
<input type="text" value="http://www.facebook.com"/>

```
可以传入下表列出的标准的属性：
    |     |
--- | --- | ---
|`readonly`	| `required` |	`autofocus`
|`value`	|`placeholder`	|`disabled`
|`size`	|`tabindex`	|`maxlength`
|`name`|	`min`|	`max`
|`pattern`|	`accept`	|`autocomplete`
|`autosave`|	`formaction`	|`formenctype`
|`formmethod`|	`formnovalidate`	|`formtarget`
|`height`	|`inputmode`|	`multiple`|
|`step`|	`width`|	`form`|
|`selectionDirection`|	`spellcheck`|


属性的值用引号括起来的就是常量值，没有引号的就是上下文环境的属性，通常是controller里面的属性。

```html
{{input type="text" value=firstName disabled=entryNotAllowed size="50"}}
```

## 动作

同样可以发出action，语法是事件名称作为参数。

```html
{{input value=firstName key-press="updateFirstName"}}
```

这里要稍微吐槽一下，这里的action无法添加额外的参数，觉得美中不足。举一例。

```html
{{input value=firstName focus-in="fieldFocused"}}
{{input value=lastName focus-in="fieldFocused"}}
```
上面的两个输入框接受焦点的时候会触发fieldFocused方法，但是你没有办法区分是哪个触发的。


{% endraw %}
