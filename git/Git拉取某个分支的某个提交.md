# Git拉取某个分支的某个提交

当前有A、B两个分支，

A分支commits有 a1, a2, a3, a4, a5

B分支commits有 b1, b2, b3, b4, b5

注：以上10次提交的hash值不一样。

现在要从B分支上面拉取 b1 的内容到 A分支上

1.先切换到B分支
```git
git checkout B
```

2.然后查看提交记录
```git
git log
```

3.找到 b1 的 commit 的 hash 值，并复制，假设b1的commit 的 hash值为 6e82d， 然后按wq退出，接着切换到A分支
```git
git checkout A
```

4.用 git cherry-pick <commit_id> 命令来将B分支的 b1 内容合并到 A 分支上，<commit_id>替换成为 b1的commit的hash值
```git
git cherry-pick 6e82d
```

5.1如果失败了，说明产生冲突了，手动把冲突解决后，输入下面的命令就拉取成功了
```git
git cherry-pick --continue
```

5.2如果失败了，不想继续拉取 b1，那么输入以下命令，放弃解决冲突
```git
git cherry-pick --abort
```

6.拉取成功后，推送到远程，输入以下命令
```git
git push
```

