# 安装

...用编辑器打开html文件，然后引入<script type="text/javascript" src="./js/xx-framwork.js"></script>，然后开始编写js代码。时不时地在浏览器和编辑器之间切换，时不时的按一下F5，看看有没有出错。

..._Emberjs不是这样的写法_

...Emberjs提供了一个配套的工具[ember-cli](http://www.ember-cli.com/)来帮你建立、组织、编写一个Emberjs项目。ember-cli带给你：

* 先进的资源管理（拼接|合并、压缩|最小化、版本|缓存相关）
* 代码生成器，帮助生成程序的组件。
* 一个组织合理的骨架，这个很有意义，只要是ember-cli的程序，大家都知道在那个目录中去找哪个文件。
* 统一使用一种模块规范
* 一个完整的测试框架
* 从日益完善的ember-cli生态圈中获益

## 安装环境的需求

### nodejs & npm

访问[https://www.npmjs.com/package/ember-cli]，你会在项目描述中看到对环境的要求。目前的版本是：

* nodejs 0.12.x
* npm > 2.7

### GIT

谷歌或必应一下git windows，下载一个安装。安装好之后，新打开命令行，输入git，如果有反应，成了。


### 安装

```shell
npm install -g ember-cli
npm install -g phantomjs
ember -v
```
看到下面类似的图，成了。

![ember -v result][ember-v]

[ember-v]:https://github.com/jianglibo/emberjs-guide-chinese/raw/master/src/common/images/ember-v-result.png "执行ember -v的输出结果"
