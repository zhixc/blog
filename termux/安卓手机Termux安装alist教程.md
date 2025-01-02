# 安卓手机 Termux 安装 alist 教程

## 1.下载 Termux 安卓版

Termux开源项目地址：https://github.com/termux/termux-app

直接访问：https://github.com/termux/termux-app/releases

在 Assets 里面选择适合自己手机的安装包，下载安装即可。

## 2.安装 alist

打开 Termux，然后输入如下命令：
```bash
pkg install alist
```
然后回车，接下来就会进行下载alist和自动安装alist了。

备注：手机上面的回车符号类似这种：<img src="./images/回车符.jpg" alt="回车符" title="回车符" style="width:20px;height:20px">

## 3.查看 alist 管理员账号和密码
输入以下命令
```bash
alist admin
```
回车后即可看到管理员账号和对应的密码。记住账号和密码。

## 4.启动 alist
输入以下命令并且回车后，即可启动 alist
```bash
alist server
```

## 5.访问和管理 alsit
Termux不要关，保持在后台运行，然后打开手机里面的浏览器，访问: http://127.0.0.1:5244

用第4步得到的账号和密码登录即可。

## 6.额外的补充

卸载 alist 命令：
```bash
pkg uninstall alist
```
升级 alsit 命令：
```bash
pkg upgrade alist
```

如果不想运行alist了，在 Termux 里面先点击 <strong>CTRL</strong> 键，然后再点击 <strong>C</strong> 键，其实对应的就是电脑键盘上的 control + C ，这是命令行终止程序的通用方法。
另外也可以直接关闭 Termux ，当 Termux 软件停止运行时，alist 自然也会停止运行。
