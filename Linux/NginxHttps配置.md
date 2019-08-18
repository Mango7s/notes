## Nginx

- ####  下载nginx源码

    wget http://nginx.org/download/nginx-1.10.3.tar.gz 或

    wget http://nginx.org/download/nginx-1.11.0.tar.gz

- ####  编译nginx支持tcp、https
    上传（移动）nginx-1.10.3.tar.gz至/tmp目录

    ```
    yum -y install gcc make pcre pcre-devel zlib-devel libjpeg-turbo-devel zziplib-devel openssl openssl-devel openssl-libs 
    tar zxvf nginx-1.10.3.tar.gz
    cd nginx-1.10.3
    ./configure --prefix=/usr/local/nginx --with-stream --with-http_ssl_module
    make
    cp ./objs/nginx ${nginx}/sbin/
    ```

- ####  生成https证书
    - ###### step1 生成一个RSA密钥

         openssl genrsa -des3 -out server.key 1024

    - ###### step2 拷贝一个不需要输入密码的密钥文件

        openssl rsa -in server.key -out server_nopass.key

    - ###### step3 生成一个证书请求

        openssl req -new -key server.key -out server.csr

    - ###### step4 签发证书

         openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt

    其中：

    第一步需要输入8位以上的密码

    第二步执行时需输入第一步设定的密码
    ​    
    第三步是生成证书请求，会提示输入省份、城市、域名信息等，重要的是，email一定要是你的域名后缀的。这样就有一个 csr 文件了，提交给 ssl 提供商的时候就是这个 csr 文件。

    生成的server.crt、server.key、server.csr放入/usr/local/nginx/sslkey目录下

- ####  配置支持tcp转发（不需要）
  nginx.conf增加stream项
```
    stream {
    include tcp.d/*.conf;
    }
```
tcp.d/*.conf配置如下:
```
upstream wsservice {
	server 10.10.50.151:32577;
}
server {
	listen 32577;
	proxy_connect_timeout 1s;
	proxy_timeout 3s;
	proxy_pass wsservice;
}
```

- ####  配置支持https

  xoj.conf

```
#配置http访问自动跳转https
server {
	listen 80;
	server_name www.xctf.org.cn;
	return 301 https://$host$request_uri;
	}

server {
				 listen 	     443;
        server_name  www.xctf.org.cn;
        ssl on;
        ssl_certificate /usr/local/nginx/sslkey/server.crt;
        ssl_certificate_key /usr/local/nginx/sslkey/server_nopass.key;
        ssl_session_timeout 5m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;
        ssl_prefer_server_ciphers on;

                location / {

                        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                        proxy_set_header Host $http_host;
                        proxy_redirect off;
                        proxy_pass http://127.0.0.1:3000;
			proxy_set_header X-Forwarded-Proto https; 
               }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }

```

- ####  配置支持websocket转发

	参照如下配置：
```
location /websocket/ {
  proxy_pass http://127.0.0.1:8088;
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";
  		}
```

- ####  配置访问控制

	配置在控制范围选择配置在server内或是location或是全局:
```
	allow 192.168.1.9;
	allow 192.168.2.0/24'
	deny all
```
