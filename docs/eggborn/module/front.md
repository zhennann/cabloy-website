# Module Frontend Development

## Frontend Page Route
In the file `front/src/routes.js`, add the page route:
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

> - `path`: supports parameters. If the path starts with `/`, represent the root page component. `login` page component is usually configured with `/`
> - `component`: page component
> - `meta`: route metadata
> - `meta.requiresAuth`: If the page component needs to be logged in, it should be set to `true`

If you reference the page component in the page, please use the absolute path:
``` html
<f7-list-item link="/aa/hello/welcome/You" title="Welcome"></f7-list-item>
<f7-list-item link="/aa/hello/profile" title="Profile"></f7-list-item>
```

## Frontend State Management

Vuex is a state management library, which is developed specifically for Vue.js applications. EggBorn.js uses Vuex to implement a fully isolated module state management mechanism.

In the file `front/src/store.js`, add the state:
``` javascript
export default function(Vue) {

  return {
    state: {
      message: 'hello world',
    },
  };

}
```

Access the state of the same module in the page component:
``` javascript
const message = this.$local.state.message;
```

Access the state of the other module in the page component:
``` javascript
const message = this.$store.state[providerId][moduleName].message;
```

> For more information, see [Vuex](https://vuex.vuejs.org/)

## Frontend Parameters Configuration

In the file `front/src/config/config.js`, add the configuration information:
``` javascript
export default {
  mode: 1,
};
```

Access the config of the same module in the page component:
``` javascript
const mode = this.$config.mode;
```

## Frontend I18N

In the directory `front/src/config/locale`, add the i18n file, such as `zh-cn.js`:
``` javascript
export default {
  mode: '模式',
  "Hello world! I'm %s.": '您好，世界！我是%s。',  
};
```

I18N resources can be merged globally, so as to share i18n resources through all the modules.

Access the i18n resources in the page component:
``` javascript
const mode = this.$text('mode');
const message = this.$text("Hello world! I'm %s.",'zhennann');
```
