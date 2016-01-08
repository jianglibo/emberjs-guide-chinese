# 可遍历对象

这是Ember对js的语法增强和统一。标准的API接口，通过内部不可见的实现，抹平浏览器之间的差异。

借助于该接口，得以书写顺滑的js代码，先来体验一下forEach方法。

```javascript
[1,2,3].forEach(function(item) {
  console.log(item);
});

//=> 1
//=> 2
//=> 3
```
forEach的第一个参数是function，如果传入第二个object参数，那么该object就成为function的环境，在function里面的this就指向该对象。

```javascript
var array = [1,2,3];

array.forEach(function(item) {
  //这里的this，就是array对象。
  console.log(item, this.indexOf(item));
}, array)

//=> 1 0
//=> 2 1
//=> 3 2
```

## Ember的可遍历对象
* Array Ember启动时自动对js内置的Array进行修饰
* Ember.set 该结构可以高效地回答是否包含某个对象

## API简介

这里列出了常见的方法，详细可以参考 [Ember.Enumerable API reference documentation.](http://emberjs.com/api/classes/Ember.Enumerable.html)

### forEach

```javascript
var food = ["Poi", "Ono", "Adobo Chicken"];

food.forEach(function(item, index) {
  console.log('Menu Item %@: %@'.fmt(index+1, item));
});
// console输出
// Menu Item 1: Poi
// Menu Item 2: Ono
// Menu Item 3: Adobo Chicken
```

### 拷贝数组

通过toArray()方法即可完成可遍历对象的复制.
```javascript
var states = Ember.Set.create();

states.add("Hawaii");
states.add("California")

states.toArray()
//=> ["Hawaii", "California"]
```
注意，上述代码中复制的是Ember.Set，所以复制对象的元素的顺序没有保证。

### 获取第一个和最后一个
```javascript
var animals = ["rooster", "pig"];

animals.get('lastObject');
//=> "pig"

animals.pushObject("peacock");

animals.get('lastObject');
//=> "peacock"
```
和Array的push不同，Enumerable在对应的方法名添加了Object字符串。

### map

map是对Enumerable持有的对象进行变形。变形之后元素的个数，顺序不变。
```javascript
var words = ["goodbye", "cruel", "world"];

var emphaticWords = words.map(function(item) {
  return item + "!";
});
// ["goodbye!", "cruel!", "world!"]
```

### Filtering过滤

过滤是将符合条件的元素取出来，返回一个不同的集合对象（即使内容一样，知道集合本省的指针不同）。
```javascript
var arr = [1,2,3,4,5];

arr.filter(function(item, index, self) {
  if (item < 4) { return true; }
})

// returns [1,2,3]
```

### Find寻找

寻找和过滤过程类似，不过Find找到一个就结束，并且返回的是单个元素，而不是集合。当然，没找到，就返回看起来是false的值。

### 回答集合是，全部，至少这样的问题

```javascript
Person = Ember.Object.extend({
  name: null,
  isHappy: false
});

//创建2个人，一个快乐，一个不快乐。
var people = [
  Person.create({ name: 'Yehuda', isHappy: true }),
  Person.create({ name: 'Majd', isHappy: false })
];

//问题，2个人都快乐吗？
people.every(function(person, index, self) {
  if(person.get('isHappy')) { return true; }
});
//答案：否
```

```javascript
//2个人中，至少有一个快乐吧？
people.some(function(person, index, self) {
  if(person.get('isHappy')) { return true; }
});

// 是的，有一个是快乐的。
```
