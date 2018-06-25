# Backend Configuration

## Backend Framework
EggBorn.js backend framework is based on Egg.js，so supports all of the features provided by Egg.js
> For more information，see [Egg.js](https://eggjs.org/)

## Backend Parameters Configuration

The parameters of the file `src/backend/config/config.default.js` can override the parameters of the modules.
``` javascript
module.exports = appInfo => {
  const config = {};

  // module config
  config.modules = {
    'aa-hello': {
      mode: 2,
    },
  };

  return config;
};
```

## Backend I18N

Framework i18n resources can override the modules's ones.

In the directory `src/backend/config/locale`, add the i18n file, such as `zh-cn.js`:
``` javascript
module.exports = {
  mode: '模式',
};
```
