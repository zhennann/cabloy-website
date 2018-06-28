# Getting Started

## Installation

``` bash
$ npm install -g egg-born
```

## Create a project

``` bash
$ egg-born project_name
$ cd project_name
$ npm install
```

> Currently, EggBorn.js offers two scaffolds:
> - `cabloy`  -- full stack project template
> - `module`  -- module template

## Configure MySQL

Edit file: `src/backend/config/config.unittest.js`

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

Edit file: `src/backend/config/config.local.js`

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

Edit file: `src/backend/config/config.prod.js`

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

## Run

Start Backend Service
``` bash
$ npm run dev:backend
```

Start Frontend Service
``` bash
$ npm run dev:front
```

## Test

```bash
$ npm run test:backend
```
