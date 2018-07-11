# 模块开发

## 命名约定
为了不断沉淀业务模块，达到高度可复用的效果，所有模块的命名空间必须充分隔离，避免相互污染与冲突，故采用如下命名方式：
> egg-born-module-{providerId}-{moduleName}

如模块`egg-born-module-a-version`，各环节命名信息如下：
> - `providerId`: a
> - `moduleName`: version
> - `fullName`: egg-born-module-a-version
> - `relativeName`: a-version
> - 前端页面路由地址: /a/version/{page}
> - 后端API路由地址: /a/version/{controller}/{action}

## 加载机制
模块既支持异步加载，也支持同步加载。默认是异步加载，如果要同步加载，只需在模块名称后面加上`-sync`后缀，如模块`egg-born-module-a-components-sync`。

## 新建模块

``` bash
$ egg-born src/module/test-todo --type=module
```
