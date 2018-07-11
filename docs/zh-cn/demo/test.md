# 测试驱动

基于Cabloy、EggBorn和Egg的完美配合，提供了一个便捷的框架，可以实现基于测试驱动的开发，从而显著提升后端代码的鲁棒性。

## backend/test/controller/test.test.js

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
