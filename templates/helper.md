# 编写模板助手

{% raw %}
自己编写的模板助手使用起来和Ember内置的助手一样。

格式化是一个常见的使用场景，一起来实现一个。用ember-cli来做。

```console
ember generate helper format-currency
```
上述命令会在你项目的application/helpers目录下生成一个format-currency.js文件。

app/helpers/format-currency.js
```javascript
import Ember from "ember";

export default Ember.Helper.helper(function(params) {
  let value = params[0],
      dollars = Math.floor(value / 100),
      cents = value % 100,
      sign = '$';

  if (cents.toString().length === 1) { cents = '0' + cents; }
  return `${sign}${dollars}.${cents}`;
});
```
在模板中，你可以这样使用：
```html
Your total is {{format-currency 250}}.
输出：
Your total is $2.50.
```
## 模板的名称

和组件不同，helper的名称不强求杠（-）分隔线。

## 参数的接收

这样使用助手的时候：
```html
{{my-helper "hello" "world"}}
```
对应的helper对参数的解析：
```javascript
import Ember from "ember";

export default Ember.Helper.helper(function(params) {
  let arg1 = params[0];
  let arg2 = params[1];

  console.log(arg1); // => "hello"
  console.log(arg2); // => "world"
});
```
有更清晰的语法：
```javascript
import Ember from "ember";

export default Ember.Helper.helper(function([arg1, arg2]) {
  console.log(arg1); // => "hello"
  console.log(arg2); // => "world"
});
```

## 带名称的参数

带名称的参数不再强求顺序，在参数多到一定程度时是非常受用的。
```html
{{format-currency 350 sign="£"}}
```
```javascript
import Ember from "ember";

export default Ember.Helper.helper(function(params, namedArgs) {
  let value = params[0],
      dollars = Math.floor(value / 100),
      cents = value % 100,
      sign = namedArgs.sign === undefined ? '$' : namedArgs.sign;

  if (cents.toString().length === 1) { cents = '0' + cents; }
  return `${sign}${dollars}.${cents}`;
});
```

也有更清晰的语法，利用js的参数解构。
```javascript
import Ember from "ember";
export default Ember.Helper.helper(function(params, { option1, option2, option3 }) {
  console.log(option1); // => "hello"
  console.log(option2); // => "world"
  console.log(option3); // => "goodbye cruel world"
});
```

顺序参数和名称参数的使用建议。

顺序参数通常用来传递数值，多少钱？名称参数用来配置行为，是英镑而不是美元。

## 有状态的助手

一般来说，助手是无状态的，输入相同的值总会返回相同的值。但也可以编写有状态的助手。

你需要修改一下ember-cli给你生成的文件。
```javascript
import Ember from "ember";

export default Ember.Helper.extend({ //注意这一行和上面代码的差异。
  compute(params, hash) {
    let value = params[0],
        dollars = Math.floor(value / 100),
        cents = value % 100,
        sign = hash.sign === undefined ? '$' : hash.sign;

    if (cents.toString().length === 1) { cents = '0' + cents; }
    return `${sign}${dollars}.${cents}`;
  }
});
```
这里是extend，结果是一个继承自Ember.Object的类，潜规则规定需要一个compute方法。

既然是类，就可以注入服务。关于注入，如果你熟悉spring的注入，那么两者是非常类似的，默然也都是单例（singleton）。

```javascript
import Ember from "ember";

export default Ember.Helper.extend({
  authentication: Ember.inject.service() //这里注入服务
  compute() {
    let authentication = this.get('authentication');

    if (authentication.get('isAuthenticated')) {
      return "Welcome back, " + authentication.get('username');
    } else {
      return "Not logged in";
    }
  }
});
```
## html的转义
从安全角度出发，Ember默认转义所有html字符串。
app/helpers/make-bold.js
```javascript
import Ember from "ember";
export default Ember.Helper.helper(function(params) {
  return `<b>${params[0]}</b>`;
});
```
```html
输入：
{{make-bold "Hello world"}}
输出：
&lt;b&gt;Hello world&lt;/b&gt;
```
要停止转义，可以用htmlSafe方法：
```javascript
import Ember from "ember";
export default Ember.Helper.helper(function(params) {
  return Ember.String.htmlSafe(`<b>${params[0]}</b>`);
});
```

但这里还是有一个漏洞，参数params[0]的转义问题，为此可以额外添加一些代码。
```javascript
import Ember from "ember";
export default Ember.Helper.helper(function(params) {
  let value = Handlebars.Utils.escapeExpression(params[0]); //这个
  return Ember.String.htmlSafe(`<b>${value}</b>`);
});
```

***如果你发现需要大量使用html，可考虑是用component***

{% endraw %}
