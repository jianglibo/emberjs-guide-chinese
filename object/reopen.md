# 重塑类和实例

通常情况下自己写类，都会在一处地方，一个源文件中。存在这样的情况，需要对别人写的Emberjs对象加入或修改一些内容。Emberjs提供了这个功能。

除非不重塑无法解决问题，通常不建议去重塑别人的对象，这仅仅是个人的想法。

```javascript
// add static property to class
Person.reopenClass({
  isPerson: false
});
// override property of Person instance
Person.reopen({
  isPerson: true
});

Person.isPerson; // false - because it is static property created by `reopenClass`
Person.create().get("isPerson"); // true
```

reopen的结果是所有创建出来的实例都会带上新增的属性或者方法，reopenClass添加的是静态的内容。这个上面的代码已经表达的很清楚了。


