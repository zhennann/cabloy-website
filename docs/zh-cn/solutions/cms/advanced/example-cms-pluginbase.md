# 举例：主题：cms-pluginbase

模块`cms-pluginbase`是官方提供的基础插件，建议其他`主题`引用此插件模块，在此插件提供的基础功能上灵活定制自己的逻辑

当然，也可以不使用此插件模块，完全实现自己的`主题`和`插件`的组合

## 插件定义

`cms-pluginbase/package.json`

``` javascript
{
  "name": "egg-born-module-cms-pluginbase",
  "version": "1.0.9",
  "title": "cms:plugin:base",
  "eggBornModule": {
    "cms": {
      "name": "base",
      "plugin": true
    },
    "dependencies": {
      "a-instance": "1.0.0"
    }
  },
  "dependencies": {
    ...
  }
}

```

## 文件结构

插件`cms-pluginbase`
