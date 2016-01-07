# 类和实例

js语言没有内置的“类”。java类似的语言获得很大的成功，普及了“类”的概念。鉴于此，不少js框架都采用js的技术模拟了“类”。

## 定义类

用Emberjs造程序的房子，必须再建地基，Emberjs已经完成了这样工作。是的，```Ember.Object```。

```javascript
Person = Ember.Object.extend({
  say(thing) {
    alert(thing);
  }
});
```
可以通过_super()方法呼叫父类的同名方法。

## 创建类的实例

```javascript
var person = Person.create();
person.say("Hello"); // alerts " says: Hello"
```


## 初始化实例

定义一个init方法，它是一个钩子，每次创建新的对象都会呼叫这个方法。

## 操作对象的属性

```javascript
var person = Person.create();

var name = person.get('name');
person.set('name', "Tobias Fünke");
```

用统一的get，set方法。__注意，这不仅仅是语法上的统一，而且是其它观察者察觉属性变化所必须__。
