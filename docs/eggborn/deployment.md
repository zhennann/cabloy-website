# Project Deployment

## Build Frontend
``` bash
$ npm run build:front
```

## Start Backend
``` bash
$ npm run start:backend
```

## Stop Backend
``` bash
$ npm run stop:backend
```

## Backend Start Parameters
Edit the file `build/config.js`:
``` javascript
// backend
const backend = {
  port: 7002,
  hostname: '127.0.0.1',
};
```

## Nginx Configuration

Suggest to use Nginx hosting frontend static resources and reverse proxy backend service
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
