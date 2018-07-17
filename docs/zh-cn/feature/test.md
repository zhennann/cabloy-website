# 测试驱动

基于Cabloy、EggBorn和Egg的完美配合，提供了一个便捷的框架，可以实现基于测试驱动的开发，从而显著提升后端代码的鲁棒性

## 从哪开始

### 1. version/test

在执行测试时，每个模块的`version/test`都会自动执行。可以在这个阶段准备一些测试数据（如测试用的角色、用户、权限等等），便于测试及调试

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

### 2. test目录

后端的测试代码都集中在`backend/test`目录中

`test-todo/backend/test/controller/test.test.js`

```javascript
const { app, mockUrl, mockInfo, assert } = require('egg-born-mock')(__dirname);

describe('test/controller/test.test.js', () => {
  it('atom:todo', async () => {
    app.mockSession({});

    // login as demo001
    await app.httpRequest().post(mockUrl('/a/authsimple/passport/a-authsimple/authsimple')).send({
      auth: 'demo001',
      password: '123456',
    });

    // create
    let result = await app.httpRequest().post(mockUrl('/a/base/atom/create')).send({
      atomClass: { module: mockInfo().relativeName, atomClassName: 'todo', atomClassIdParent: 0 },
    });
    assert(result.body.code === 0);
    const atomKey = result.body.data;

    // submit
    result = await app.httpRequest().post(mockUrl('/a/base/atom/submit')).send({
      key: atomKey,
      item: {
        atomName: 'read a book',
        completed: 0,
      },
    });
    assert(result.body.code === 0);

    // login as demo002
    await app.httpRequest().post(mockUrl('/a/authsimple/passport/a-authsimple/authsimple')).send({
      auth: 'demo002',
      password: '123456',
    });

    // review
    result = await app.httpRequest().post(mockUrl('/a/base/atom/action')).send({
      action: 101,
      key: atomKey,
    });
    assert(result.body.code === 0);

    // login as demo001
    await app.httpRequest().post(mockUrl('/a/authsimple/passport/a-authsimple/authsimple')).send({
      auth: 'demo001',
      password: '123456',
    });

    // delete
    result = await app.httpRequest().post(mockUrl('/a/base/atom/delete')).send({
      key: atomKey,
    });
    assert(result.body.code === 0);

  });
});
```

### 3. app.meta.isTest, app.meta.isLocal

可以通过`app.meta.isTest`、`app.meta.isLocal`，在合适的环境加载合适的代码逻辑

`test-cook/backend/src/models.js`

```javascript
const cook = require('./model/cook.js');
const cookType = require('./model/cookType.js');
const cookPublic = require('./model/cookPublic.js');

module.exports = app => {
  const models = {
  };
  if (app.meta.isTest || app.meta.isLocal) {
    Object.assign(models, {
      cook,
      cookType,
      cookPublic,
    });
  }
  return models;
};
```

### 4. 模块`test-cook`

模块`test-cook`是开箱即用的测试模块，提供大量的测试驱动开发指引

