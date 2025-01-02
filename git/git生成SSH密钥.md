# 生成 SSH 密钥

"你的邮箱"指的是你注册 github / gitee 时所用的邮箱

```bash
ssh-keygen -t rsa -C '你的邮箱'
```

输入命令后需要敲回车键几次，然后在系统盘的根目录下就会有 .ssh 文件夹，这是一个隐藏的文件夹。

Mac OS用以下命令可以查看 .ssh 文件夹下的公钥

```bash
cat /Users/你的用户名/.ssh/id_rsa.pub 
```

将公匙的内容复制后，到 github 里面创建ssh key，然后将复制的内容粘贴，然后确定

验证公匙是否设置成功，可以使用以下命令
```bash
ssh -T git@github.com
```







