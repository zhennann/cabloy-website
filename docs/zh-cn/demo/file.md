# 文件清单

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
          sorting: 1,
          menu: 1,
        },
        listTodo: {
          title: 'Todo List',
          scene: 'list',
          autoRight: 1,
          atomClassName: 'todo',
          action: 'read',
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

本文件定义version控制器。

```javascript
module.exports = app => {
  class VersionController extends app.Controller {

    async update() {
      await this.service.version.update(this.ctx.request.body);
      this.ctx.success();
    }

    async init() {
      await this.service.version.init(this.ctx.request.body);
      this.ctx.success();
    }

    async test() {
      await this.service.version.test(this.ctx.request.body);
      this.ctx.success();
    }

  }
  return VersionController;
};
```

> - update: 对应路由version/update
> - init: 对应路由version/init
> - test: 对应路由version/test

## backend/src/controller/todo.js

本文件定义todo控制器。

```javascript
module.exports = app => {

  class TodoController extends app.Controller {

    async create() {
      const res = await this.ctx.service.todo.create(this.ctx.request.body);
      this.ctx.success(res);
    }

    async read() {
      const res = await this.ctx.service.todo.read(this.ctx.request.body);
      this.ctx.success(res);
    }

    async select() {
      const res = await this.ctx.service.todo.select(this.ctx.request.body);
      this.ctx.success(res);
    }

    async write() {
      await this.ctx.service.todo.write(this.ctx.request.body);
      this.ctx.success();
    }

    async delete() {
      await this.ctx.service.todo.delete(this.ctx.request.body);
      this.ctx.success();
    }

    async action() {
      const res = await this.ctx.service.todo.action(this.ctx.request.body);
      this.ctx.success(res);
    }

    async enable() {
      const res = await this.ctx.service.todo.enable(this.ctx.request.body);
      this.ctx.success(res);
    }

  }
  return TodoController;
};
```

> - create: 对应路由todo/create
> - read: 对应路由todo/read
> - select: 对应路由todo/select
> - write: 对应路由todo/write
> - delete: 对应路由todo/delete
> - action: 对应路由todo/action
> - enable: 对应路由todo/enable

## backend/src/services.js

本文件定义所有的services。

```javascript
const version = require('./service/version.js');
const todo = require('./service/todo.js');

module.exports = app => {
  const services = {
    version,
    todo,
  };
  return services;
};
```

## backend/src/service/version.js

本文件定义service version。

```javascript
module.exports = app => {

  class Version extends app.Service {

    async update(options) {
      if (options.version === 1) {
        // create table: testTodo
        const sql = `
          CREATE TABLE testTodo (
            id int(11) NOT NULL AUTO_INCREMENT,
            createdAt timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
            updatedAt timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
            deleted int(11) DEFAULT '0',
            iid int(11) DEFAULT '0',
            atomId int(11) DEFAULT '0',
            completed int(11) DEFAULT '0',
            PRIMARY KEY (id)
          )
        `;
        await this.ctx.model.query(sql);
      }
    }

    async init(options) {
      if (options.version === 1) {
        // create role: reviewer
        const roleAuthenticated = await this.ctx.meta.role.getSystemRole({ roleName: 'authenticated' });
        await this.ctx.meta.role.add({
          roleName: 'reviewer',
          roleIdParent: roleAuthenticated.id,
        });

        // add role rights
        const roleRights = [
          { roleName: 'authenticated', action: 'create' },
          { roleName: 'authenticated', action: 'write', scopeNames: 0 },
          { roleName: 'authenticated', action: 'delete', scopeNames: 0 },
          { roleName: 'authenticated', action: 'read', scopeNames: 'authenticated' },
          { roleName: 'reviewer', action: 'review', scopeNames: 'authenticated' },
          { roleName: 'superuser', action: 'review', scopeNames: 'authenticated' },
        ];
        const module = this.ctx.app.meta.modules[this.ctx.module.info.relativeName];
        const atomClass = await this.ctx.meta.atomClass.get({ atomClassName: 'todo' });
        for (const roleRight of roleRights) {
        // role
          const role = await this.ctx.meta.role.get({ roleName: roleRight.roleName });
          // scope
          let scope;
          if (!roleRight.scopeNames) {
            scope = 0;
          } else {
            const roleScope = await this.ctx.meta.role.get({ roleName: roleRight.scopeNames });
            scope = [ roleScope.id ];
          }
          // add role right
          await this.ctx.meta.role.addRoleRight({
            roleId: role.id,
            atomClassId: atomClass.id,
            action: this.ctx.constant.module('a-base').atom.action[roleRight.action] || module.main.meta.base.atoms.todo
              .actions[roleRight.action].code,
            scope,
          });
        }
      }
    }

    async test() {
      // create test users: demo001,demo002
      const users = [
        { userName: 'demo001', roleName: 'registered' },
        { userName: 'demo002', roleName: 'reviewer' },
      ];
      const userIds = [];
      for (const user of users) {
        // add user
        userIds[user.userName] = await this.ctx.meta.user.add({
          userName: user.userName,
          realName: user.userName,
        });
        // add auth info for login
        await this.ctx.performAction({
          method: 'post',
          url: '/a/authsimple/auth/add',
          body: {
            userId: userIds[user.userName],
            password: '123456',
          },
        });
        // role
        const role = await this.ctx.meta.role.get({ roleName: user.roleName });
        // add user role
        await this.ctx.meta.role.addUserRole({
          userId: userIds[user.userName],
          roleId: role.id,
        });
      }
    }

  }

  return Version;
};
```

> - update: 创建数据表testTodo
> - init: 创建角色reviewer，并设置角色数据权限
> - test: 创建两个测试账号，归属不同的角色

## backend/src/service/todo.js

本文件定义service todo，实现增删改查等相关的数据操作。

```javascript
module.exports = app => {

  class Todo extends app.Service {

    async create({ atomClass, key, atom, user }) {
      // add todo
      const res = await this.ctx.model.todo.insert({
        atomId: key.atomId,
      });
      return { atomId: key.atomId, itemId: res.insertId };
    }

    async read({ atomClass, key, item, user }) {
      // read
    }

    async select({ atomClass, options, items, user }) {
      // select
    }

    async write({ atomClass, key, item, validation, user }) {
      // update todo
      await this.ctx.model.todo.update({
        id: key.itemId,
        completed: item.completed,
      });
    }

    async delete({ atomClass, key, user }) {
      // delete todo
      await this.ctx.model.todo.delete({
        id: key.itemId,
      });
    }

    async action({ action, atomClass, key, user }) {
      if (action === 101) {
        // change flag
        await this.ctx.meta.atom.flag({
          key,
          atom: { atomFlag: 2 },
          user,
        });
      }
    }

    async enable({ atomClass, key, atom, user }) {
      // enable
      const atomFlag = atom.atomEnabled ? 1 : 0;
      // change flag
      await this.ctx.meta.atom.flag({
        key,
        atom: { atomFlag },
        user,
      });
    }

  }

  return Todo;
};
```

!> 模块`a-base`针对以下指令进行了封装，从而实现了基于权限的处理，这里只需要实现与Todo相关的逻辑即可。

> - create: 新建条目
> - read: 读取单条目信息，可在这里提供附加信息
> - select: 读取条目清单，可在这里提供附加信息
> - write: 更新条目
> - delete: 删除条目
> - action: 执行自定义的指令
> - enable: 将条目从“草稿”状态切换到“提交”状态

## backend/src/models.js

本文件定义所有的数据模型。

```javascript
const todo = require('./model/todo.js');

module.exports = app => {
  const models = {
    todo,
  };
  return models;
};
```

## backend/src/model/todo.js

本文件定义数据模型todo。

```javascript
module.exports = app => {
  class Todo extends app.meta.Model {
    constructor(ctx) {
      super(ctx, { table: 'testTodo', options: { disableDeleted: false } });
    }
  }
  return Todo;
};
```
## backend/src/config/locales.js

本文件定义后端支持的语言清单，缺省为en-us。

```javascript
module.exports = {
  'zh-cn': require('./locale/zh-cn.js'),
};
```

## backend/src/config/locale/zh-cn.js

本文件定义后端的zh-cn语言资源。

```javascript
module.exports = {
  Todo: '待办',
  Review: '评审',
  Reviewing: '评审中',
  Reviewed: '已评审',
  'Create Todo': '新建待办',
  'Todo List': '待办清单',
  'What to do': '要做什么',
  Completed: '已完成',
};
```

## front/src/config/locales.js

本文件定义前端支持的语言清单，缺省为en-us。

```javascript
export default{
  'zh-cn': require('./locale/zh-cn.js').default,
};
```

## front/src/config/locale/zh-cn.js

```javascript
export default {
  'What to do': '要做什么',
  Completed: '已完成',
};
```
