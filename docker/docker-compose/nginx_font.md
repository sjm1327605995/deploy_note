### Nginx制作镜像代理Vue和React前端

要求：

1. 打包后形成目录 `dist`，且存在 `dist/index.html` 文件。
2. 将 `dist` 目录下的所有文件拷贝到Nginx的 `html` 目录下。
3. 修改 `nginx.conf` 文件，使其指向 `dist` 目录。

以下方法形成的镜像可以直接用作Vue和React的基础镜像：

- **nginx/conf.d 下的 `my.conf` 文件内容如下：**

```text
server {
    listen       80;
    server_name localhost;

    #charset koi8-r;
    #access_log  logs/host.access.log  main;

    location / {
        root /usr/share/nginx/html;
        add_header Cache-Control 'no-store, no-cache, must-revalidate, proxy-revalidate, max-age=0';
        try_files $uri $uri/ /index.html;
    }
}
```

Dockerfile 内容如下：

```Dockerfile
FROM nginx:alpine
COPY nginx/conf.d/my.conf /etc/nginx/conf.d/my.conf
```

build 镜像：

```bash
docker build -t web-nginx .
```

#### vue前端镜像方法
- **Dockerfile 内容如下：**
```Dockerfile
FROM docker.sywldrmd.com:60443/sywldrmd/nginx-web:latest
COPY dist /usr/share/nginx/html
EXPOSE 80
```