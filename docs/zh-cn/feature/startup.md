# 启动项

`启动项`允许在后端进程启动后自动执行

## 如何使用

### 1. 定义启动项

`a-hook/backend/src/config/config.js`

```javascript
// startups
config.startups = {
  installHooks: {
    type: 'all',
    path: 'hook/installHooks',
  },
};
```

> - installHooks: 启动项名称
>   - type: 'all': 每个进程启动后都要启动，'worker': 只启动一次
>   - path: 执行对应的后端路由

### 2. 定义后端路由

`a-hook/backend/src/routes.js`

```javascript
{ method: 'post', path: 'hook/installHooks', controller: hook, middlewares: 'inner',
  meta: {
    instance: { enable: false },
  },
},
```

!> 因为启动项与`实例`无关，所以需要将中间件`instance`禁用
