# 调试助手

{% raw %}
Handlebars和Ember内置了一些辅助工具帮助发现模板的问题。通过将内容输出到浏览器的控制台或者从模板中激活调试，触发断点。

## 日志

可以在模板中使用{{log}}表达式输出内容。
```html
{{log 'Name is:' name}}
```

## 设置调试断点

在模板的适当位置假如{{debugger}}，程序会在该位置停顿。此时，你可以通过浏览器控制台获取上下文的变量的值。

```console
> get('foo')
```

在{{#each}}中使用：
```html
{{#each items as |item|}}
  {{debugger}}
{{/each}}
```

可以这样查看值：
```console
> get('item.name')
```

还可以查看整个上下文环境对象：
```console
> context
```

{% endraw %}
