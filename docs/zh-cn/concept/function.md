# 功能与菜单

Cabloy是前后端分离的框架。前后端分离对后端路由的权限控制提出了更高的要求。后端路由除了与原子指令相关的路由外，还有与功能相关的路由，如用户管理、角色管理、权限管理等等。

Cabloy对这些功能路由进行了统一管理，从而也便于对功能路由进行统一的权限分配。

`功能`的本质是用于对后端路由授权的实体。因此，一般而言一个`功能`对应一个后端API接口，但也可以对应多个后端Api接口。

而菜单只是功能路由的一个特例，因此也一并进行处理。

模块`test-todo`的功能配置如下：

`modules/test-todo/backend/src/meta.js`

```javascript
functions: {
  createCook: {
    title: 'Create Cook',
    scene: 'create',
    autoRight: 1,
    atomClassName: 'cook',
    action: 'create',
    sorting: 1,
    menu: 1,
  },
  listCook: {
    title: 'Cook List',
    scene: 'list',
    autoRight: 1,
    atomClassName: 'cook',
    action: 'read',
    sorting: 1,
    menu: 1,
  },
},
```

> - title: 功能的标题
> - scene: 如果是`菜单`，指定菜单的场景，用于菜单的分类显示
> - autoRight: 菜单权限是否与原子指令保持一致。如果一致，当角色被赋予原子指令权限时，也相应地拥有此菜单权限
> - atomClassName, action: 如果autoRight=1, 通过这两个字段查找对应的原子指令
> - sorting: 菜单排序值
> - menu: 是否为菜单。如果menu=0就是后端`功能`
> - actionModule: 前端处理组件所属模块名，默认为当前所属模块
> - actionComponent: 前端处理组件名
> - actionPath: 前端菜单页面路径
