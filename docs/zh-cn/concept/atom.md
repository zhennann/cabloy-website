# 原子

原子是Cabloy最基本的要素，如文章、评论、请假单，等等。

通过原子的组合，就可以实现任何想要的功能，如CMS、OA、CRM、ERP等等。

## 统一存储

所有原子数据的基本信息都统一存储到数据表`aAtom`中，与业务相关的字段存储在业务表中，如`testTodo`。`aAtom`与业务表是一对一的关系。

这种存储机制体现了`共性`与`差异性`的有机统一，有如下好处：

1. 可统一配置数据权限
2. 可统一支持增删改查等操作
3. 可统一支持`星标`、`标签`操作

`aAtom`表结构：

`a-base/backend/src/service/version/update1Data.js`

```
CREATE TABLE aAtom (
  id int(11) NOT NULL AUTO_INCREMENT,
  createdAt timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updatedAt timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  deleted int(11) DEFAULT '0',
  iid int(11) DEFAULT '0',
  itemId int(11) DEFAULT '0',
  atomEnabled int(11) DEFAULT '0',
  atomFlag int(11) DEFAULT '0',
  atomClassId int(11) DEFAULT '0',
  atomName varchar(255) DEFAULT NULL,
  userIdCreated int(11) DEFAULT '0',
  userIdUpdated int(11) DEFAULT '0',
  PRIMARY KEY (id)
)
```

> - id: 关键字段
> - createdAt: 条目创建时间，自动设置
> - updatedAt: 条目修改时间，自动设置
> - deleted: 软删除标记，业务数据建议开启`软删除`标记
> - iid: 数据实例Id，与域名相对应
> - itemId: 对应的业务表条目Id
> - atomEnabled: 是否处于草稿状态
> - atomFlag: 原子标记，与扩展指令相关
> - atomClassId: 对应的原子类型
> - atomName: 原子标题
> - userIdCreated: 创建原子条目的用户Id
> - userIdUpdated: 修改原子条目的用户Id

## 原子类型

`原子类型`是原子对应的元数据信息，在模块的meta中设置，如模块`test-todo`中原子类型`todo`的元数据信息如下：

`modules/test-todo/backend/src/meta.js`

```javascript
const meta = {
  base: {
    atoms: {
      todo: {
        info: {
          title: 'Todo',
          tableName: 'testTodo',
        },
      },
    },
  },
};
```

> - title: 原子类型的名称
> - tableName: 原子类型对应的业务数据表名称

## 原子星标、标签

可以对原子设置`星标`，也可以添加不同的`标签`，方便对原子快速分类、整理，以及查看。

## 原子草稿

新建的原子条目处于`草稿`状态，当`提交`之后处于`正常`状态。

`草稿`状态下，只有创建人可以操作，其他人均看不到。

`正常`状态下，只有分配了相应权限的用户才可以操作相应的指令。创建人始终可以查看自己创建的条目，但是如果没有分配权限，就无法操作`修改`、`删除`等指令。

核心模块`a-base`统一封装了`草稿`的逻辑，后端路由配置如下：

`modules/a-base/backend/src/routes.js`

```javascript
{ method: 'post', path: 'atom/enable', controller: atom },
```

业务模块只需提供扩展逻辑路由，以便在`提交`至`正常`状态时做一些特别处理。如模块`test-todo`的路由配置如下：

`modules/test-todo/backend/src/routes.js`

```javascript
{ method: 'post', path: 'todo/enable', controller: todo, middlewares: 'inner' },
```

## 原子指令、原子标记

`原子指令`是与`原子类型`相关的所有操作，分为`基本指令`和`扩展指令`两部分。

`原子标记`与`原子指令`相关。
- 如果`原子指令`没有关联`原子标记`，那么正常状态下的原子`原子指令`都是有效的
- 如果`原子指令`关联了`原子标记`，那么正常状态下的原子，只有设置了该`原子标记`，其对应的`原子指令`才有效
- `原子指令`可以关联多个`原子标记`

如模块`test-todo`中原子类型`todo`相关的指令及标记配置如下：

`modules/test-todo/backend/src/meta.js`

```javascript
const meta = {
  base: {
    atoms: {
      todo: {
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
      },
    },
  },
};
```

`actions.review`
> - code: 指令代码，从101开始分配
> - title: 指令标题
> - flag: 当原子标记等于`1`时，此指令才有效

`flags`
> - 1、2: 枚举值
> - title: 原子标记的标题

### 基本指令

`基本指令`包括create、read、write、delete等。

核心模块`a-base`统一封装了`基本指令`的基本逻辑，可以满足大多数场景的需求。如果想实现自定义的逻辑，可以通过`扩展指令`覆盖`基本指令`的配置。

`基本指令`的配置如下：

`a-base/backend/src/config/constants.js`

```javascript
atom: {
  action: {
    create: 1,
    read: 2,
    write: 3,
    delete: 4,
    save: 51,
    submit: 52,
    custom: 100, // custom action start from custom
  },
  actionMeta: {
    create: { title: 'Create', actionComponent: 'action', actionPath: 'atom/edit?atomId={{atomId}}&itemId={{itemId}}&atomClassId={{atomClassId}}&atomClassName={{atomClassName}}&atomClassIdParent={{atomClassIdParent}}' },
    read: { title: 'View', actionPath: 'atom/view?atomId={{atomId}}&itemId={{itemId}}&atomClassId={{atomClassId}}&atomClassName={{atomClassName}}&atomClassIdParent={{atomClassIdParent}}' },
    write: { title: 'Edit', actionPath: 'atom/edit?atomId={{atomId}}&itemId={{itemId}}&atomClassId={{atomClassId}}&atomClassName={{atomClassName}}&atomClassIdParent={{atomClassIdParent}}' },
    delete: { title: 'Delete', actionComponent: 'action' },
    save: { title: 'Save', actionComponent: 'action', authorize: false },
    submit: { title: 'Submit', actionComponent: 'action', authorize: false },
    custom: { title: 'Custom' },
  },
},
```

