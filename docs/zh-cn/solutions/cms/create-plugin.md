# 制作插件

在这里新建一个插件模块`test-cmspluginhello`，在页面加载完成时弹出提示`Hello world`

## 新建插件模块

`插件`本质上也是EggBorn模块

进入项目目录，执行EggBorn提供的脚手架创建一个新模块

``` bash
$ cd /path/to/project
$ egg-born src/module/test-cmspluginhello --type=module
```

## 修改package.json

`test-cmspluginhello/package.json`

``` javascript
{
  "name": "egg-born-module-test-cmspluginhello",
  "version": "1.0.0",
  "title": "cms:plugin:hello",
  "eggBornModule": {
    "cms": {
      "name": "hello",
      "plugin": true
    },
  },
  ...
}
```

- name: 必须按照EggBorn模块的命名规范: `egg-born-module-{providerId}-{moduleName}`
  - providerId: 开发者Id，强烈建议采用Github的Username，从而确保贡献到社区的模块不会冲突
- cms: CMS配置信息
  - name: 主题名称
  - `plugin`: 声明本模块是一个插件

## 配置参数

`插件`可以提供自定义的参数

`test-cmspluginhello/backend/src/config/config.js`

``` javascript
module.exports = appInfo => {
  const config = {};

  // plugin
  config.plugin = {
    _message: 'Hello World',
  };

  return config;
};
```

## 创建初始脚本

`test-cmspluginhello/backend/cms/plugin/init.js.ejs`

``` javascript
$(document).ready(function() {
  // alert
  const message='<%=site.plugins['test-cmspluginhello']._message%>';
  window.alert(message);
});
```

### 脚本如何引用

只需在渲染模版中声明JS文件即可

在这里，可以在主题`test-cmsthemehello`的首页模版中引用

`test-cmsthemehello/backend/cms/theme/main/index/index.ejs`

``` html
<% js('plugins/test-cmspluginhello/init.js.ejs') %>
<html>
 <head></head>
 <body>
  <div><%=site._message%></div>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/1.12.4/jquery.min.js"></script>
  <script src="__JS__"></script>
</body>
</html>
```

## 其他源码及资源

根据需要添加其他源码及资源，这里从略

## 构建模块

作为EggBorn模块，如果在项目内部使用，不需要构建，可以直接使用。如果分享到社区，供其他用户安装使用，必须进行构建

``` bash
$ cd src/module/test-cmspluginhello  -- 进入模块目录
$ npm run build:front             -- 构建前端代码
$ npm run build:backend           -- 构建后端代码
```

## 发布模块

可以将制作好的模块发布到社区

``` bash
$ npm publish
```

