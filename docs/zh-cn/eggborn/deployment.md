# 项目部署

## 构建前端代码
``` bash
$ npm run build:front
```

## 启动后端服务
``` bash
$ npm run start:backend
```

## 停止后端服务
``` bash
$ npm run stop:backend
```

## 后端服务启动参数配置
编辑`build/config.js`文件
``` javascript
// backend
const backend = {
  port: 7002,
  hostname: '127.0.0.1',
};
```

## nginx配置
强烈建议使用nginx托管前端静态资源，并反向代理后端服务，配置如下
``` javascript
server {
  listen 80;
  server_name example.com www.example.com;
  set $node_port 7002;

  root /path/to/www;

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
