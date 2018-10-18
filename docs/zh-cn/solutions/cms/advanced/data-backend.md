# 后端上下文对象

Cabloy-CMS采用`ejs`模版引擎进行页面渲染，在渲染之前创建一个上下文对象，归集相关的数据和方法，以便在模版文件中使用

## 上下文对象结构

``` javascript
{
  ctx: ...,
  site: ...,
  require: [Function],
  url: [Function],
  css: [Function],
  js: [Function],
  env: [Function],
  text: [Function],
  util: {
    time: {
      now: [Function],
      today: [Function],
      formatDateTime: [Function],
      formatDate: [Function],
      formatTime: [Function]
    },
    formatDateTime: [Function]
  },
  article: ...,
  _filename: ...,
  _path: ...
}
```

|名称|类型|说明|
|-|-|-|
|ctx|属性|通过ctx对象可以调用后端API及各种资源|
|site|属性|站点配置信息|
|require|方法|引用模块|
|url|方法|构造绝对链接|
|css|方法|声明css文件，以便最后合并和最小化|
|js|方法|声明js文件，以便最后合并和最小化|
|env|方法|注入环境变量，以便输出到前端使用|
|text|方法|文本国际化|
|util|属性|工具函数|
|article|属性|当前渲染的文章信息|
|_path|属性|标示当前模版文件的相对路径（相对于目录intermediate）|

## 访问后端资源

通过ctx对象可以调用后端API及各种资源

比如，为了渲染菜单，需要获取目录树，可以如下操作

``` javascript
const res = await ctx.performAction({
  method:'post',
  url: '/a/cms/category/tree',
  body: { language:site.language.current,hidden:0 },
});
const tree=res.list;
```

## 引用模块

在.ejs文件中，也可以像在NodeJS中一样引用模块

``` javascript
// 引用node_modules中的模块
const moment=require('moment');
// 引用项目内的文件模块
const test=require('./test.js');
```

## 绝对地址

建议页面中所有资源的URL链接都渲染成绝对地址

``` javascript
// 相对于网站根目录
<%=url('assets/images/background.png')%>
// 相对于当前文件
<%=url('./fonts/github/700i.woff')%>
```

## 合并和最小化CSS、JS

在渲染过程中，先声明CSS和JS文件，然后在最后进行合并和最小化。在渲染模版中提供占位符，替换为生成的实际URL链接

### 声明CSS、JS

``` javascript
// css
css('../assets/css/markdown/github.css.ejs');
css('../assets/css/article.css');
css('../assets/css/sidebar.css');
// js
js('../assets/js/lib/json2.min.js');
js('../assets/js/lib/bootbox.min.js');
js('../assets/js/util.js.ejs');
js('../assets/js/article.js.ejs');
js('../assets/js/sidebar.js.ejs');
```

> 如果引用的CSS、JS文件后缀名为'.ejs'，也会作为ejs模版进行渲染

### 占位符

``` javascript
// CSS文件链接占位符
<link rel="stylesheet" href="__CSS__">
// JS文件链接占位符
<script src="__JS__"></script>
```

### 效果

``` javascript
<link rel="stylesheet" href="https://zhennann.me/assets/css/8d38154d198309325c0759a22213dbd6ff0b7edecd2f4868dc72311335ccbe25.css">
<script src="https://zhennann.me/assets/js/b17e06ccb536dee939d4b1deaa595436363a52769c210d74d6a77f011e0f6461.js"></script>
```

## 注入环境参数

为了便于前端实现灵活且丰富的功能逻辑，需要把一些环境参数注入到前端。后端通过env声明环境参数，这些参数最后会进行合并注入到前端。

同样，也需要在前端提供占位符，替换为生成的实际环境参数

### 声明env

``` javascript
env('index',{
  [_path]:data.index,
});
```

### 占位符

``` javascript
// CSS文件链接占位符
<link rel="stylesheet" href="__CSS__">
// ENV占位符
__ENV__
```

### 效果

``` javascript
<script type="text/javascript">
var env={
  "base": ...,
  "language": ...,
  "format": ...,
  "comment": ...,
  "site": ...,
  "index": {
    "main/index/index": 20
  }
};
</script>
```

## 国际化

如果需要让`主题`和`插件`可以应用于不同的语言，需要对其中用到的文本资源进行国际化处理

因为`主题`和`插件`本质上都是EggBorn模块，所以可以直接使用EggBorn模块提供的国际化机制

比如，插件`cms-pluginbase`提供了向下滚动的功能，如果加载失败需要在页面中提示`Load error, try again`，可以如下操作

### 定义语言资源

`cms-pluginbase/backend/src/config/locale/zh-cn.js`

``` javascript
module.exports = {
  'Load error, try again': '加载失败，请重试',
};
```

### 引用

`cms-pluginbase/backend/cms/plugin/assets/js/util.js.ejs`

``` javascript
const $buttonTry = $('<button type="button" class="btn btn-warning btn-xs"><%=text("Load error, try again")%></button>');
```

## 路径标示：_path

一个通用的ejs模版文件可能被多个主ejs模版文件包含引用。通过_path，可以在通用ejs模版文件中知晓当前被哪个主ejs模版文件引用，以便做不同的逻辑处理


