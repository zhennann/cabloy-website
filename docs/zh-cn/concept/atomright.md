# 原子授权

## 白名单策略

Cabloy采用白名单策略，默认不对任何原子授权，所有授权必须明确指定才会生效。

## 草稿

原子有两个状态：`草稿`和`正常`。

`草稿`状态下，只有创建人自己可以执行`修改`和`删除`指令。

`正常`状态下，只有拥有授权的用户才可以执行相应授权的指令。

## 公开访问

默认情况下，原子只有被授予了权限才可以被查询到。Cabloy同样也支持不授予权限而直接公开访问，只需将原子类型的public属性设为1即可。

## 简单流程

Cabloy目前实现了原子的`简单流程`处理。比如实现这个流程：当执行指令`review`之后，原子才可以公开访问，从而实现CMS中的文章发表功能。

## 如何授权

前面提到有三种授权时机，如果要通过Api授权，如下：

`a-base/backend/src/config/middleware/adapter/role.js`

```javascript
async addRoleRight({ roleId, atomClassId, action, scope })
```

> - roleId: 需要被授权的角色Id
> - atomClassId: 原子类型Id
> - action: 需要授权的指令code
> - scope: 授权范围，支持以下值
>   - 0: 操作自己创建的原子
>   - roleId: 操作roleId以下所有子角色的用户创建的原子
>   - roleIds: roleId数组

## 授权判断

可以通过`中间件`或`Api`进行授权的判断

### 中间件判断

Cabloy使用中间件封装了授权判断的逻辑，只需在后端路由上配置相应的中间件参数即可。

`a-base/backend/src/routes.js`

```javascript
{ method: 'post', path: 'atom/create', controller: atom, middlewares: 'transaction',
  meta: { right: { type: 'atom', action: 1 } },
},
```

> - `right`属于全局中间件，默认处于开启状态，只需配置参数即可
> - type: 判断原子授权
> - action: 指令code

### Api判断

`a-base/backend/src/config/middleware/adapter/atom.js`

```javascript
async checkRightCreate({ atomClass: { id, module, atomClassName, atomClassIdParent = 0 }, user })
async checkRightRead({ atom: { id }, user })
async checkRightUpdate({ atom: { id, action }, user })
async checkRightAction({ atom: { id, action }, user })
```

## 授权判断一览表

原子处于不同的状态，其授权的判断机制不一样。

| 指令 | 草稿 | 简单流程 | 公开访问 | 正常 |
| - | :-: | :-: | :-: | :-: |
| create | - | - | - | - |
| read | 只有创建人有权限 | 判断是否有其他指令的授权 | 不需判断 | 需判断 |
| write、delete | 只有创建人有权限 | 需判断 | - | 需判断 |
| 扩展指令 | - | 需判断 | - | 需判断 |

> - `-`: 不支持
> - create: 只需对原子类型授权，与原子的状态无关


