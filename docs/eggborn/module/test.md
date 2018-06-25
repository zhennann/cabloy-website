# Module Test
Only backend test driven development is supported currently.

## Backend Controller Test

In the directory `backend/test/controller`, add controller test file:
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

## Backend Service Test

In the directory `backend/test/service`, add service test file:
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

## Run Test

Run test in the project root directory:
``` bash
$ npm run test:backend
$ npm run cov:backend
```
