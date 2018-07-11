# 后端架构配置

## 后端架构
后端架构基于Egg.js，完整支持Egg.js提供的所有功能与特性
> 更多信息，请参阅: [Egg.js](https://eggjs.org/)

## 后端参数配置
`src/backend/config/config.default.js`文件中的参数配置可以覆盖模块的参数
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

## 后端国际化
在`src/backend/config/locale`目录添加国际化文件，可以覆盖模块的国际化语言
`zh-cn.js`文件中的语言定义示例如下
``` javascript
module.exports = {
  mode: '模式',
};
```
