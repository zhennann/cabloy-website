# 状态

通过`状态`可以标记后台服务运行当中的状态值。如`角色结构`发生变化，通过状态`roleDirty`进行标记

## 如何使用

`a-status/backend/src/controller/test.js`

```javascript
async status() {

  // get
  let value = await this.ctx.meta.status.get('__enable');
  assert(value === undefined);

  // set
  await this.ctx.meta.status.set('__enable', true);

  // get
  value = await this.ctx.meta.status.get('__enable');
  assert(value === true);

  // other module's status
  const moduleStatus = this.ctx.meta.status.module(this.ctx.module.info.relativeName);
  value = await moduleStatus.get('__enable');
  assert(value === true);

  // set
  await this.ctx.meta.status.set('__enable', false);

  // get
  value = await this.ctx.meta.status.get('__enable');
  assert(value === false);

  this.ctx.success();
}
```
