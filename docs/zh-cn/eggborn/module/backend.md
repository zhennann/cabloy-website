# 模块后端开发

## 后端api路由
在`backend/src/routes.js`中添加api路由，如
``` javascript
const home = require('./controller/home.js');

module.exports = [
  { method: 'get', path: 'home/index', controller: home, action: 'index', middlewares:'transaction' },
];
```

> - `method`: get/post等方法
> - `path`: 路径，支持参数
> - `component`: Controller对象
> - `action`: Controller方法，如果不设置，则自动采用path尾部单词
> - `middlewares`: 可指定一组中间件，如`transaction`是启用数据库事务

在前端页面组件中访问本模块api路由
``` javascript
this.$api.get('home/index').then(data => {
}).catch(err => {
});
```

在前端页面组件中访问其他模块api路由
``` javascript
this.$api.get('/providerId/moduleName/home/index').then(data => {
}).catch(err => {
});
```

## 后端Controller
后端Controller的实现方式与Egg.js保持一致
``` javascript
module.exports = app => {
  class HomeController extends app.Controller {

    async index() {
      const message = await this.service.home.index();
      this.ctx.success(message);
    }

  }
  return HomeController;
};
```

> 更多信息，请参阅: [Egg.js Controller](https://eggjs.org/zh-cn/basics/controller.html)

## 后端Service
Service用于封装业务逻辑，供Controller调用，实现方式与Egg.js保持一致。
``` javascript
module.exports = app => {
  class Home extends app.Service {

    async index() {
      const res = await this.ctx.db.queryOne('show tables');
      return res;
    }

  }

  return Home;
};
```

> 与Egg.js不同之处在于，Service使用`ctx.db`操作数据库，从而自动支持数据库事务。

> 更多信息，请参阅: [Egg.js Service](https://eggjs.org/zh-cn/basics/service.html)

## 后端Controller调用
为了支持大型Web系统的开发，EggBorn.js支持模块后端Controller之间的调用，如
``` javascript
const message = await this.ctx.performAction({
  method: 'get',
  url: 'home/index',
  query: {
    username: 'kevin',
  },
  params: {
    mode: 1,
  },
  body: {
    content: 'ready',
  },
});
```

> - `method`: get/post等方法
> - `url`: 访问本模块的Controller使用相对路径，访问其他模块的Controller使用以`/`开头的绝对路径。
> - `query`、`params`、`body`: 与常规的Controller参数保持一致

## 后端数据库操作
后端数据库操作与Egg.js保持一致
> 更多信息，请参阅: [Egg.js MySQL](https://eggjs.org/zh-cn/tutorials/mysql.html)

## 后端数据库事务
EggBorn.js提供了更为便利的数据库事务实现方式，只需在后端api路由记录中添加`transaction`中间件，Service使用`ctx.db`操作数据库。
如果是主Controller通过`ctx.performAction`调用子Controller，数据库事务开启规则如下：

| 主Controller配置 | 子Controller配置 | 子Controller实际启用 |
|:----------------|:----------------|:-------------------- |
| true            | true            | true                 |
| true            | false           | true                 |
| false           | true            | true                 |
| false           | false           | false                |

## 后端参数配置
在`backend/src/config/config.js`中添加配置信息，如
``` javascript
module.exports = appInfo => {
  const config = {};

  config.message = "Hello world! I'm %s.";

  return config;
};
```

访问本模块内部的参数配置示例如下
``` javascript
const message = this.ctx.config.message;
```

## 后端国际化
在`backend/src/config/locale`目录添加国际化文件
`zh-cn.js`文件中的语言定义示例如下
``` javascript
module.exports = {
  "Hello world! I'm %s.": '您好，世界！我是%s。',
  'not found': '未发现',
};
```

国际化语言采取全局合并的方式，有利于语言资源的共享，访问方式如下
``` javascript
const notFound = this.ctx.text('not found');
const message = this.ctx.text("Hello world! I'm %s.", 'zhennann');
```

## 后端错误处理
在`backend/src/config/errors.js`文件中添加错误代码
``` javascript
// error code should start from 1001
module.exports = {
  1001: 'not found',
};
```

返回错误信息示例如下
``` javascript
this.ctx.fail(1001);
```

也可抛出异常示例如下
``` javascript
this.ctx.throw(1001);
```
