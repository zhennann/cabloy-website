# 插件：cms-pluginarticle

模块`cms-pluginarticle`是官方提供的文章插件，实现文章的各项功能，包括

## 标题区块渲染

`cms-pluginarticle/backend/cms/plugin/article/title.ejs`

## 统计区块渲染

`cms-pluginarticle/backend/cms/plugin/article/stat.ejs`

## 附件区块渲染

`cms-pluginarticle/backend/cms/plugin/article/attachments.ejs`

## 前后文章区块渲染

`cms-pluginarticle/backend/cms/plugin/article/brothers.ejs`

## 评论区块渲染

`cms-pluginarticle/backend/cms/plugin/article/comments.ejs`

## 初始脚本

`cms-pluginarticle`提供了初始脚本，在页面加载完成时自动执行

`cms-pluginarticle/backend/cms/plugin/assets/js/init.js.ejs`

``` javascript
$(document).ready(function() {
  // brothers
  util.article.brothers({container:'.brothers'});
});

$(document).on('echo-ready', function() {
  // stats
  util.article.stats();
  // comments
  util.article.comments({container:'.comments'});
});
```

|名称|说明|
|-|-|
|brothers|访问后端服务API取得前后文章信息，填充前后文章区块|
|stats|访问后端服务API取得文章统计信息，填充统计区块|
|comments|访问后端服务API取得评论清单，填充评论区块|
