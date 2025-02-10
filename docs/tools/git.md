# Git

## 修改最后一次提交的commit信息

```shell
git commit --amend
```

## 撤销上一次的commit

```shell
git reset HEAD~
```

## 修改remote url

* 直接修改

```shell
git remote set-url origin git@github.com:UserName/Repository.git
```

* 先删后加

```shell
git remote rm origin
git remote add origin git@github.com:UserName/Repository.git
```

## 同时推送多个平台

```shell
#github
git remote add origin git@github.com:UserName/Repository.git

#码云
git remote set-url --add origin git@gitee.com:UserName/Repository.git
```

## 回滚上次push的提交

```bash
git reset --hard HEAD^
git add .
git commit
#强推
git push -f origin <branch_name>
```

## 保持fork之后的项目和上游同步

```bash
#添加上游仓库
git remote add upstream [upstream_url]

#fetch
git fetch upstream

#将upstream/<branch_name> merge到本地当前分支
git merge upstream/<branch_name>

#同时别忘了push到自己的github仓库
git push origin <branch_name>
```

## 合并某个commitId

```bash
git cherry-pick commitId
```

## 22 to 443

git的ssh使用22端口不可用时，可以使用443端口

```bash
vim ~/.ssh/config

#写入
Host github.com
  Hostname ssh.github.com
  Port 443
```

## 设置SS代理

```bash
vim ~/.ssh/config

#写入
Host github.com
   HostName github.com
   User git
   ProxyCommand nc -v -x 127.0.0.1:1086 %h %p
```

!> 注意配置文件内的 127.0.0.1:1086 需要和本地 SS 客户端的所暴露的 本地 Socks5 监听地址 和 本地 Socks5 监听端口 一致

## WebHook

使用Swoole的`Http\Server`来作为Web服务，执行GitHub/Gitee的WebHook

[git-deploy](https://github.com/sy-records/git-deploy)

## 压缩/合并commit

```shell
git rebase -i HEAD~[number]

# example : git rebase -i HEAD~4
# HEAD～4的含义是从头部开始追溯4条记录
```

执行后进入编辑模式，将需要压缩的commit前面的`pick`改为`squash`

```shell
# 取消
git rebase --abort

# 因为冲突打断的话 执行continue
git rebase --continue

# 强推
git push -f origin <branch_name>
```

## 清除本地 remotes/origin/*

```shell
git remote prune origin
```

## 删除远程分支

```shell
git branch -r -d origin/<branch_name>
git push origin :<branch_name>
```

## 删除远程不存在但存在本地的分支

```bash
git branch -vv | grep ': gone]' | awk '{print $1}' | xargs git branch -D
```

## 修改分支名称

```shell
# 本地分支重命名
git branch -m oldName newName
# 将重命名后的分支推送到远程
git push origin newName
# 删除远程的旧分支
git push --delete origin oldName

git branch --unset-upstream
git branch --set-upstream-to=origin/<branch_name> <branch_name>
```

## 使用远程覆盖本地

远程分支强推后，本地文件冲突，需要使用远程文件覆盖本地文件时使用。

```shell
git fetch --all
git reset --hard origin/<branch_name>
```

## 批量创建/删除.gitignore文件

```shell
find . -type d -empty -exec touch {}/.gitignore \;

find ./ -type f -name '.gitignore' -delete
```

## 将某个分支的代码完全覆盖另一个分支

```shell
#将test分支上的代码完全覆盖dev分支
git checkout dev
git reset --hard origin/test
git push -f
```

## 删除中间某次提交

```shell
#commit-id 为要删除的commit的下一个commit号
git rebase -i commit-id

#要删除的commit之前的改为drop
git push -f
```

## 修改提交日期

```shell
git filter-branch --env-filter \
'if [ $GIT_COMMIT = f70cf6dc1dabb4ad0523a3259681b1e0fe43a788 ]
then
    export GIT_AUTHOR_DATE="Sun Apr 4 09:30:01 2021 +0800"
    export GIT_COMMITTER_DATE="Sun Apr 4 09:30:01 2021 +0800"
fi'
```

## 获取最后一次 commit id

```shell
git log --pretty=format:"%h" | head -1
```

https://git-scm.com/docs/pretty-formats

## 非SSH模式免密

```shell
echo 'http://username:password@githost' >> ~/.git-credentials
```

## Patch

```bash
# 所有文件
git diff > xxx.patch

# 指定文件
git diff file1 file2 > xxx.patch

# 应用patch
git apply xxx.patch
```

## 获取两个分支/commitID的差异文件

```bash
git diff master dev --stat --name-only
git diff xxxxxx xxxxxx --stat --name-only
```

## Git 提交统计

### 指定时间内某个人的提交代码行数

```bash
git log --author="username" --after="2022-08-01 00:00:01" --before="2022-09-15 23:59:59" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -
```

### 某个人的提交代码行数

```bash
git log --author="username" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -
```

### 按用户查看提交代码行数

```bash
git log --format='%aN' | sort -u | while read name; do echo -en "$name\t"; git log --author="$name" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -; done
```

### 查看每个用户的提交次数

```bash
git shortlog -s -n 
```

## Tag

### 删除本地tag

```bash
git tag -d <tagname>
```

### 删除远程tag

```bash
git push origin :refs/tags/<tagname>
```

## 获取某个分支的文件

```bash
git checkout branchName -- filepath
```

## 生成patch并合并

```bash
git format-patch -1 commitId -o  ~/GitHub/sy-records/path
git apply --reject patchfile
```
