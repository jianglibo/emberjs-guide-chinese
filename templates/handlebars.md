# Handlebar基础

Ember使用[Handlebars templating library](http://www.handlebarsjs.com/)驱动程序的界面。

Ember拓展了Handlebar的功能，使得它更加适合于展现用户界面。一旦界面在屏幕上呈现，就再也不用编写额外的代码去维护数据的同步问题。

从我基于YUI的经验来看，Ember的模板系统几乎断绝了DOM和浏览器原生事件系统，大部分情境下，不需要操作DOM。

## 定义模板

用ember-cli的ember g template kittens命令，就会在app/templates目录中创建一个kittens.hbs文件。没什么特殊的，就像一个普通的HTML文件。
```html
<h1>小猫</h1>
<p>小猫最可爱!</p>
```

## 表达式

每个模板对应一个控制器（controller），模板从控制器获取属性。
```html
Hello, <strong>{{firstName}} {{lastName}}</strong>!
```

