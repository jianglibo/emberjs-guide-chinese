# 依赖于集合的算计属性

todo是一个容易理解的例子。想象这样一个页面，上面是任务的列表，下方有一个统计栏，有总共多少，已完成多少，还剩多少等信息。从程序的角度来看，有一个数组来保存任务，每个任务对象都有一个isDone属性。

```javascript
export default Ember.Controller.extend({
  todos: [
    Ember.Object.create({ isDone: true }),
    Ember.Object.create({ isDone: false }),
    Ember.Object.create({ isDone: true })
  ],

  remaining: Ember.computed('todos.@each.isDone', function() {
    var todos = this.get('todos');
    return todos.filterBy('isDone', false).get('length');
  })
});
```

注意这里的remaining属性，todos.@each.isDone，它能够在下列事件发生时，自动更新remaining的值。

* 任何一个todo的isDone属性发生变化
* 添加一个任务到todos
* 从todos中移除一个todo
* 整个todos被替换。

***注意，这种观察仅限于1个层级，todos.@each.creator.name，这样不行***
