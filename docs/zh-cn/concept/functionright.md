# 功能授权

## 白名单策略

Cabloy采用白名单策略，默认不对任何功能授权，所有授权必须明确指定才会生效。

## 如何授权

前面提到有三种授权时机，如果要通过Api授权，如下：

`a-base/backend/src/config/middleware/adapter/role.js`

```javascript
async addRoleFunction({ roleId, functionId, roleRightId = 0 })
```

> - roleId: 需要被授权的角色Id
> - functionId: 功能Id
> - roleRightId: 如果菜单授权与原子指令保持一致，那么这里就保存原子指令的授权记录Id

## 授权判断

可以通过`中间件`或`Api`进行授权的判断

### 中间件判断

Cabloy使用中间件封装了授权判断的逻辑，只需在后端路由上配置相应的中间件参数即可。

`a-baseadmin/backend/src/routes.js`

```javascript
{ method: 'post', path: 'role/children', controller: role,
  meta: { right: { type: 'function', name: 'role' } }
},
```

> - `right`属于全局中间件，默认处于开启状态，只需配置参数即可
> - type: 判断功能授权
> - name: 功能的名称

### Api判断

`a-base/backend/src/config/middleware/adapter/function.js`

```javascript
async checkRightFunction({ function: { module, name }, user })
```
