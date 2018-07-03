# 创建模块

## 创建模块文件骨架

进入项目`cabloy-demo`路径，执行egg-born命令，创建模块`test-todo`的文件骨架。

```bash
$ egg-born src/module/test-todo --type=module
```

## package.json

本文件定义模块的通用信息。

```json
  "name": "egg-born-module-test-todo",
  "version": "1.0.0",
  "title": "Todo",
  "eggBornModule": {
    "fileVersion": 1,
    "dependencies": {
      "a-base": "1.0.0"
    }
  },
```
> - fileVersion: 模块存入MySQL的数据结构如果有变更，fileVersion都要加1，这里是第一次创建，所以值为1。
> - dependencies: 这里声明模块之间的依赖关系，后端服务启动时依据模块之间的依赖关系，按预定的顺序依次加载模块。

## backend/src/meta.js

本文件定义模块的元数据。

```javascript
module.exports = app => {
  const schemas = require('./config/validation/schemas.js')(app);
  const meta = {
    base: {
      atoms: {
        todo: {
          info: {
            title: 'Todo',
            tableName: 'testTodo',
          },
          actions: {
            review: {
              code: 101,
              title: 'Review',
              flag: '1',
            },
          },
          flags: {
            1: {
              title: 'Reviewing',
            },
            2: {
              title: 'Reviewed',
            },
          },
          validator: 'todo',
          search: {
            validator: 'todoSearch',
          },
        },
      },
      functions: {
        createTodo: {
          title: 'Create Todo',
          scene: 'create',
          autoRight: 1,
          atomClassName: 'todo',
          action: 'create',
          actionModule: 'a-base',
          actionComponent: 'action',
          actionPath: '/a/base/atom/edit?atomId={{atomId}}&itemId={{itemId}}&atomClassId={{atomClassId}}&atomClassName={{atomClassName}}&atomClassIdParent={{atomClassIdParent}}',
          sorting: 1,
          menu: 1,
        },
        listTodo: {
          title: 'Todo List',
          scene: 'list',
          autoRight: 1,
          atomClassName: 'todo',
          action: 'read',
          actionPath: '/a/base/atom/list?module={{module}}&atomClassName={{atomClassName}}',
          sorting: 1,
          menu: 1,
        },
      },
    },
    validation: {
      validators: {
        todo: {
          schemas: 'todo',
        },
        todoSearch: {
          schemas: 'todoSearch',
        },
      },
      keywords: {},
      schemas: {
        todo: schemas.todo,
        todoSearch: schemas.todoSearch,
      },
    },
  };
  return meta;
};
```

### meta.base.atoms.todo

本节点定义`原子类型`。

> - info: 原子类型的基本信息
>   - title: 原子类型的名称  
>   - tableName: 原子类型对应的数据表名称
> - actions.review: 定义原子指令`review` 
>   - code: 指令代码，从101开始分配
>   - title: 指令标题
>   - flag: 指令对应的flag，当原子的flag等于某值时，此指令才有效
>       - 空字符串: 指令总是有效，与flag无关
>       - 逗号分隔字符串：可以指定多个flag值
> - flags: flags枚举值
> - validator: 原子对应的验证器，当提交todo条目内容时，通过此验证器验证内容字段的有效性
> - search.validator: 与搜索功能相关的验证器，通过此验证器可显示与todo相关的个性化搜索字段

### meta.base.functions

本节点定义菜单项。

> - createTodo: 创建Todo条目
>   - title: 菜单标题
>   - scene: 菜单的显示场景，用于菜单归类显示
>   - autoRight: 菜单权限是否与原子指令保持一致。如果一致，当角色被赋予原子指令权限时，也相应地拥有此菜单权限
>   - atomClassName, action: 如果autoRight=1, 通过这两个字段查找对应的原子指令
>   - actionModule, actionComponent: 当点击菜单时，执行这两个字段对应的前端组件
>   - actionPath: 当点击菜单时，打开此字段对应的页面链接
>   - sorting: 菜单排序值
>   - menu: 是否为菜单。如果menu=0就是后端功能，一般对应一个后端API接口 
> - listTodo: 显示Todo条目清单

### meta.validation 

本节点定义原子用到的`验证器`。

> - validators.todo: todo条目对应的验证器
>   - schemas: 此验证器对应的schemas清单，一般只需要一个schema
> - validators.todoSearch: todo个性化搜索对应的验证器
>   - schemas: 此验证器对应的schemas清单
> - keywords: schemas中用到的自定义keywords，这里为空
> - schemas.todo: todo验证器对应的schema
> - schemas.todoSearch: todoSearch验证器对应的schema

## backend/src/config/validation/schemas.js

本文件定义schemas信息。

```javascript
module.exports = app => {
  const schemas = {};
  // todo
  schemas.todo = {
    type: 'object',
    properties: {
      completed: {
        type: 'number',
        ebType: 'toggle',
        ebTitle: 'Completed',
      },
    },
  };
  // todo search
  schemas.todoSearch = schemas.todo;
  return schemas;
};
```

### schemas.todo

本节点定义todo的schema信息。

> - completed: 是否已完成字段
>   - type: 字段值的类型
>   - ebType: 字段前端显示组件的类型
>   - ebTitle: 字段标题

### schemas.todoSearch

本节点定义todoSearch的schema信息，与schemas.todo一致。

## backend/src/routes.js

本文件定义模块的后端API路由。

```javascript
const version = require('./controller/version.js');
const todo = require('./controller/todo.js');

module.exports = app => {
  const routes = [
    // version
    { method: 'post', path: 'version/update', controller: version, middlewares: 'inner' },
    { method: 'post', path: 'version/init', controller: version, middlewares: 'inner' },
    { method: 'post', path: 'version/test', controller: version, middlewares: 'test' },
    // todo
    { method: 'post', path: 'todo/create', controller: todo, middlewares: 'inner' },
    { method: 'post', path: 'todo/read', controller: todo, middlewares: 'inner' },
    { method: 'post', path: 'todo/select', controller: todo, middlewares: 'inner' },
    { method: 'post', path: 'todo/write', controller: todo, middlewares: 'inner' },
    { method: 'post', path: 'todo/delete', controller: todo, middlewares: 'inner' },
    { method: 'post', path: 'todo/action', controller: todo, middlewares: 'inner' },
    { method: 'post', path: 'todo/enable', controller: todo, middlewares: 'inner' },
  ];
  return routes;
};
```

### version

本组包括数据版本相关的路由。

> - version/update: 依据模块的数据版本依次执行所有的版本变更，使模块总是保持最新的数据版本状态
> - version/init: 依据模块的数据版本依次所有的数据初始化逻辑
> - version/test: 为模块的测试驱动，准备相应地测试数据

### todo

本组包括与原子todo相关的路由。

> - todo/create: 创建todo条目
> - todo/read: 读取单条todo条目信息
> - todo/select: 读取多条todo条目信息
> - todo/write: 保存todo条目信息
> - todo/delete: 删除todo条目信息
> - todo/action: 执行原子指令
> - todo/enable: 将原子从“草稿”状态切换到“提交”状态

## backend/src/controller/version.js

## backend/src/controller/todo.js

## backend/src/services.js

## backend/src/service/version.js

## backend/src/service/todo.js

## backend/src/config/locale/zh-cn.js


