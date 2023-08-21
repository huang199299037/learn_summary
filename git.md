# git

## remote

```bash
# 作用是显示所有远程仓库
git remote -v  

# (origin为远程地址的别名) 显示某个远程仓库的信息
git remote show origin

# 重命名remote
git remote rename origin destination # 将远程名称从 'origin' 更改为 'destinatio

# add remote
git remote add origin [url]

#  删除远程仓库
git remote rm origin 

# 删除远程分支develop
git push origin --delete develop
```

## branch

```bash
# 删除分支名
git branch -d develop

# 强制删除分支名
git branch -D develop

# branch 重命名
git branch -m 原始名称 新名称

# 切换到远程分支
git checkout -b 本地分支名 远程分支名

65062a46 108a0d0e
```

## clash for windows

```
$ git fetch origin
Connection closed by 20.205.243.166 port 22
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.

fix：
https://github.com/vernesong/OpenClash/issues/1960
https://docs.github.com/zh/authentication/troubleshooting-ssh/using-ssh-over-the-https-port
```

