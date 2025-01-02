# Git的提交

## 1.初始化仓库
```bash
git init
```

## 2.添加所有文件到暂存区
```bash
git add .
```

## 3.提交到本地仓库
```bash
git commit -m "备注信息可以任意填写"
```

在推送至远程仓库前要给有git公钥，git公钥生成和github账号设置公钥可以参考另一篇文章[生成SSH密钥](./生成SSH密钥.md)

## 4.SSH关联远程仓库
我在github上面简历了一个远程仓库 blog
```bash
git remote add origin git@github.com:zhixc/blog.git
```

## 5.设置分支
```bash
git branch -M main
```

## 6.推送至远程仓库
```bash
git push -u origin main
```

