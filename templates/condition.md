# 分支语法

{% raw %}

有时候模板内容需要根据属性是否存在（有/无，真/假，空/不空）来决定显示的内容。
```html
{{#if person}}
  欢迎回来, <b>{{person.firstName}} {{person.lastName}}</b>!
{{/if}}
```
上面的模板中，如果person属于false,undefined,null,[]，都不会显示内容。

```html
{{#if person}}
  欢迎回来, <b>{{person.firstName}} {{person.lastName}}</b>!
{{else}}
  请登录
{{/if}}

```

还可以链状的书写。
```html
{{#if isAtWork}}
  Ship that code! 不知道什么意思
{{else if isReading}}
  You can finish War and Peace eventually...
{{/if}}
```

还可以用unless反向，除非是真的，否则就输出。
```html
{{#unless hasPaid}}
  You owe: ${{total}}
{{/unless}}
```

{{#if}} {{#unless}}属于块状表达式，用#开头，需要对应的关闭标签。

{% endraw %}
