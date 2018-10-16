# 部署

## 实例与子域名

Cabloy支持多实例，实例与网站子域名一一对应，不同实例的数据完全隔离。比如`instance1.cabloy.org`和`instance2.cabloy.org`

在`调试`阶段，Cabloy启用了一个缺省实例，但在`部署`阶段，需要规划实例与子域名

在CMS应用中，把域名`example.com`留给静态文件，需要给后台管理系统分配一个子域名，如`admin.example.com`

## 多站点支持

由于一个实例对应一个CMS应用，通过`多实例`就可以支持`多站点`了

## 实例配置

编辑文件：`src/backend/config/config.prod.js`

```javascript
// instances
config.instances = [
  { subdomain: 'admin', password: '', title: '',
    meta: {
      jsonp: { whiteList: 'example.com' },
    },
  },
];
```

> - subdomain: 子域名
> - password: 实例中用户`root`的访问密码
> - title: 网站标题
> - meta.jsonp.whiteList: 白名单，只有白名单中的域名可以通过ajax访问后台API接口

## 构建前端代码
``` bash
$ npm run build:front
```

## 启动后端服务
``` bash
$ npm run start:backend
```

- 别忘了创建MySQL数据库，并配置`src/backend/config/config.prod.js`

## 停止后端服务
``` bash
$ npm run stop:backend
```

## 后端服务启动参数配置

编辑文件：`build/config.js`

``` javascript
// backend
const backend = {
  port: 7002,
  hostname: '127.0.0.1',
};
```

## nginx配置

强烈建议使用nginx托管前端静态资源，并反向代理后端服务

在项目根目录已经生成了两个nginx配置文件，分别对应Cabloy-CMS的“动态部分”和“静态部分”，请根据实际情况修改

### 动态部分：nginx.conf

``` javascript
server {

  listen 80;
  server_name admin.example.com;
  set $node_port 7002;

  root /Users/wind/Documents/temp/cabloy-cms/dist;

  location /public {
    root /Users/wind/cabloy/cabloy-cms;
    internal;
  }

  location  /api/ {
    proxy_http_version 1.1;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Host $server_name;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header Host $http_host;
    proxy_set_header X-NginX-Proxy true;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_pass http://127.0.0.1:$node_port$request_uri;
    proxy_redirect off;
  }

}
```

### 静态部分：nginx-cms.conf

``` javascript
server {

  listen 80;
  server_name example.com;

  root /Users/wind/cabloy/cabloy-cms/public/1/cms/dist;

}
```