> - title: 指令的标题
> - actionModule: 前端处理组件所属模块名，默认为当前所属模块
> - actionComponent: 前端处理组件名
> - actionPath: 前端页面路径
> - authorize: 是否需要授权。因为`save`、`submit`是`write`的辅助指令，所以不需要单独授权
> - custom: 指令占位符，`扩展指令`从101开始

`a-base`提供的后端路由，封装了`事务`、`验证器`、`权限`等中间件的配置，如下：

`modules/a-base/backend/src/routes.js`

```javascript
{ method: 'post', path: 'atom/create', controller: atom, middlewares: 'transaction',
  meta: { right: { type: 'atom', action: 1 } },
},
{ method: 'post', path: 'atom/read', controller: atom,
  meta: { right: { type: 'atom', action: 2 } },
},
{ method: 'post', path: 'atom/write', controller: atom, middlewares: 'validate,transaction',
  meta: {
    right: { type: 'atom', action: 3 },
    validate: { data: 'item' },
  },
},
{ method: 'post', path: 'atom/submit', controller: atom, middlewares: 'validate,transaction',
  meta: {
    right: { type: 'atom', action: 3 },
    validate: { data: 'item' },
  },
},
{ method: 'post', path: 'atom/delete', controller: atom, middlewares: 'transaction',
  meta: { right: { type: 'atom', action: 4 } },
},
```

业务模块只需提供扩展逻辑路由，即可在基本逻辑的基础上做一些扩展操作。如模块`test-todo`的路由配置如下：

`modules/test-todo/backend/src/routes.js`

```javascript
{ method: 'post', path: 'todo/create', controller: todo, middlewares: 'inner' },
{ method: 'post', path: 'todo/read', controller: todo, middlewares: 'inner' },
{ method: 'post', path: 'todo/write', controller: todo, middlewares: 'inner' },
{ method: 'post', path: 'todo/delete', controller: todo, middlewares: 'inner' },
```

### 扩展指令

可以通过`扩展指令`实现与具体业务相关的操作，如原子类型`todo`增加`review`操作。

如果有特殊需求，可以通过`扩展指令`的配置方式改变`基本指令`的处理路由，从而实现自定义功能。

原子类型`todo`的扩展指令配置如下：

`modules/test-todo/backend/src/meta.js`

```javascript
const meta = {
  base: {
    atoms: {
      todo: {
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
      },
    },
  },
};
```

核心模块`a-base`实现了对`扩展指令`的统一分发，其提供的后端路由封装了`事务`、`验证器`、`权限`等中间件的配置，如下：

`modules/a-base/backend/src/routes.js`

```javascript
{ method: 'post', path: 'atom/action', controller: atom, middlewares: 'validate,transaction',
  meta: {
    right: { type: 'atom' },
    validate: { data: 'item' },
  },
},
```

业务模块只需提供扩展逻辑路由，以便进行相应的指令逻辑处理。如模块`test-todo`的路由配置如下：

`modules/test-todo/backend/src/routes.js`

```javascript
{ method: 'post', path: 'todo/action', controller: todo, middlewares: 'inner' },
```

## 原子查询

核心模块`a-base`封装了原子的查询操作，支持条件、排序、分页等特性，并对权限进行了处理，其后端路由配置如下：

`modules/a-base/backend/src/routes.js`

```javascript
{ method: 'post', path: 'atom/read', controller: atom,
  meta: { right: { type: 'atom', action: 2 } },
},
{ method: 'post', path: 'atom/select', controller: atom },
```

## 公开访问

默认情况下，原子只有被授予了权限才可以被查询到。Cabloy同样也支持不授予权限而直接公开访问。开启`公开访问`只需要一个步骤：

!> 以模块`test-cook`的原子类型`cookPublic`为例

1. 设置`cookPublic`的属性public

`modules/test-cook/backend/src/meta.js`

```javascript
base: {
  atoms: {
    cookPublic: {
      info: {
        tableName: 'testCookPublic',
        public: 1,
        flow: 1,
      },
    },
  },
},
```

## 简单流程

Cabloy目前实现了原子的`简单流程`处理。比如实现这个流程：当执行指令`review`之后，原子才可以公开访问，从而实现CMS中的文章发表功能。

默认情况下不开启`简单流程`，开启`简单流程`的步骤如下：

!> 以模块`test-cook`的原子类型`cookPublic`为例

1. 设置`cookPublic`的属性flow

`modules/test-cook/backend/src/meta.js`

```javascript
base: {
  atoms: {
    cookPublic: {
      info: {
        tableName: 'testCookPublic',
        public: 1,
        flow: 1,
      },
    },
  },
},
```

2. 修改原子的属性atomFlow

原子提交到正常状态后，自动进入`简单流程`状态。此时，只有指令操作权限的用户可以查看并执行指令。如果执行某个指令后流程处理完毕，那么只需在这个指令的后端逻辑中，将原子的属性atomFlow改为0即可。流程处理完毕后，其他有权限的用户才可以访问此原子。

`test-cook/backend/src/controller/test.js`

```javascript
await this.ctx.meta.atom.flow({
  key: cookKey,
  atom: {
    atomFlow: 0,
  },
  user: { id: userIds.Tom },
});
```



