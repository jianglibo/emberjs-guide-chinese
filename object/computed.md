# 算计的属性

在其它语言中，也有“虚拟属性”的叫法。它的值是通过计算得到的，因此得名。

该属性可以嵌套，或者说链化。

## 简单的示例

```javascript
Person = Ember.Object.extend({
  // these will be supplied by `create`
  firstName: null,
  lastName: null,
  nothingRelateToFullName: 'n',
  fullName: Ember.computed('firstName', 'lastName', function() {
    return this.get('firstName') + ' ' + this.get('lastName');
  })
});

var ironMan = Person.create({
  firstName: "Tony",
  lastName:  "Stark"
});

ironMan.get('fullName'); // "Tony Stark"

```

fullName通过计算firstName和lastName的值得到最终的值。细说一下这个语法，如果这样写：

```javascript
  fullName: Ember.computed(function() {
    return this.get('firstName') + ' ' + this.get('lastName');
  })

```
fullName的值还是一样的。写成第一种语法，正是Emberjs精髓的地方。它表示一旦firstName或者lastName或者两者一起变化的时候，fullName就会变化。如果fullName和模板中的UI对象绑定的话，就会自动同步。

## 链化的属性

如果还有一个字段description，依赖于fullName，那么firstName的变化就会传递下去直到末端。

## 设置算计的属性

```javascript
Person = Ember.Object.extend({
  firstName: null,
  lastName: null,

  fullName: Ember.computed('firstName', 'lastName', {
    get(key) {
      return this.get('firstName') + ' ' + this.get('lastName');
    },
    set(key, value) {
      var [ firstName, lastName ] = value.split(/\s+/);
      this.set('firstName', firstName);
      this.set('lastName',  lastName);
    }
  })
});


var captainAmerica = Person.create();
captainAmerica.set('fullName', "William Burnside");
captainAmerica.get('firstName'); // William
captainAmerica.get('lastName'); // Burnside
```

将computed的最后一个参数从function变成一个具有get，set的对象，就具备了set功能。

不一定非得如此，最后一个参数是function也可以具备set功能，不过要通过参数的个数来决定是get还是set。
```javascript
Person = Ember.Object.extend({
  firstName: null,
  lastName: null,

  fullName: Ember.computed('firstName', 'lastName', function() {
    var argslen = arguments.length;
    if (argslen === 1) {
      return this.get('firstName') + ' ' + this.get('lastName');
    } else {
      var [ firstName, lastName ] = arguments[1].split(/\s+/);
      this.set('firstName', firstName);
      this.set('lastName',  lastName);
    }
  })
});


var captainAmerica = Person.create();
captainAmerica.set('fullName', "William Burnside");
captainAmerica.get('firstName'); // William
captainAmerica.get('lastName'); // Burnside
```
