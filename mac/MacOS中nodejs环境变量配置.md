# MacOS 中压缩包版 nodejs 环境变量配置

## 前言
> 如果你是pkg安装包安装的，那么后面的步骤可以不用看了，因为pkg安装的会自动配置环境变量，这篇主要介绍的是压缩包版的配置环境变量，压缩包版无需安装，解压后配置环境变量后即可使用。


## 先确定当前用户使用的是那种 shell

要确定当前 macOS 上使用的是哪种 shell（Bash、Zsh 等），可以执行以下步骤：

**打开终端：** 打开终端应用程序。

**查看当前 shell：** 在终端中运行以下命令：

```bash
echo $SHELL
```
这将显示当前正在使用的 shell 的路径。通常，如果你看到 /bin/bash，则表示当前使用的是 Bash shell；如果看到 /bin/zsh，则表示当前使用的是 Zsh shell。

**查看系统默认 shell：** 另外，你也可以查看系统默认的 shell。在终端中运行以下命令：

```bash
dscl . -read /Users/$(whoami) UserShell
```
这将显示系统为当前用户设置的默认 shell。如果显示 /bin/bash，则表示默认为 Bash shell；如果显示 /bin/zsh，则表示默认为 Zsh shell。

通过以上方法，你就可以确定当前 macOS 上正在使用的是哪种 shell。


## 然后再配置 nodejs 环境变量

本次演示的例子用的是 node-v18.19.1-darwin-x64.tar.gz

解压 node-v18.19.1-darwin-x64.tar.gz

得到 node-v18.19.1-darwin-x64 文件夹，然后将这个文件夹移动到你喜欢的位置，我移动到 /Users/z/dev/ 目录下

此时该文件夹的路径是：/Users/z/dev/node-v18.19.1-darwin-x64

bin可执行文件的路径是：/Users/z/dev/node-v18.19.1-darwin-x64/bin

在 macOS 上配置 Node.js 的环境变量可以通过以下步骤完成：

**查找 Node.js 安装路径：** 由上面的步骤，以我的例子，得到路径是 /Users/z/dev/node-v18.19.1-darwin-x64/bin

**编辑配置文件：** 打开终端应用程序，然后编辑你的 shell 配置文件。如果你使用的是 Bash shell，可以编辑 ~/.bash_profile 或 ~/.bashrc 文件；如果是使用的是 Zsh shell，则编辑 ~/.zshrc 文件。

**添加环境变量：** 在配置文件中添加以下行：

```bash
export PATH="/Users/z/dev/node-v18.19.1-darwin-x64/bin:$PATH"
```

这将把 Node.js 的安装路径添加到系统的 PATH 变量中，使得你可以在终端中直接运行 node 和 npm 命令。

**保存并应用修改：** 保存文件并关闭编辑器。然后，在终端中运行以下命令，使修改生效：

```bash
source ~/.bash_profile   # 如果使用的是 Bash shell
```

或者

```bash
source ~/.zshrc   # 如果使用的是 Zsh shell
```
**验证配置是否生效：** 在终端中运行以下命令，检查 Node.js 和 npm 是否已经正确配置：

```bash
node -v
npm -v
```

如果一切顺利，你应该能够看到安装的 Node.js 和 npm 的版本号。

完成以上步骤后，你的 macOS 环境就已经成功配置了 Node.js 的环境变量。

