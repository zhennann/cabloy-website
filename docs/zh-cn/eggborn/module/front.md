# 模块前端开发

## 前端页面路由
在`front/src/routes.js`中添加页面路由，如
``` javascript
function load(name) {
  return require(`./pages/${name}.vue`).default;
}

export default [
  { path: 'welcome/:who', component: load('welcome') },
  { path: 'profile', component: load('profile'), meta: { requiresAuth: true } },
  { path: '/login', component: load('login') },
];
```

> - `path`: 路径，支持参数。以`/`开头，代表根页面组件。`login`页面组件通常这样配置
> - `component`: 页面组件对象
> - `meta`: 路由元数据
> - `meta.requiresAuth`: 如果页面组件需要登录，须设为`true`

在页面中引用页面组件，请使用绝对路径，如
``` html
<f7-list-item link="/aa/hello/welcome/You" title="Welcome"></f7-list-item>
<f7-list-item link="/aa/hello/profile" title="Profile"></f7-list-item>
```

## 前端状态管理
Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。EggBorn.js采用Vuex实现了完全隔离的模块状态管理机制。
在`front/src/store.js`中添加状态，如
``` javascript
export default function(Vue) {

  return {
    state: {
      message: 'hello world',
    },
  };

}
```

在页面组件中访问本模块状态
``` javascript
const message = this.$local.state.message;
```

在页面组件中访问其他模块状态
``` javascript
const message = this.$store.state[providerId][moduleName].message;
```

> 更多信息，请参阅: [Vuex](https://vuex.vuejs.org/)

## 前端参数配置
在`front/src/config/config.js`中添加配置信息，如
``` javascript
export default {
  mode: 1,
};
```

只支持在页面组件中访问本模块内部的参数配置
``` javascript
const mode = this.$config.mode;
```

## 前端国际化
在`front/src/config/locale`目录添加国际化文件
`zh-cn.js`文件中的语言定义示例如下
``` javascript
export default {
  mode: '模式',
  "Hello world! I'm %s.": '您好，世界！我是%s。',  
};
```

国际化语言采取全局合并的方式，有利于语言资源的共享，在页面组件中访问方式如下
``` javascript
const mode = this.$text('mode');
const message = this.$text("Hello world! I'm %s.",'zhennann');
```
