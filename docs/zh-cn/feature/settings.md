# 设置

Cabloy统一管理模块的`设置`功能，前端集中提供了`设置`相关的页面组件。模块只需提供与设置相关的JSON Schema配置即可。因此`设置`使用了`验证`的相关特性

模块提供的`设置`分两部分：`用户设置`、`实例设置`

## 如何使用

### 1. 定义设置

`a-settings/backend/src/meta.js`

```javascript
const require3 = require('require3');
const extend = require3('extend2');

module.exports = app => {
  const meta = {
  };
  // only support in test
  if (app.meta.isTest) {
    // schemas
    const schemas = require('./config/validation/schemas.js')(app);
    // keywords
    const keywords = require('./config/validation/keywords.js')(app);
    // meta
    extend(true, meta, {
      settings: {
        user: {
          validator: 'userTest',
        },
        instance: {
          validator: 'instanceTest',
        },
      },
      validation: {
        validators: {
          userTest: {
            schemas: 'user,userExtra',
          },
          instanceTest: {
            schemas: 'instance',
          },
        },
        keywords: {
          'x-languages': keywords.languages,
        },
        schemas: {
          user: schemas.user,
          userExtra: schemas.userExtra,
          instance: schemas.instance,
        },
      },
    });
  }
  return meta;
};
```

> - settings: 模块提供的设置清单
>   - user: 用户设置
>     - validator: 与用户设置相关的验证器
>   - instance: 实例设置
>     - validator: 与实例设置相关的验证器

### 2. 定义缺省值

`a-settings/backend/src/config/config.js`

```javascript
// settings
config.settings = {
  instance: {
    info: {
      title: 'title1',
    },
  },
  user: {
    info: {
      username: 'zhennann',
    },
    extra: {
      extra: {
        info: {
          mobile: '1',
          sex: 1,
          language: 'en-us',
        },
      },
    },
  },
};
```

### 3. 使用设置

`a-settings/backend/src/controller/test.js`

```javascript
async settings() {

  // user

  // get settings from config
  let data = await this.ctx.meta.settings.getUser({ name: '/info/username' });
  assert(data === 'zhennann');
  data = await this.ctx.meta.settings.getUser({ name: '/extra/extra/info/language' });
  assert(data === 'en-us');

  // load settings
  data = await this.ctx.meta.settings.loadSettingsUser();
  assert(data.info.username === 'zhennann');
  // save settings
  data.extra.extra.info.language = 'zh-cn';
  await this.ctx.meta.settings.saveSettingsUser({ data });

  // get settings from db
  data = await this.ctx.meta.settings.getUser({ name: '/extra/extra/info/language' });
  assert(data === 'zh-cn');

  // instance

  // get settings from config
  data = await this.ctx.meta.settings.getInstance({ name: '/info/title' });
  assert(data === 'title1');

  // laod settings
  data = await this.ctx.meta.settings.loadSettingsInstance();
  assert(data.info.title === 'title1');
  // save settings
  data.info.title = 'title2';
  await this.ctx.meta.settings.saveSettingsInstance({ data });

  // get settings from db
  data = await this.ctx.meta.settings.getInstance({ name: '/info/title' });
  assert(data === 'title2');

  this.ctx.success();
}
```


