# 制作主题

`主题`既可以全新制作，也可以`继承`自其他主题

在这里新建一个主题模块`test-cmsthemehello`，在首页渲染一行`Hello world`

## 新建主题模块

`主题`本质上也是EggBorn模块

进入项目目录，执行EggBorn提供的脚手架创建一个新模块

``` bash
$ cd /path/to/project
$ egg-born src/module/test-cmsthemehello --type=module
```

## 修改package.json

`test-cmsthemehello/package.json`

``` javascript
{
  "name": "egg-born-module-test-cmsthemehello",
  "version": "1.0.0",
  "title": "cms:theme:hello",
  "eggBornModule": {
    "cms": {
      "name": "hello",
      "theme": true,
      "extend": ""
    },
    ...
  },
  "dependencies": {
    ...
    "egg-born-module-cms-pluginbase": "^1.1.1",
    "egg-born-module-cms-pluginarticle": "^1.0.0",
    "egg-born-module-cms-pluginsidebar": "^1.0.0",
    "egg-born-module-cms-pluginmarkdowngithub": "^1.0.0",
    "egg-born-module-cms-plugintrack": "^1.0.1"
  }
}
```

- name: 必须按照EggBorn模块的命名规范: `egg-born-module-{providerId}-{moduleName}`
  - providerId: 开发者Id，强烈建议采用Github的Username，从而确保贡献到社区的模块不会冲突
- cms: CMS配置信息
  - name: 主题名称
  - `theme`: 声明本模块是一个主题
  - `extend`: 如果要继承主题，填入原主题的模块名如`cms-themeblog`
- dependencies: 如果使用了插件，在这里填入插件模块信息。如果继承了主题，也需要在这里填入原主题的模块信息

## 配置参数

`主题`可以提供自定义的参数

`test-cmsthemehello/backend/src/config/config.js`

``` javascript
module.exports = appInfo => {
  const config = {};

  // theme
  config.theme = {
    _message: 'Hello World',
  };

  return config;
};
```

## 创建首页渲染模版

`test-cmsthemehello/backend/cms/theme/main/index/index.ejs`

``` html
<html>
 <head></head>
 <body><%=site._message%></body>
</html>
```

## 其他源码及资源

根据需要添加其他源码及资源，这里从略

## 构建模块

作为EggBorn模块，如果在项目内部使用，不需要构建，可以直接使用。如果分享到社区，供其他用户安装使用，必须进行构建

``` bash
$ cd src/module/test-cmsthemehello   -- 进入模块目录
$ npm run build:front             -- 构建前端代码
$ npm run build:backend           -- 构建后端代码
```

## 发布模块

可以将制作好的模块发布到社区

``` bash
$ npm publish
```

