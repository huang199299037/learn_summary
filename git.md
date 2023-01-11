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
```

