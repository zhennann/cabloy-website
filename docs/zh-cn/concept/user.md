# 用户

## 用户类型

Cabloy中用户有两个类型：匿名用户、注册用户。如果用户没有登录就会自动分配一个匿名用户，如果已经登录就是注册用户。

## 会话过期

匿名用户和注册用户都有会话过期配置。特别的，只要会话不过期，多次访问系统都会分配相同的匿名用户。

您可以在项目后端的config中配置会话过期，默认的会话过期如下：

`a-base/backend/src/config/config.js`

```javascript
// anonymous
config.anonymous = {
  maxAge: 365 * 24 * 3600 * 1000, // 365 天
};
// registered or rememberMe
config.registered = {
  maxAge: 1 * 24 * 3600 * 1000, // 1 天
};
```

## 用户代理

Cabloy支持用户代理功能，比如，`Mike`把自己的权限代理给`Jone`，`Jone`登录后就可以以`Mike`的身份访问系统。

## 用户认证

Cabloy实现了统一的认证框架，不论是`UserPassword`认证，还是Github等第三方认证，都可以非常方便的整合进系统，实现开箱即用的认证功能。

## 前端访问

Cabloy通过vuex机制管理前端的当前登录状态，从而方便其他地方直接引用用户信息。

`egg-born-front/src/base/auth.js`

```javascript
export default function(Vue) {
  return {
    state: {
      loggedIn: false,
      user: null,
    },
    mutations: {
      login(state, { loggedIn, user }) {
        state.loggedIn = loggedIn;
        state.user = user;
        if (user && user.op && user.op.locale) {
          Vue.prototype.$meta.util.cookies.set('locale', user.op.locale);
        }
      },
      logout(state) {
        state.loggedIn = false;
        state.user = null;
      },
    },
  };
}
```

在前端访问举例：

```javascript
const op=this.$store.state.auth.user.op;
const agent=this.$store.state.auth.user.agent;
```

> - 如果没有使用`用户代理`，那么op=agent。
> - 如果使用了`用户代理`，那么，op就是当前操作用户，agent就是登录用户

## 后端访问

因为匿名用户也被分配了实体，所以在后端总是可以访问到用户实例。

```javascript
const op=this.ctx.user.op;
const agent=this.ctx.user.agent;
```
