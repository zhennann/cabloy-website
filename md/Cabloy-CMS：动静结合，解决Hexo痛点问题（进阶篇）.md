Cabloy-CMS：动静结合，解决Hexo痛点问题（进阶篇）

# 前言

[前一篇文章](https://cnodejs.org/topic/5bc22efb15e4fd1923f48eb3)
介绍了如何通过Cabloy-CMS快速搭建一个博客站点。

这里简单介绍Cabloy-CMS静态站点的渲染机制，更多详细的内容请参见[https://cms.cabloy.org](https://cms.cabloy.org)

# 渲染规则

## 渲染时机

为了平衡渲染性能，Cabloy-CMS提供了两个渲染时机：`一次构建`、`文章单独渲染`

### 一次构建

在`CMS配置`页面，点击`构建`按钮，一次性渲染并输出站点所有文件

### 文章单独渲染

当发布文章时，立即渲染文章，并渲染与文章相关的页面。

比如`首页`页面：为了提升首页加载性能，首页可能会包含最近发布的文章。所以，当`文章`单独渲染时，也会再次渲染`首页`

## SEO相关

SEO文件有三个：`robots.txt`、`sitemapindex.xml`、`sitemap.xml`

SEO文件均在`构建`时一次性输出

`sitemapindex.xml`包含不同语言的`sitemap.xml`链接，一个语言对应一个`sitemap.xml`文件

当文章单独渲染时，会修改`sitemap.xml`的内容

## 目录、标签、搜索

由于使用了站点地图文件，并且所有文章都已经渲染成静态文件，所以，`目录`、`标签`、`搜索`等场景下的文章清单，没必要提前渲染，只需在需要时通过ajax调用后端API获取清单并动态显示

## CMS、主题、插件

模块`a-cms`只提供了基本的渲染机制和渲染骨架，具体的页面布局、元素、功能，都通过`主题`和`插件`的组合实现。这种模式，既可以快速开发部署，也可以充分释放CMS的可扩展性和灵活性

Cabloy-CMS目前提供了主题模块`cms-themeblog`、`cms-themeaws`和插件模块`cms-pluginbase`、`cms-pluginarticle`、`cms-pluginsidebar`、`cms-pluginmarkdowngithub`、`cms-plugintrack`，实现了全功能的博客站点，后续也会持续推出一系列`主题`和`插件`

您可以自由组合`主题`和`插件`，甚至实现自己的`主题`和`插件`，呈现完全不同的站点效果。

也希望您能分享您的智慧与成果，加入到Cabloy的生态中来

# 文件结构

Cabloy-CMS采用精细的文件结构，带来了如下便利：
  - 便于定制CSS、JS
  - 便于定制图片等各类静态资源
  - 便于实现多语言
  - 便于调试与发布

> 建议先把服务运行起来，并`构建`一次，就可以清晰的看到Cabloy-CMS的文件结构

## 根目录

在开发环境中，为了便于调试，CMS文件`根目录`位于源代码项目内部。而在生产环境中，源代码项目可能是只读的，所以CMS文件`根目录`缺省放置在当前用户的Home目录中。

### 开发环境

根目录：`[ProjectDir]/src/backend/app/public/[InstanceId]/cms`

- InstanceId: 实例Id，通过多实例可以实现多CMS站点的搭建

### 运行环境

根目录：`[HomeDir]/cabloy/[ProjectName]/public/[InstanceId]/cms`

- HomeDir: 默认为当前用户的Home目录，可以通过模块`a-file`配置

`src/backend/config/config.prod.js`

``` javascript
config.modules = {
  'a-file': {
    publicDir: 'CustomDir',
  },
};
```

## 一级目录

![](https://user-gold-cdn.xitu.io/2018/10/21/16696b0247c0b91b?w=122&h=76&f=png&s=4557)

|名称|说明|
|-|-|
|dist|`构建`的输出目录|
|en-us/zh-cn|语言源码目录|

## 输出目录

![](https://user-gold-cdn.xitu.io/2018/10/21/16696b06beaf1476?w=347&h=303&f=png&s=30164)

|名称|说明|渲染时机|备注|
|-|-|-|-|
|articles|存储所有渲染的文章页面|一次构建||
|assets|资源文件|一次构建||
|plugins|插件的资源文件|一次构建||
|static|静态文件|一次构建|如文件`articles.html`，通过ajax调用后端API获取文章清单，从而可以集中实现`目录`、`标签`、`搜索`等功能|
|zh-cn|其他语言的文件输出目录||支持多语言时，缺省语言在`根目录`下，其他语言在`子目录`下|
|index.html|首页|两个渲染时机|为了提升首页加载性能，首页可能会包含最近发布的文章。所以，当`文章`单独渲染时，也会再次渲染`首页`|
|robots.txt|SEO相关|一次构建|不论是否有多语言，只有一个`robots.txt`在`根目录`下|
|sitemap.xml|SEO相关，当前语言的站点地图文件|一次构建，`文章`单独渲染时修改内容||
|sitemapindex.xml|SEO相关，站点地图文件索引|一次构建|不论是否有多语言，只有一个`sitemapindex.xml`在`根目录`下|

## 语言源码目录

![](https://user-gold-cdn.xitu.io/2018/10/21/16696b09e0e38c02?w=299&h=364&f=png&s=32619)

|名称|说明|备注|
|-|-|-|
|intermediate|中间文件目录|在一次`构建`时，将`主题`、`插件`、`自定义源码`的所有源码文件和资源统一写入`intermediate`目录，然后再执行渲染逻辑|
|custom|自定义源码目录|用户可以在`custom`目录添加自定义源码文件，在一次性`构建`时，会自动覆盖`intermediate`中相同路径的文件|
|custom/dist|特别输出目录|在实际生产环境中，会有一些第三方用途的文件，如`Google站点验证文件`，可以放置在这个目录，以便一次`构建`时输出|

|名称|说明|渲染时机|备注|
|-|-|-|-|
|assets|资源文件|一次构建||
|layout|布局目录|中间文件|`layout`不是官方强制定义的目录。主题可根据自己的需要添加，规划自己的页面元素|
|main|主渲染模版目录|两个渲染时机||
|main/article.ejs|文章渲染模版||当需要渲染`文章`时使用此模版文件|
|main/index|首页渲染模版目录||当需要渲染`首页`时使用此目录中的模版文件。为什么是目录？在一个复杂的站点中，根据场景需要可以有多个类`首页`模版文件|
|plugins|插件目录|一次构建|在一次`构建`时，把所有`插件`源码文件和资源写入`plugins`目录|
|static|静态文件目录|一次构建|如文件`articles.ejs`，通过ajax调用后端API获取文章清单，从而可以集中实现`目录`、`标签`、`搜索`等功能|

> 为什么需要把所有源码文件（`主题`、`插件`、`自定义源码`）都写入`intermediate`目录？
> - 写入一个目录，便于各文件之间的包含引用


#  渲染流程

Cabloy-CMS提供了两个渲染时机：`一次构建`、`文章单独渲染`，下面分别描述两个时机的渲染流程

## 合并站点配置

在渲染之前，先合并站点配置信息

![](https://user-gold-cdn.xitu.io/2018/10/21/16696b13a644ac66?w=177&h=284&f=png&s=16166)

## 一次构建

![](https://user-gold-cdn.xitu.io/2018/10/21/16696b178c27ee18?w=369&h=829&f=png&s=71705)

## 文章单独渲染

![](https://user-gold-cdn.xitu.io/2018/10/21/16696b1b9d2d8a26?w=289&h=223&f=png&s=19555)

# 后端上下文对象

Cabloy-CMS采用`ejs`模版引擎进行页面渲染，在渲染之前创建一个上下文对象，归集相关的数据和方法，以便在模版文件中使用

## 上下文对象结构

``` javascript
{
  ctx: [Object],
  site: [Object],
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
  article: [Object],
  _path: [String]
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

比如，插件`cms-pluginbase`提供了`无限滚动`的功能，如果加载失败需要在页面中提示`Load error, try again`，可以如下操作

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

# 终极篇

请允许再次强调，`主题`和`插件`本质上还是EggBorn模块，可以添加`前端页面`和`后端服务`

`大象无形`，终极武器掌握在您的手中，能呈现出什么效果，完全取决于您的想象力

欢迎贡献您的智慧和产品到社区，谢谢！

## GitHub贡献

> 有任何疑问，欢迎提交 [issue](https://github.com/zhennann/cabloy/issues)！
