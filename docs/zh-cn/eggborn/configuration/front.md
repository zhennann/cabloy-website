# 前端架构配置

## 前端参数配置
`src/front/config/config.js`文件中的参数配置可以覆盖模块的参数
``` javascript
export default{
  modules: {
    'aa-hello': {
      mode: 2,
    },
  },
};
```

## 前端国际化
在`src/front/config/locale`目录添加国际化文件，可以覆盖模块的国际化语言
`zh-cn.js`文件中的语言定义示例如下
``` javascript
export default {
  mode: '模式',
};
```