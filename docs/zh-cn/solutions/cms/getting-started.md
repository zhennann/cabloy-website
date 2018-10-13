# 快速开始

## 安装EggBorn

```bash
$ npm install -g egg-born
```

## 新建Cabloy项目

```bash
$ egg-born cabloy-cms --type=cabloy
$ cd cabloy-cms
$ npm i
```

## 安装CMS模块

```bash
$ npm i egg-born-module-a-cms
```

## 安装CMS主题模块

```bash
$ npm i egg-born-module-cms-themeblog
```

## 配置MySQL

`src/backend/config/config.unittest.js`

``` javascript
  // mysql
  config.mysql = {
    clients: {
      // donnot change the name
      __ebdb: {
        host: '127.0.0.1',
        port: '3306',
        user: 'root',
        password: '',
        database: 'sys', // donnot change the name
      },
    },
  };
```

`src/backend/config/config.local.js`

``` javascript
  // mysql
  config.mysql = {
    clients: {
      // donnot change the name
      __ebdb: {
        host: '127.0.0.1',
        port: '3306',
        user: 'root',
        password: '',
        database: 'sys', // recommended
      },
    },
  };
```

`src/backend/config/config.prod.js`

``` javascript
  // mysql
  config.mysql = {
    clients: {
      // donnot change the name
      __ebdb: {
        host: '127.0.0.1',
        port: '3306',
        user: 'root',
        password: '',
        database: '{{name}}',
      },
    },
  };
```

## 运行

启动后端服务

```bash
$ npm run dev:backend
```

启动前端服务

```bash
$ npm run dev:front
```

## 进入后台管理页面

- 网址：[http://localhost:9092/](http://localhost:9092/)
- 用户名：root
- 密码：123456


