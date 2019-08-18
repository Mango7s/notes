## **Supervisor相关配置**

### 安装supervisor

```
pip install supervisor
```
生成配置文件
```
echo_supervisord_conf > supervisord.conf(生成在当前路径下)
```
mv到/etc下


### 管理Nginx
```shell
touch /etc/supervisord.d/service.conf

cat <<"EOF" > /etc/supervisord.d/service.conf

[program:nginx]

command=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/xoj.conf

autostart=true

autorestart=true

user=root
```

### 配置app
```shell
touch /etc/supervisord.d/app.conf

cat <<"EOF" > /etc/supervisord.d/app.conf

[program:app]

command=/root/.virtualenvs/xoj/bin/gunicorn app.wsgi -c gunicorn_config.py

directory=/home/app/

environment=JAVA_HOME=/usr/lib/jvm/java

autostart=true

autorestart=true

user=root
```