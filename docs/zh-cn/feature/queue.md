# 队列

`队列`用于序列化执行业务逻辑。比如，通过`队列`执行数据库更新操作，可以确保多进程之间的数据一致性，避免数据冲突

## 如何使用

### 1. 定义队列

`a-sequence/backend/src/config/config.js`

```javascript
// queues
config.queues = {
  sequence: {
    path: 'sequence/next',
  },
};
```

> - sequence: 队列名称
>   - path: 队列要执行的后端路由

### 2. 定义后端路由

`a-sequence/backend/src/routes.js`

```javascript
{ method: 'post', path: 'sequence/next', controller: sequence, middlewares: 'inner' },
```

### 3. 投递队列

`a-sequence/backend/src/config/middleware/adapter/sequence.js`

```javascript
async next(name) {
  const res = await ctx.app.meta.queue.pushAsync({
    subdomain: ctx.subdomain,
    module: moduleInfo.relativeName,
    queueName: 'sequence',
    data: {
      module: this.moduleName,
      name,
    },
  });
  return res;
}
```

> - subdomain: `实例`对应的子域名
> - module: 对应所属模块
> - queueName: 队列名称
> - data: 队列参数，当队列执行时传递给后端路由
