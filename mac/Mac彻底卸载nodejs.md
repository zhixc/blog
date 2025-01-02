# Mac彻底卸载node js

1.要彻底卸载 Node.js，可以按照以下步骤进行操作：

首先，使用终端命令行或者 Finder 找到 Node.js 的安装目录，一般情况下默认安装在 /usr/local 或者 /usr/local/bin 目录下。

2.在终端中输入以下命令来删除 Node.js 的安装目录及其相关文件：

```bash
sudo rm -rf /usr/local/bin/node
sudo rm -rf /usr/local/bin/npm
sudo rm -rf /usr/local/lib/node_modules
sudo rm -rf /usr/local/include/node
sudo rm -rf /usr/local/share/man/man1/node.1
sudo rm -rf /usr/local/lib/dtrace/node.d
sudo rm -rf /usr/local/share/systemtap/tapset/node.stp
sudo rm -rf /usr/local/bin/node-waf
sudo rm -rf /usr/local/include/node_modules
```

3.接着，还需要删除 Node.js 的配置文件，输入以下命令：
```bash
sudo rm -rf ~/.npm
sudo rm -rf ~/.node-gyp
```

4.最后，可以使用以下命令来验证是否已经彻底卸载 Node.js：
```bash
node -v
npm -v
```

如果以上命令没有输出任何信息，那么说明 Node.js 已经被成功卸载。

请注意，在执行以上命令时需要小心谨慎，确保知道自己在做什么，以免误删其他重要文件。
