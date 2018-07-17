# 路由钩子

Cabloy通过模块`a-hook`提供了路由钩子，可以在控制器指令前后执行业务逻辑

## 如何使用

### 1. 定义路由钩子

在`meta`中定义路由钩子

`a-hook/backend/src/meta.js`

```javascript
module.exports = app => {
  const meta = {};
  if (app.meta.isTest) {
    Object.assign(meta, {
      hook: {
        before: [
          { path: '/a/base/auth/echo', route: 'test/hookTestBefore' },
        ],
        after: [
          { path: '/a/base/auth/echo', route: 'test/hookTestAfter' },
        ],
      },
    });
  }
  return meta;
};
```

> - hook.before: 控制器指令前执行
>   - path: 需要钩的后端路由
>   - route: 需要执行的后端路由
> - hook.after: 控制器指令后执行

### 2. 定义钩子路由

`a-hook/backend/src/routes.js`

```javascript
{ method: 'post', path: 'test/hookTestBefore', controller: test, middlewares: 'test' },
{ method: 'post', path: 'test/hookTestAfter', controller: test, middlewares: 'test' },
```
