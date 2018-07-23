# 中间件

## 中间件的作用

灵活应用中间件机制，可以有效扩展架构的功能。中间件主要的作用有：拦截、重整、注入

### 拦截

在执行控制器指令之前，对请求参数进行判断，如果不符合预期，就拒绝。比如中间件`test`：

`egg-born-backend/app/middleware/test.js`

```javascript
module.exports = () => {
  return async function test(ctx, next) {
    if (!ctx.app.meta.isTest) ctx.throw(403);
    // next
    await next();
  };
};
```

### 重整

在执行控制器指令之前，对请求参数进行调整，使其符合预期的格式（如果不符合预期也可以拒绝）。比如中间件`validate`：

`a-validation/backend/src/config/middleware/validate.js`

```javascript
// request.body
//   validate: module(optional), validator, schema(optional)
//   data:
module.exports = (options, app) => {
  return async function validate(ctx, next) {
    // ignore
    const validator = options.validator || (ctx.request.body.validate && ctx.request.body.validate.validator);
    if (!validator) return await next();
    // params
    const module = options.module || (ctx.request.body.validate && ctx.request.body.validate.module) || ctx.module.info.relativeName;
    const schema = options.schema || (ctx.request.body.validate && ctx.request.body.validate.schema);
    const data = ctx.request.body[options.data || 'data'];
    // if error throw 422
    await ctx.meta.validation.validate({
      module,
      validator,
      schema,
      data,
    });
    // next
    await next();
  };
};
```

### 注入

在执行控制器指令之前，向ctx注入实例，扩展功能。比如中间件`cachemem`：

`a-cache/backend/src/config/middleware/cachemem.js`

```javascript
const memFn = require('./adapter/mem.js');
const CACHE = Symbol('CTX#__CACHE');

module.exports = () => {
  return async function cachemem(ctx, next) {
    ctx.cache = ctx.cache || {};
    Object.defineProperty(ctx.cache, 'mem', {
      get() {
        if (ctx.cache[CACHE] === undefined) {
          ctx.cache[CACHE] = new (memFn(ctx))();
        }
        return ctx.cache[CACHE];
      },
    });

    // next
    await next();
  };
};
```

## 中间件的类型

### Egg中间件

请参考[Egg中间件](https://eggjs.org/zh-cn/basics/middleware.html)

### EggBorn系统中间件

为满足常见场景的应用，EggBorn内置了三个系统中间件：`inner`、`test`、`transaction`

#### 1. `inner`

在后端路由对应的控制器指令中可以访问其他后端路由，这称之为`内部访问`。在后端路由中指定中间件`inner`就可以限定此路由只能被`内部访问`。

`egg-born-backend/app/middleware/inner.js`

```javascript
module.exports = () => {
  return async function inner(ctx, next) {
    if (!ctx.innerAccess) ctx.throw(403);
    // next
    await next();
  };
};
```

#### 2. `test`

后端路由指定中间件`test`，可以限定此路由只能在`test`运行环境中访问。

`egg-born-backend/app/middleware/test.js`

```javascript
module.exports = () => {
  return async function test(ctx, next) {
    if (!ctx.app.meta.isTest) ctx.throw(403);
    // next
    await next();
  };
};
```

#### 3. `transaction`

后端路由指定中间件`transaction`，可以限定此路由开启数据库事务。

`egg-born-backend/app/middleware/transaction.js`

```javascript
module.exports = () => {
  return async function transaction(ctx, next) {

    // set flag
    ctx.dbMeta.transaction = true;

    try {
      // next
      await next();

      // check if success
      if (ctx.response.body && ctx.response.body.code !== 0) {
        await handleTransaction(ctx, false);
      } else {
        await handleTransaction(ctx, true);
      }
    } catch (err) {
      await handleTransaction(ctx, false);
      throw err;
    }

  };
};
```

### Cabloy中间件

Cabloy允许在模块中开发中间件，供其他模块使用。Cabloy中间件的开发步骤，以`cachemem`为例如下：

#### 1. config.js

`a-cache/backend/src/config/config.js`

```javascript
// middlewares
config.middlewares = {
  cachedb: {
    global: true,
    dependencies: 'instance',
  },
  cachemem: {
    global: true,
  },
};
```

> - global: 如果是全局中间件，就自动加载，否则需要在后端路由中指定
> - dependencies: 此中间件依赖的其他中间件，决定了中间件的加载顺序

#### 2. cachemem.js

`a-cache/backend/src/config/middleware/cachemem.js`

```javascript
const memFn = require('./adapter/mem.js');
const CACHE = Symbol('CTX#__CACHE');

module.exports = () => {
  return async function cachemem(ctx, next) {
    ctx.cache = ctx.cache || {};
    Object.defineProperty(ctx.cache, 'mem', {
      get() {
        if (ctx.cache[CACHE] === undefined) {
          ctx.cache[CACHE] = new (memFn(ctx))();
        }
        return ctx.cache[CACHE];
      },
    });

    // next
    await next();
  };
};
```

#### 3. middlewares.js

`a-cache/backend/src/config/middlewares.js`

```javascript
const cachedb = require('./middleware/cachedb.js');
const cachemem = require('./middleware/cachemem.js');

module.exports = {
  cachedb,
  cachemem,
};
```

#### 4. main.js

`a-cache/backend/src/main.js`

```javascript
const middlewares = require('./config/middlewares.js');
module.exports = app => {
  return {
    middlewares,
  };
};
```

## 中间件的使用

### 全局中间件

全局中间件自动加载，不需在后端路由中指定。

### 局部中间件

只需设置后端路由参数`middlewares`，如：

`a-base/backend/src/routes.js`

```javascript
{ method: 'post', path: 'version/update', controller: version, middlewares: 'inner' },
```

### 中间件参数

可通过后端路由参数`meta`指定中间件的参数，如：

`a-base/backend/src/routes.js`

```javascript
{ method: 'post', path: 'atom/write', controller: atom, middlewares: 'validate,transaction',
  meta: {
    right: { type: 'atom', action: 3 },
    validate: { data: 'item' },
  },
},
```

> - meta.right: 全局中间件`right`的参数
> - meta.validate: 局部中间件`validate`的参数

### 禁用中间件

可通过两种方式禁用中间件：

#### 1. config配置

`a-instance/backend/src/config/config.js`

```javascript
// middlewares
config.middlewares = {
  instance: {
    global: true,
    dependencies: 'cachemem',
    ignore: /(\/version\/(start|check|update|initModule)|\/a\/instance\/version\/init|\/a\/version\/version\/init)/,
  },
};
```

#### 2. 后端路由

`a-base/backend/src/routes.js`

```javascript
{ method: 'post', path: 'auth/echo', controller: auth, meta: { auth: { enable: false } } },
```


