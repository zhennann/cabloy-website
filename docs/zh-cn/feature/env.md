# 运行环境

Egg内置了三种运行环境：`test`、`local`、`prod`。Cabloy和EggBorn基于三种运行环境扩展了不同的启动逻辑，为模块开发以及服务运行提供了更多的便利。

## 环境参数

为了满足不同环境的需要，环境参数的配置也略有不同。

### 环境参数配置文件

- `src/backend/config/config.default.js`
- `src/backend/config/config.unittest.js`
- `src/backend/config/config.local.js`
- `src/backend/config/config.prod.js`

### 启动命令

```bash
$ npm run test:backend
$ npm run dev:backend
$ npm run start:backend
$ npm run stop:backend
```

### MySQL配置

为了便于测试与开发调试，`test`和`local`环境需配置系统用户账号，并且属性`database`设置为`sys`。

`test`环境启动时，会将以前的测试数据库删除，并重新生成新的数据库。

`local`环境启动时，会直接使用`test`环境创建的数据库。因此，必须先执行`npm run test:backend`一次，才可以正常执行`npm run dev:backend`。
如果`local`环境的测试数据太多，或者数据库结构发生变化时，只需再次执行`npm run test:backend`即可。




