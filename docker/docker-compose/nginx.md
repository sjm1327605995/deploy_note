### Nginx

#### 单域名简单配置

Docker-compose 配置文件 `docker-compose.yml`：

```yaml
version: "3"
services:
   web:
     #定义主机名
     container_name: nginx
     #使用的镜像
     image: nginx
     #容器的映射端口
     ports:
       - 80:80
     #定义挂载点         
     volumes:
       - ./html:/usr/share/nginx/html
       - ./conf/nginx.conf:/etc/nginx/nginx.conf
       - ./conf.d:/etc/nginx/conf.d
       - ./logs:/var/log/nginx
     #docker 重启后，容器自启动
     restart: always
```

nginx 配置文件 `conf/nginx.conf`：

```text
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;
     #允许上传文件大小
    client_max_body_size 50M; 
    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}

```
conf.d 目录下存放其他配置文件，如：可以有多个配置。这里给出一个简单的示例：
default.conf：
```text
server{
       listen 443 ssl;
       server_name  xxx.xxx;
       charset utf-8;

       # location ~ .*\.(#txt)$ {
       #   # 此处一定要改成nginx容器中的目录地址，宿主机上的地址容器访问不到
       #   # 命令必须用 root, 不能用 alias
       #  root   /usr/share/nginx/html;
       #  try_files $uri $uri/ =404;
        
       # }
       
       ssl_certificate /etc/nginx/cert/xxx.pem;
       #填写证书私钥文件绝对路径
       ssl_certificate_key /etc/nginx/cert/xx.key;
 
       ssl_session_cache shared:SSL:1m;
       ssl_session_timeout 5m;
	 
       #自定义设置使用的TLS协议的类型以及加密套件（以下为配置示例，请您自行评估是否需要配置）
       #TLS协议版本越高，HTTPS通信的安全性越高，但是相较于低版本TLS协议，高版本TLS协议对浏览器的兼容性较差。
       ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
       ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;

       #表示优先使用服务端加密套件。默认开启
       ssl_prefer_server_ciphers on;       
        #error_page  404              /404.html;

       # redirect server error pages to the static page /50x.html
       #
       error_page   500 502 503 504  /50x.html;
       location = /50x.html {
           root   html;
       }
    }

    #server {
    #listen 80;
    #填写证书绑定的域名
    #server_name xxx.xxx;
    ##将所有HTTP请求通过rewrite指令重定向到HTTPS。
    #rewrite ^(.*)$ https://$host$1;
   
    #}
```