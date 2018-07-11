# Frontend Configuration

## Frontend Parameters Configuration

The parameters of the file `src/front/config/config.js` can override the parameters of the modules
``` javascript
export default{
  modules: {
    'aa-hello': {
      mode: 2,
    },
  },
};
```

## Frontend I18N

Framework i18n resources can override the modules's ones.

In the directory `src/front/config/locale`, add the i18n file, such as `zh-cn.js`:
``` javascript
export default {
  mode: '模式',
};
```
