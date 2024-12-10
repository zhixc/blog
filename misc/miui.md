### MIUI的ROM下载
- https://xiaomirom.com


### macOS 使用 adb 给手机刷 recovery

先下载 adb 命令行工具


刷第三方 recovery 

举例子：将第三方recovery文件如 LOS_Recovery.img 放到 platform-tools 目录下，终端进入 platform-tools 目录，手机重启到 fastboot 模式下(需要已经解锁bl)，然后执行如下命令，即可刷入：

```bash
./fastboot flash recovery LOS_Recovery.img
```

联发科取消 vb 验证，防止无限重启，需要刷入 vbmeta.img 文件，执行如下命令

```bash
./fastboot --disable-verity --disable-verification flash vbmeta vbmeta.img
```

成功刷入第三方 recovery 后，重启进入 recovery ，执行如下命令，即可进入

```bash
./fastboot reboot recovery
```




[不登陆小米账号启用 MIUI 的 ADB 调试（安全设置）和 ADB 应用安装 - 哔哩哔哩](https://www.bilibili.com/read/cv21517358/)

```txt
    <boolean name="permcenter_install_intercept_enabled" value="false" />
    <boolean name="security_adb_install_enable" value="true" />
```



