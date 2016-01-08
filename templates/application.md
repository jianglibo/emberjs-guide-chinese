# 顶层模板

{% raw %}
顶层模板的名称是application。在你的html页面中有一个{{content-for 'body'}}的槽，在这个槽里面嵌入这个application.hbs，然后application里面又嵌入新的模板，就像俄罗斯套娃。

```html
<header>
  <h1>Igor's Blog</h1>
</header>

<div>
  {{outlet}}
</div>

<footer>
  &copy;2013 Igor's Publishing, Inc.
</footer>
```

注意{{outlet}}，这是一个槽，由不同的url解析出来的模板来填充。

{% endraw %}
