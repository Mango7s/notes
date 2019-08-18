#### git diff并打包
```
git diff v1 v2 --name-only | xargs zip update.zip
```

### git常见问题

#### git重置仓库地址：

```
git remote set-url origin https://github.com/xxx/xxx.git
```

#### 重置用户密码：

##### linux

```
git config --system --unset credential.helper/git config --global --unset credential.helper

git config credential.helper store
```

##### Windows:

删除C:\Program Files\Git\mingw64\etc\gitconfig

#### 本地文件删除后merge还是不能显示：

git fetch
git reset --hard origin/master  重置
git pull

