# 创建模块

## 创建模块文件骨架

进入项目`cabloy-demo`路径，执行egg-born命令，创建模块`test-todo`的文件骨架。

```bash
$ egg-born src/module/test-todo --type=module
```

## 命名约定
为了不断沉淀业务模块，达到高度可复用的效果，所有模块的命名空间必须充分隔离，避免相互污染与冲突，故采用如下命名方式：
> egg-born-module-{providerId}-{moduleName}

如模块`egg-born-module-test-todo`，各环节命名信息如下：
> - `providerId`: test
> - `moduleName`: todo
> - `fullName`: egg-born-module-test-todo
> - `relativeName`: test-todo
> - 前端页面路由地址: /test/todo/{page}
> - 后端API路由地址: /test/todo/{controller}/{action}
