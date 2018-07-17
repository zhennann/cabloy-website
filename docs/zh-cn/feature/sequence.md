# 序列

`序列`可以为某个具体业务提供唯一的序列值，比如`原子`在新建时分配唯一名称

## 如何使用

### 1. 定义序列

`a-base/backend/src/meta.js`

```javascript
mmodule.exports = app => {
  const meta = {};
  if (app.meta.isTest || app.meta.isLocal) {
    // meta
    Object.assign(meta, {
      sequence: {
        providers: {
          test: {
            start: 0,
            expression({ ctx, value }) {
              return ++value;
            },
          },
        },
      },
    });
  }
  return meta;
};
```

> - sequence.providers: 模块提供的序列清单
>   - test: 序列名称
>     - start: 起始值
>     - expression: 序列生成逻辑

### 2. 获取序列值

`a-sequence/backend/src/controller/test.js`

```javascript
const require3 = require('require3');
const assert = require3('assert');

module.exports = app => {
  const pMap = require('p-map');
  class TestController extends app.Controller {

    async sequence() {

      // current
      let current = await this.ctx.meta.sequence.current('test');
      assert(current === 0);

      // next
      let next = await this.ctx.meta.sequence.next('test');
      assert(next === 1);

      // current
      current = await this.ctx.meta.sequence.current('test');
      assert(current === 1);

      // reset
      await this.ctx.meta.sequence.reset('test');

      // other module's sequence
      const moduleSequence = this.ctx.meta.sequence.module(this.ctx.module.info.relativeName);

      // next
      next = await moduleSequence.next('test');
      assert(next === 1);

      // current
      current = await moduleSequence.current('test');
      assert(current === 1);

      // reset
      await moduleSequence.reset('test');

      // concurrency
      const results = await pMap([ 1, 2, 3, 4, 5 ], async () => {
        return await moduleSequence.next('test');
      });
      assert(results.join(',') === '1,2,3,4,5');

      // reset
      await moduleSequence.reset('test');

      this.ctx.success();
    }

  }
  return TestController;
};
```
