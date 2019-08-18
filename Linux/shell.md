#### 查看当前ip归属地
```shell
curl ip.gs
```
#### 端口扫描

```shell
# 扫描1-1000
sudo nmap -sA -Pn 195.172.xx.xx -p1-1000
```

#### 替换文本(vim/sed)

```shell
# 内容替换
s#{text1}#{text2}#g  
s/{text1}/{text2}/g
s_{text1}_{text2}_g
#例：关闭selinux
sed -i 's#SELINUX=enforcing#SELINUX=disable#g' /ect/sysconfig/selinux
# vim删除查找到的行
g/{text}/d
# shell删除查找到的行
sed -i '/{text}/d' file

# 行操作
#{text1}#{text2}
/{text1}/{text2}
_{text1}_{text2}
```
