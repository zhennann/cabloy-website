# 缓存

Cabloy通过模块`a-cache`提供了两种缓存：`内存缓存`、`数据库缓存`

## 内存缓存

`内存缓存`仅对当前进程有效

`a-cache/backend/src/controller/test.js`

```javascript
async cachemem() {

  // set
  this.ctx.cache.mem.set('name', 'zhennann');

  // has
  let res = this.ctx.cache.mem.has('name');
  assert(res);

  // get
  let value = this.ctx.cache.mem.get('name');
  assert(value === 'zhennann');

  // remove
  this.ctx.cache.mem.remove('name');
  res = this.ctx.cache.mem.has('name');
  assert(!res);

  // set with timeout
  this.ctx.cache.mem.set('name', 'zhennann', 1000);

  // get
  value = this.ctx.cache.mem.get('name');
  assert(value === 'zhennann');

  // other module's cache
  const moduleCache = this.ctx.cache.mem.module(this.ctx.module.info.relativeName);
  value = moduleCache.get('name');
  assert(value === 'zhennann');

  // get after timeout
  await sleep(1000);
  value = this.ctx.cache.mem.get('name');
  assert(!value);

  this.ctx.success();
}
```

## 数据库缓存

`数据库缓存`可跨越进程边界

`a-cache/backend/src/controller/test.js`

```javascript
async cachedb() {

  // set
  await this.ctx.cache.db.set('name', 'zhennann');

  // has
  let res = await this.ctx.cache.db.has('name');
  assert(res);

  // get
  let value = await this.ctx.cache.db.get('name');
  assert(value === 'zhennann');

  // remove
  await this.ctx.cache.db.remove('name');
  res = await this.ctx.cache.db.has('name');
  assert(!res);

  // set with timeout
  await this.ctx.cache.db.set('name', 'zhennann', 1000);

  // get
  value = await this.ctx.cache.db.get('name');
  assert(value === 'zhennann');

  // other module's cache
  const moduleCache = this.ctx.cache.db.module(this.ctx.module.info.relativeName);
  value = await moduleCache.get('name');
  assert(value === 'zhennann');

  // get after timeout
  await sleep(1000);
  value = await this.ctx.cache.db.get('name');
  assert(!value);

  // clear
  //   not clear, hold other caches
  // await this.ctx.cache.db.clear();

  this.ctx.success();
}
```

