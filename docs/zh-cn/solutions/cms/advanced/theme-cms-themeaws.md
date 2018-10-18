# 主题：cms-themeaws

Cabloy-CMS提供了`主题继承`的机制，从而方便在原有主题的基础上修改个别文件，从而快速创建一个新的主题

`cms-themeblog`就是在`cms-themeblog`的基础上创建的新主题

## 操作步骤

继承`主题`一般只需要三个步骤

### 声明继承关系

`cms-themeaws/package.json`

``` javascript
{
  "name": "egg-born-module-cms-themeaws",
  "version": "1.0.0",
  "title": "cms:theme:aws",
  "eggBornModule": {
    "cms": {
      "name": "aws",
      "theme": true,
      "extend": "cms-themeblog"
    },
    "dependencies": {
      "a-instance": "1.0.0"
    }
  },
  ...
  "dependencies": {
    ...
    "egg-born-module-cms-themeblog": "^1.1.3"
  }
}
```

- `"extend": "cms-themeblog"`: 声明继承自主题`cms-themeblog`
- `"egg-born-module-cms-themeblog": "^1.1.3"`: 声明模块依赖，从而自动安装原主题

### 覆盖默认参数

`cms-themeaws/backend/src/config/config.js`

``` javascript
// theme
config.theme = {
  _theme: {
    name: 'cms-themeaws',
    url: 'https://github.com/zhennann/egg-born-module-cms-themeaws',
  },
};
```

- _theme: 是主题`cms-themeblog`提供的参数，在这里进行覆盖

### 默认源码和资源

根据需要覆盖原有主题的源码和资源，在这个主题中只覆盖了一个CSS样式文件

`cms-themeaws/backend/cms/theme/assets/css/site.css.ejs`

``` css
body {
  font-size:14px;
  color:#666;
  background: #FAFAFA;
}

...

a {
  color: #007eb9;
}
a:visited{
  color:#005b86;
}
a:hover,a:focus{
  color:#e47911;
}
```
