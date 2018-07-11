# Module Backend Development 

## Backend API Route

In the file `backend/src/routes.js`, add the api route:
``` javascript
const home = require('./controller/home.js');

module.exports = [
  { method: 'get', path: 'home/index', controller: home, action: 'index', middlewares:'transaction' },
];
```

> - `method`: get/post etc.
> - `path`: Supports parameters
> - `component`: Controller Component
> - `action`: Controller Action. If not set, will use the last word of `path` automatically.
> - `middlewares`: middlewares, such as `transaction` means enable the database transaction.

Access the api route of the same module in the frontend page component:
``` javascript
this.$api.get('home/index').then(data => {
}).catch(err => {
});
```

Access the api route of the other module in the frontend page component:
``` javascript
this.$api.get('/providerId/moduleName/home/index').then(data => {
}).catch(err => {
});
```

## Backend Controller

The backend controller is consistent with Egg.js
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

> For more information，see [Egg.js Controller](https://eggjs.org/en/basics/controller.html)

## Backend Service

Service is used to encapsulate the business logics. It is consistent with Egg.js
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

> Different from Service of Egg.js, Service of EggBorn.js uses `ctx.db` to manipulate the database, which automatically supports database transaction.

> For more information，see [Egg.js Service](https://eggjs.org/en/basics/service.html)

#### Backend Controller Interoperability
For the development of large-scale web application, EggBorn.js supports interoperability between the backend controllers, such as
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

> - `method`: get/post etc.
> - `url`: If access backend controller of the same module, please use the relative path. If access backend contrller of the other module, please use the absolute path with the beginning of `/`
> - `query`、`params`、`body`: Same as Egg.js Controller

#### Backend Database Operation

The backend database operation is consistent with Egg.js
> For more information，see [Egg.js MySQL](https://eggjs.org/en/tutorials/mysql.html)

## Backend Database Transaction

EggBorn.js offers a more convenient database transaction processing. You just need to configure the `transaction` middleware in the backend api route. Service uses `ctx.db` to operate database.
If the `controller caller` calls the `controller callee` through `ctx.performAction`, the database transaction status is as follow:

| Controller Caller Config | Controller Callee Config | Controller Callee Status |
|:----------------|:----------------|:-------------------- |
| true            | true            | true                 |
| true            | false           | true                 |
| false           | true            | true                 |
| false           | false           | false                |

## Backend Parameters Configuration

In the file `backend/src/config/config.js`, add the configuration information:
``` javascript
module.exports = appInfo => {
  const config = {};

  config.message = "Hello world! I'm %s.";

  return config;
};
```

Access the config of the same module:
``` javascript
const message = this.ctx.config.message;
```

## Backend I18N

In the directory `backend/src/config/locale`, add the i18n file, such as `zh-cn.js`:
``` javascript
module.exports = {
  "Hello world! I'm %s.": '您好，世界！我是%s。',
  'not found': '未发现',
};
```

I18N resources can be merged globally, so as to share i18n resources through all the modules.

Access the i18n resources:
``` javascript
const notFound = this.ctx.text('not found');
const message = this.ctx.text("Hello world! I'm %s.", 'zhennann');
```

## Backend Error Handing

In the file `backend/src/config/errors.js`, add the error code:
``` javascript
// error code should start from 1001
module.exports = {
  1001: 'not found',
};
```

Return error object:
``` javascript
this.ctx.fail(1001);
```

Throw error exception:
``` javascript
this.ctx.throw(1001);
```
