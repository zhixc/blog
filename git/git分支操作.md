# git分支操作

查看当前的分支
```bash
git branch
```

创建分支 dev
```bash
git branch dev
```

切换到分支 dev
```bash
git checkout dev
```

注意：上面两条命令相当于下面一条，即创建并且切换到分支 dev
```bash
git checkout -b dev
```

查看所有分支(本地和远程)
```bash
git branch -a
```

查看当前所在分支
```bash
git branch
```

删除本地的 abc 分支
```bash
git branch -d abc
```


删除远程的 abc 分支
```git
git push origin --delete abc
```