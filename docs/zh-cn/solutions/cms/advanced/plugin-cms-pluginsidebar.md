# 插件：cms-pluginsidebar

模块`cms-pluginsidebar`是官方提供的侧边栏插件，实现侧边栏区块的渲染，包括

## 个人信息区块渲染

`cms-pluginsidebar/backend/cms/plugin/sidebar/profile.ejs`

## 标签区块渲染

`cms-pluginsidebar/backend/cms/plugin/sidebar/tags.ejs`

## 最近评论区块渲染

`cms-pluginsidebar/backend/cms/plugin/sidebar/commentsRecent.ejs`

## 初始脚本

`cms-pluginsidebar`提供了初始脚本，在页面加载完成时自动执行

`cms-pluginsidebar/backend/cms/plugin/assets/js/init.js.ejs`

``` javascript
$(document).ready(function() {
  // tags
  util.sidebar.tags({container:'.panel-tags'});
});

$(document).on('echo-ready', function() {
  // recent comments
  util.sidebar.commentsRecent({container:'.panel-comments-recent'});
});
```

|名称|说明|
|-|-|
|tags|访问后端服务API取得标签信息，填充标签区块|
|commentsRecent|访问后端服务API取得最近评论清单，填充最近评论区块|
