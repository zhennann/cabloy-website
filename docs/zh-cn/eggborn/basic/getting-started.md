# 快速开始

## 安装

```bash
$ npm install -g egg-born
```

## 新建项目

```bash
$ egg-born cabloy-demo --type=cabloy
$ cd cabloy-demo
$ npm i
```

## 配置MySQL

修改文件: `src/backend/config/config.unittest.js`

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
        database: 'sys',
      },
    },
  };
```

修改文件: `src/backend/config/config.local.js`

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
        database: 'sys',
      },
    },
  };
```

修改文件: `src/backend/config/config.prod.js`

``` javascript
  // mysql
  config.mysql = {
    clients: {
      // donnot change the name
      __ebdb: {
        host: '127.0.0.1',
        port: '3306',
        user: 'root', // 'travis',
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

## 测试

```bash
$ npm run test:backend
```
