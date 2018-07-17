# 角色

Cabloy的角色体系不同于网上流行的`RBAC模型`。

> `RBAC模型`没有解决业务开发中`资源范围授权`的问题。比如，Mike是软件部的员工，只能查看自己的日志；Jone是软件部经理，可以查看本部门的日志；Jimmy是企业负责人，可以查看整个企业的日志。

> `RBAC模型`概念复杂，在实际应用中，又往往引入新的概念，使得角色体系难以理解和维护。

## 概念辨析

涉及到角色体系，往往会有这些概念：用户、用户组、角色、部门、岗位、资源等等。而Cabloy设计的角色体系只有用户、角色、原子类型等概念，概念精简，层次清晰，灵活高效，既便于理解，又便于维护。

`内置角色`树

- root
  - anonymous
  - authenticated
    - registered
    - activated
    - superuser
    - organization
      - internal
      - external

> 1. 部门即角色: 部门其实就是角色，因为部门下面还有子角色，Cabloy把这种部门角色称为`目录角色`
> 2. 岗位即角色: 岗位其实也是角色，Cabloy把岗位角色称为`子角色`，只有`子角色`才允许添加`用户`
> 3. 授权范围即角色: 为了实现`资源范围授权`的特性，只需要在授权记录中指定某个角色即可

## 角色授权

1. 目前只提供两类资源的授权：`原子`、`功能`，完全可以满足实际业务开发的需求
2. 只有`子角色`可以添加`用户`，用户自然拥有所属角色的全部授权

## 授权传递机制

Cabloy中的角色授权有两种传递机制：

1. 继承传递(纵向): 角色的授权会自动被其子角色继承。比如，角色`organization`自动拥有角色`authenticated`的授权
2. 聚合传递(横向): 角色可以聚合另一个角色从而拥有改角色的授权。比如，如果角色`superuser`聚合了角色`activated`，那么就拥有角色`activated`的授权。同样，根据`继承传递`机制，角色`superuser`的所有子角色也自动拥有角色`activated`的授权

!> 通过`聚合传递`机制可以实现`角色模版`的功能，就是预定义角色，并为角色分配一组权限，从而便于在实际使用中给其他角色授权

## 授权时机

Cabloy提供的Api架构可以很方便的对`角色结构`和`角色授权`进行管理。当然，一般而言有这三个授权时机：

### 1. version/init

这里进行的授权同时适用于`test`、`local`、`prod`三个运行环境，以模块`test-todo`为例：

`test-todo/backend/src/service/version.js`

```javascript
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
```

### 2. version/test

这里进行的授权同时适用于`test`、`local`两个运行环境，便于测试和开发。以模块`test-todo`为例：

`test-todo/backend/src/service/version.js`

```javascript
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
```

### 3. 管理界面

如果角色授权只有在实际部署或运行时才能决定，就需要通过`管理界面`进行授权操作。

## 角色结构重建

为了提升运行性能，Cabloy依据`继承`和`聚合`两种传递机制，对角色进行展开处理。所以，如果修改了任何与角色结构相关的数据，就需要执行`角色结构重建`，从而使角色生效。

`查询角色结构状态`
```javascript
const dirty=await this.ctx.meta.role.getDirty();
```

`角色结构重建`
```javascript
await this.ctx.meta.role.build();
```
