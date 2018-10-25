# 插件：cms-pluginbase

模块`cms-pluginbase`是官方提供的基础插件，建议其他`主题`引用此插件模块，在此插件提供的基础功能上灵活定制自己的逻辑

当然，也可以不使用此插件模块，完全实现自己的`主题`和`插件`的组合

## 插件定义

`cms-pluginbase/package.json`

``` javascript
{
  "name": "egg-born-module-cms-pluginbase",
  "version": "1.0.9",
  "title": "cms:plugin:base",
  "eggBornModule": {
    "cms": {
      "name": "base",
      "plugin": true
    },
    "dependencies": {
      "a-instance": "1.0.0"
    }
  },
}
```

- `"plugin": true`: 声明是`插件`模块

## 初始脚本

`cms-pluginbase`提供了初始脚本，在页面加载完成时自动执行

`cms-pluginbase/backend/cms/plugin/assets/js/init.js.ejs`

``` javascript
$(document).ready(function() {
  // echo
  util.echo();
  // img delay
  $.each($('.img-delay'), (index, item) => {
    const $item = $(item);
    const src = $item.data('src');
    const width = $item.data('width');
    const height = $item.data('height');
    $item.attr('src', util.combineImageUrl(src, width, height));
    $(item).removeClass('img-delay');
  });
});
```

### echo

`echo`用于访问后端服务API，返回当前用户的登录信息

`cms-pluginbase/backend/cms/plugin/assets/js/util.js.ejs`

``` javascript
echo() {
  return util.ajax({
    url: '/a/base/auth/echo',
  }).then(data => {
    this.user = data.user;
    $(document).trigger('echo-ready', data);
  });
},
```

> 取得登录信息后，触发一个JQuery事件`echo-ready`，便于其他脚本在`echo-ready`中执行代码逻辑

### 图片延时加载

`cms-pluginbase`内置了延时加载的功能，同时通过`combineImageUrl`自动匹配设备像素比

需要延时加载的图片如下定义

``` html
<img class="img-delay" data-src="[url]" data-width="[width]" data-height="[height]">
```

## 工具对象：util

`cms-pluginbase`最核心的功能是提供了一个工具对象`util`，其结构如下

``` javascript
{
  ajax: [Function],
  performAction: [Function],
  combineImageUrl: [Function],
  echo: [Function],
  formatDateTime: [Function],
  loadMore: [Function],
  parseUrlQuery: [Function],
  promise: [Function],
  rootUrl: [Function],
  time: [Object],
  url: [Function],
  user: [Object]
}
```

|名称|类型|说明|
|-|-|-|
|ajax|方法|访问后端服务API接口|
|performAction|方法|访问后端服务API接口的统一入口|
|combineImageUrl|方法|组合图片URL，自动匹配设备像素比|
|echo|方法|用于访问后端服务API，返回当前用户的登录信息，并触发JQuery事件`echo-ready`|
|formatDateTime|方法|根据当前站点配置格式化时间|
|loadMore|方法|实现无限滚动时加载更多的功能|
|parseUrlQuery|方法|解析Url的Query参数|
|promise|方法|如果没有window.Promise自动创建一个|
|rootUrl|方法|返回指定语言的URL根路径|
|time|属性|时间工具集|
|url|方法|返回url的绝对路径|
|user|属性|执行echo返回的当前用户信息|




