# Github之pull request

Github 通过发起 pull request 提交代码到他人仓库，当仓库管理者审核后进行 merge ，代码就会合并到他人的仓库里面

步骤一般如下：

1.在发起 pull request 之前，先 fork 别人的仓库

2.将仓库克隆到本地，创建一个仓库分支(如dev)，修改一些代码，然后 commit 到本地仓库，接着 push 到远程仓库分支(如dev)

部分命令操作如下
```bash
git checkout -b 分支名
git add .
git commit -m "xxxxx"
git push origin 分支名
```

3.上github查看自己 fork 的仓库，可以看到有 Compare & pull request 按钮，点击后按提示操作即可发起 pull request

参考的详细教程：https://blog.csdn.net/CY2333333/article/details/113731490

