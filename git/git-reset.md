# Git的版本回退

## 1.查看提交的版本信息
```bash
git log
```

## 2.回退到指定的版本号，如 cc788e8
```bash
git reset –hard cc788e8
```

## 3.远程仓库强制回退
```bash
git push --force
```

<font color="red">注意：这种回退版本的方式比较硬核，会丢失当前已经提交到本地仓库的一些内容。</font>