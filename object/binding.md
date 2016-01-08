# 绑定

绑定将两个属性链接在一起，当其中一个变化时，链接的另一端也会跟着变化。

Ember的绑定可以在任何两个对象之间，不仅仅限于常见的model和view之间。重复一下，对象是指Ember的对象。
```javascript
//老婆对象
wife = Ember.Object.create({
  householdIncome: 80000 //家庭收入
});

//定义丈夫类
Husband = Ember.Object.extend({
  householdIncome: Ember.computed.alias('wife.householdIncome') //丈夫对象持有的老婆对象的家庭收入。
});

//创建丈夫对象
husband = Husband.create({
  wife: wife //持有老婆对象
});

//从丈夫那里得到家庭收入，等于老婆那边的家庭收入
husband.get('householdIncome'); // 80000

// 更改老婆对象的家庭收入
wife.set('householdIncome', 90000);
husband.get('householdIncome'); // 90000丈夫对象的家庭收入。
```
绑定不是立即更新，它发生在一个运行周期的结束点，你可以多次改变链接的值，不用担心代码的额外负担。

# 单向绑定

顾名思义，单向绑定的变化只从一个方向发生，通常仅仅基于性能优化的考虑。你可以放心的使用双向绑定，如果仅仅改变链接的一端，那么双向绑定看起来就是单向绑定。

单向绑定有其特定的使用场景，比如A-》B，可以把A看作B的默认值，修改B不会影响到A。原文的例子是发货地址，A是信用卡的地址，B是收货地址，初始呈现给用户的是信用卡地址，如果不是，用户可以修改，但修改的结果不会影响A。

```javascript
user = Ember.Object.create({
  fullName: "Kara Gates"
});

UserView = Ember.View.extend({
  userName: Ember.computed.oneWay('user.fullName')
});

userView = UserView.create({
  user: user
});

// 改变user对象的name，会引起userview对象的变化。
user.set('fullName', "Krang Gates");
// userView.userName变成了 "Krang Gates"

// 但是修改view的值，不会影响user的值。
userView.set('userName', "Truckasaurus Gates");
user.get('fullName'); // 还是"Krang Gates"

```
