
### docker创建
```
docker run -it <img_name> /bin/bash
授权docker root
docker run -itd --privileged 8a2b05e9a631 /usr/sbin/init
docker run -itd -p 445:445 -p 8000:8000 --privileged b4b76b6a68c7 /usr/sbin/init
```

### docker镜像打标签
```
docker tag [image id] [name]:[tag]
```

### docker容器启动
```
docker start <contianer_id>
```

### docker进入容器
```
docker exec -it <contianer_id> /bin/bash
```

### docker file创建&&及docker build构建docker镜像
```
vim DockerFile

FROM ubuntu_base_traffic:latest # 源镜像
EXPOSE 6000 # 暴露端口
ADD start.sh /start.sh
RUN chmod 755 /start.sh
# CMD ["/start.sh"] # 启动命令
ENTRYPOINT ["sh", "/start.sh"] # 启动命令

docker build -t ubuntu_base_traffic:3.0 -f ./DockerFile
-t: 指定新镜像的[名称]:[tag]
-f: 指定DockerFile
```
### 保存容器为镜像
```
docker commit -m 'commit msg' <contianer_id> <img_name>
```

### 导出镜像
```
docker save img -o  file_path
```

### 导出镜像
```
docker load -i file_path
```

### 导入至openstack
```
openstack image create --file FILE_PATH --disk-format raw --container-format docker --public NAME
```

