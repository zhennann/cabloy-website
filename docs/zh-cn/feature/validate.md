# 验证

在前后端各自为政的开发模式下，前端和后端需要单独实现各自的参数验证逻辑。Cabloy充分发挥全栈开发的优势，只需定义好JSON Schema信息，就可以同时支持前端与后端的参数验证逻辑。同时，还具备数据清洗特性，根据JSON Schema信息自动把参数转换成后端所需的数据类型。

Cabloy的`验证`机制底层采用ajv，建议您对[ajv](https://github.com/epoberezkin/ajv)有初步的了解。

## 定义验证信息

以模块`a-authsimple`的`signin`验证逻辑为例：

### 1. schemas.js

`a-authsimple/backend/src/config/validation/schemas.js`

```javascript
module.exports = app => {
  const schemas = {};
  schemas.signin = {
    type: 'object',
    properties: {
      auth: {
        type: 'string',
        ebType: 'text',
        ebTitle: 'Your mobile/email',
        notEmpty: true,
      },
      password: {
        type: 'string',
        ebType: 'text',
        ebTitle: 'Your password',
        ebSecure: true,
        notEmpty: true,
        minLength: 6,
      },
      rememberMe: {
        type: 'boolean',
        ebType: 'toggle',
        ebTitle: 'Remember me',
      },
    },
  };
  return schemas;
};
```

### 2. meta.js

`a-authsimple/backend/src/meta.js`

```javascript
module.exports = app => {
  const schemas = require('./config/validation/schemas.js')(app);
  return {
    validation: {
      validators: {
        signin: {
          schemas: 'signin',
        },
      },
      schemas: {
        signin: schemas.signin,
      },
    },
  };
};
```

> - validation.validators: 声明模块提供的验证器清单
>   - signin: 验证器信息
>     - schemas: 验证器对应的schema清单，一般只需提供一个主schema
> - validation.schemas: 声明模块提供的schema清单
>   - signin: schema信息

### 3. main.js

`a-authsimple/backend/src/main.js`

```javascript
const metaFn = require('./meta.js');

module.exports = app => {
  return {
    meta: metaFn(app),
  };
};
```

## 后端验证

模块`a-validation`提供了两个验证中间件：`validate`、`validation`

### 1. validate

中间件`validate`根据路由配置的中间件参数，自动对后端路由进行`拦截`、`重整`操作

`a-authsimple/backend/src/routes.js`

```javascript
{ method: 'post', path: 'auth/signup', controller: auth, middlewares: 'validate',
  meta: { validate: { validator: 'signup' } },
},
```

> - middlewares.validate: 由于`validate`是局部中间件，需要显式声明
> - meta.validate: 中间件参数
>   - module: 验证器所属模块，缺省为当前模块
>   - validator: 需要使用的验证器名称

### 2. validation

中间件`validation`向ctx.meta注入验证对象，便于在合适的场景进行验证

`a-authsimple/backend/src/config/passport/auth.js`

```javascript
// validate
await ctx.meta.validation.validate({ validator: 'signin', data: body });
```

## 前端验证

模块`a-components`提供了一个全局vue组件`eb-validate`，用于拦截验证错误提示信息，并进行渲染。

另外，`eb-validate`还提供两种Form组件布局：

### 1. 自定义布局

在`eb-validate`内部自定义From组件布局

`a-authsimple/front/src/components/signin.vue`

```javascript
<template>
  ...
  <eb-validate ref="validate" :onPerform="onPerformValidate">
    <f7-list form no-hairlines-md>
      <f7-list-item>
        <f7-icon material="person_outline" slot="media"></f7-icon>
        <f7-label floating>{{$text('Your mobile/email')}}</f7-label>
        <eb-input type="text" :placeholder="$text('Your mobile/email')" clear-button v-model="auth" dataPath="auth"></eb-input>
      </f7-list-item>
    </f7-list>
  </eb-validate>
  <f7-list>
    <eb-list-button :onPerform="signIn">{{$text('Sign in')}}</eb-list-button>
  </f7-list>
  ...
</template>
<script>
export default {
  meta: {
    global: false,
  },
  data() {
    return {
      auth: null,
      password: null,
      rememberMe: false,
    };
  },
  methods: {
    onPerformValidate() {
      return this.$api.post('passport/a-authsimple/authsimple', {
        auth: this.auth,
        password: this.password,
        rememberMe: this.rememberMe,
      }).then(user => {
        this.$store.commit('auth/login', {
          loggedIn: true,
          user,
        });
        this.$meta.vueApp.reload();
      });
    },
    signIn() {
      return this.$refs.validate.perform();
    },
  },
};
```

### 2. 自动布局

由`eb-validate`根据验证器提供的schema信息自动布局Form组件

`a-authsimple/front/src/pages/reset.vue`

```javascript
<template>
  <f7-page>
    <eb-navbar :title="$text('Reset password')" eb-back-link="Back"></eb-navbar>
    <f7-block>
      <eb-validate ref="validate" auto :data="data" :params="{validator: 'reset'}" :onPerform="onPerformValidate">
      </eb-validate>
      <eb-button v-if="!$config.test" :onPerform="onPerformOk">{{$text('OK')}}</eb-button>
    </f7-block>
  </f7-page>
</template>
<script>
export default {
  meta: {
    global: false,
  },
  data() {
    return {
      data: {
        passwordOld: null,
        passwordNew: null,
        passwordNewAgain: null,
      },
    };
  },
  methods: {
    onPerformValidate() {
      return this.$api.post('auth/reset', {
        data: this.data,
      }).then(() => {
        this.$f7Router.back();
      });
    },
    onPerformOk() {
      return this.$refs.validate.perform();
    },
  },
};
</script>
```

> - auto: 自动布局
> - params: 自动布局参数
>   - module: 验证器所属模块，默认为当前模块
>   - validator: 验证器名称


