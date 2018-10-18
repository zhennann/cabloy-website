# 前端环境对象

为了便于前端实现灵活且丰富的功能逻辑，需要把一些环境参数注入到前端。

Cabloy-CMS本身内置了一些前端环境对象，同时，也可以通过`后端上下文对象`的`env`方法注入自定义属性，这些参数最后会进行合并注入到前端

## 注入env

``` javascript
env('index',{
  [_path]:data.index,
});
```

## 占位符

``` javascript
// CSS文件链接占位符
<link rel="stylesheet" href="__CSS__">
// ENV占位符
__ENV__
```

## 前端环境对象结构

``` javascript
<script type="text/javascript">
var env={
  "base": {
    "title": "my blog",
    "subTitle": "gone with the wind",
    "description": "",
    "keywords": ""
  },
  "language": {
    "items": "en-us,zh-cn",
    "default": "en-us",
    "current": "en-us"
  },
  "format": {
    "date": "YYYY-MM-DD",
    "time": "HH:mm:ss"
  },
  "comment": {
    "order": "asc",
    "recentNum": 5
  },
  "site": {
    "path": "main/article",
    "serverUrl": "https://zhennann.cabloy.org",
    "rawRootUrl": "https://zhennann.me"
  },
  "article": ...,
  "index": {
    "main/index/index": 20
  }
};
</script>
```

|名称|来源|说明|
|-|-|-|
|base|站点配置|站点基本信息|
|language|站点配置|语言信息|
|format|站点配置|时间格式化|
|comment|站点配置|评论参数|
|site|内置参数|站点参数|
|site.path||当前页面路径标示|
|site.serverUrl||后端服务URL前缀|
|site.rawRootUrl||前端站点URL前缀|
|article|内置参数|如果是文章页面，会自动注入此属性|
|index|自定义参数|由主题`cms-themeblog`注入的参数|

