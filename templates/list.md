# 显示一个列表

{% raw %}

用{{#each}}遍历一个列表。
```html
<ul>
  {{#each people key="id" as |person|}}
    <li>你好, {{person.name}}!</li>
  {{/each}}
</ul>
```
每次经历，person代表列表中的一个元素。输出：
```html
<ul>
  <li>你好, Yehuda!</li>
  <li>你好, Tom!</li>
  <li>你好, Trek!</li>
</ul>
```

{{#each}}能够感知people的变化，people增删元素，或者people里面的某个person的name发生变化，自动会在UI上呈现，无需编写任何代码。当然你需要使用[Ember Mutable Array methods](http://emberjs.com/api/classes/Ember.MutableArray.html)。比如，不是[].push，而是[].pushObject。

## 指定元素的键

通过指定键，Ember在重画的时候可以知道哪个元素还在dom中，进而重复使用，达到提高性能的目的。代码编写者必须明确知道键的唯一性。

内置的键有：
* ```*@index```，元素在列表中的位置
* ```*@item```，元素本身，也就是对象的reference
* ```*@guid```，用Ember.guidFor产生的guid

## 空列表

{{#each}}可以像{{#if}}一样配套使用{{else}}，如果列表为空，可以产生分支效果。
```html
{{#each people key="id" as |person|}}
  你好, {{person.name}}!
{{else}}
  对不起, 这里没人。
{{/each}}
```

多么贴心的设计，thank you!

{% endraw %}

