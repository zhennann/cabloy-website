# 模块测试
目前只支持后端测试

## 后端Controller测试
在`backend/test/controller`目录添加Controller测试文件
``` javascript
// controller/home.test.js
const { app, mockUrl, assert } = require('egg-born-mock')(__dirname);

describe('test/controller/home.test.js', () => {

  it('action:index', async () => {
    const result = await app.httpRequest().get(mockUrl('home/index'));
    assert(result.body.code === 0);
  });

});
```

## 后端Service测试
在`backend/test/service`目录添加Service测试文件
``` javascript
// service/home.test.js
const { app, mockUrl, assert } = require('egg-born-mock')(__dirname);

describe('test/service/home.test.js', () => {

  it('index', async () => {
    const ctx = app.mockContext({ mockUrl: mockUrl() });
    const message = await ctx.service.home.index();
    assert(message);
  });

});
```

## 执行测试
在项目根目录执行测试
``` bash
$ npm run test:backend
$ npm run cov:backend
```
