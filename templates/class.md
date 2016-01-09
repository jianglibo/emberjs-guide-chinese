# 处理css的class

{% raw %}

和处理属性一样暖心，只有真心站在开发者的角度，才会有如此结果。

## 总有的class
```html
<div class={{priority}}>
  Warning!
</div>
```

## 有条件的class
如果isUrgent，输出is-urgent。
```html
<div class={{if isUrgent 'is-urgent'}}>
  Warning!
</div>
```
## 分支的class
```html
<div class={{if isEnabled 'enabled' 'disabled'}}>
  Warning!
</div>
```
{% endraw %}
