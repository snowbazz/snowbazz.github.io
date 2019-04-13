
#### HUGO

Add some content

```git
hugo new posts/xxx.md
```

Start the Hugo Server

```git
# 启动本地服务器
hugo server -t=we -D
# 访问地址
127.0.0.1：1313
# 重新生成静态blog，t=theme
hugo -t=hyde -D    
```

部署到orrrz.github.io

```git
cd public

git init 

git add .

git commit -m 'xxx'

git remote add origin git@github.com:username/username.github.io.git

git push -u origin master
# --force
```

#### 一键部署

```git
# 在根目录新建 deploy.sh ，复制以下代码并保存
#!/bin/bash

echo -e "\033[0;32mDeploying updates to GitHub...\033[0m"
msg="rebuilding site `date`"
if [ $# -eq 1 ]
then msg="$1"
fi

# 修改为自己的theme
hugo -t=theme -D

cd public
git add -A
git commit -m "$msg"
git push --force -u origin master

cd ../
```

如果是linux系统还需要给脚本添加可执行权限

```git
chmod +x deploy.sh
```

启动

```git
./deploy.sh
```



<hr>

#### 创建git 仓库

```git
mkdir xxx
cd xxx
git init

Initialized empty Git repository in /home/xxx/.git/
```

#### 添加一些文件并提交

```git
touch readme.md
git add readme.md
git commit -m 'for test'
```

#### push前应先将远程repo修改pull下来

```git
git pull origin master

git push -u origin master
```

#### 从远程克隆仓库

```git
# SSH协议/HTTPS协议地址
git clone SSH/HTTPS
```

#### 查看分支

```git
git branch
# 查看本地和远程的分支情况
git branch -a
```

#### 创建分支

```git
git branch xxx
```

#### 切换分支

```git
# 从当前branch切换到 xxx
git checkout xxx
```

#### 创建同时切换分支

```git
# 在当前分支上创建分支 dev，并切换到 dev
git checkout -b dev
```

#### 删除分支

```git
# 不能在要删除的分支上操作
git branch -d dev
```

#### 将本地分支推送到远程分支

```git
# 提交本地的 dev 分支到远程仓库 origin 的 dev 分支
git push origin dev:dev
# 提交本地的 dev 分支到远程仓库 origin 的 remotedev 分支
git push orgin dev:remotedev
```

#### 删除远程分支

```git
# 删除远程仓库的 remotetest 分支，本地的不收影响
git push origin :remotedev
```

#### 拉取远程分支

```git
git pull <remote> <branch>
# 比如 将远程 origin 仓库 testpull 分支拉取下来
git pull origin testpull
```

#### 本地分支与远程分支关联

```git
git branch --set-upstream-to=origin/<branch> dev
# 本地的 dev 分支和远程的 origin/dev 分支关联后，可以直接使用 git pull
git branch --set-upstream-to=origin/dev dev
```

#### 合并分支

```git
# 将test合并到当前分支上
git merge test
```

#### git checkout 远程分支

有时候在远程仓库的基础上，拉取特定的某一个分支

```git
# localdev 是本地分支， origin/dev 是远程的 dev 分支
git checkout -b localdev origin/dev
```

#### git fetch

git fetch 和 git pull 很像，但 git fetch 要柔和一点，git pull 会将远程的命令拉下来直接和本地的合并。git fetch 会先获取信息，但不合并。

```git
git fetch origin dev
# 比较本地版本和远程版本
git log -p dev origin/dev
# 合并远程 dev 版本
git merge origin/dev
```