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
const tree=res.list;
});
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
// 相对于根目录dist
<%=url('assets/images/background.png')%>
// 相对于当前文件
<%=url('./fonts/github/700i.woff')%>
```

## 合并和最小化CSS、JS

在渲染过程中，先声明CSS和JS文件，然后在最后进行合并和最小化。在渲染模版中提供占位符，替换为生成的实际URL链接

### 声明CSS、JS

``` javascript
// css
css('../../assets/css/site.css.ejs');
// js
js('../../assets/js/site.js.ejs');
```

> 如果引用的CSS、JS文件后缀名为'.ejs'，也会作为ejs模版进行渲染

### 占位符
``` javascript
// CSS文件链接占位符
<link rel="stylesheet" href="__CSS__">
// JS文件链接占位符
<script src="__JS__"></script>
```

## 注入环境参数

## 国际化

## 路径标示：_path


