# 观察者

Ember支持观察任何属性，包括算计的属性，前提是该属性是在Ember.Object以及继承自Ember.Object的对象上。

```javascript
Person = Ember.Object.extend({
  // these will be supplied by `create`
  firstName: null,
  lastName: null,

  fullName: Ember.computed('firstName', 'lastName', function() {
    var firstName = this.get('firstName');
    var lastName = this.get('lastName');

    return firstName + ' ' + lastName;
  }),

  fullNameChanged: Ember.observer('fullName', function() {
    // fullName变化的时候，在这里应对
  })
});

var person = Person.create({
  firstName: 'Yehuda',
  lastName: 'Katz'
});

person.set('firstName', 'Brohuda'); // 此动作会触发观察者
```

## 观察和异步

到目前为止，Ember的观察是同步方式，观察目标一变化，观察代码立即执行，会引起同步问题。

```javascript
Person.reopen({
  lastNameChanged: Ember.observer('lastName', function() {
    // 这里观察着lastName，其中另一个属性fullName也是依赖于lastName的计算属性，因为观察是同步的，所以下面的log会记录还没有变化fullName的值。
    console.log(this.get('fullName'));
  })
});
```
在观察多个属性的情况下，还会多次触发观察代码。
```javascript
Person.reopen({
  partOfNameChanged: Ember.observer('firstName', 'lastName', function() {
    // 因为同时观察了firstName和lastName，这段代码会执行2次，通常情况下这种多次执行是没有必要的。
  })
});

person.set('firstName', 'John');
person.set('lastName', 'Smith');
```
为消除这种情况，可以使用Ember.run.once。可以确保代码只执行一次，并且发生在所有的绑定同步之后。
```javascript
Person.reopen({
  partOfNameChanged: Ember.observer('firstName', 'lastName', function() {
    Ember.run.once(this, 'processFullName');
  }),

  processFullName: Ember.observer('fullName', function() {
    // 现在代码只会执行一次。
    console.log(this.get('fullName'));
  })
});

person.set('firstName', 'John');
person.set('lastName', 'Smith');
```

## 观察和实例的初始化

观察代码在实例初始化完成之后才会触发。

如果需要在初始化过程中引发观察代码，不可依赖于set行为，使用Ember.on去执行初始化结束之后的代码。
```javascript
Person = Ember.Object.extend({
  init: function() {
    this.set('salutation', 'Mr/Ms');
  },

  salutationDidChange: Ember.on('init', Ember.observer('salutation', function() {
    // some side effect of salutation changing
  }))
});
```

## 未被消费的计算属性不会触发观察者

在没有获取（get）计算属性的情况下，观察于此属性的代码不会被触发。
“姓名”是一个计算的属性，依赖于“姓”和“名”，在“姓”或者“名”变化的情况下，如过没有获取“姓名”的值，那么对于“姓名”的观察代码就不会执行。

在通常情况下，计算的属性总是被消费的，比如你把一个计算属性的值显示在UI上，就包含了get动作。

若需要观察没有被消费的属性，可以在实例的init中get一下。

## 在类定义之外书写观察代码
```javascript
person.addObserver('fullName', function() {
  // deal with the change
});

```
