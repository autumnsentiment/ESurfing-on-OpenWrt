# ESurfing-on-OpenWrt
基于Rsplwe大佬的ESurfing源码，实现在OpenWrt上验证广东电信校园网

**实现效果**


_后续进行图片替换_

# 准备工作

1.一台x86/64或arm64(armv8)平台，内存要求>=512M，存储/闪存要求>=1024M。

2.刷写好OpenWrt固件（刷写步骤请参考其他教程）

3.准备MobaXterm或Xshell，用于ssh访问和文件传输

# 步骤

1. **下载源码**
> https://github.com/Rsplwe/ESurfingDialer

![image](https://github.com/user-attachments/assets/6bb12a0c-30ee-4919-b9a4-c9ab58488bfb)



**上传至openwrt的root目录下**
![image](https://github.com/user-attachments/assets/97c700ae-ec82-4950-ae1f-14ebac359f4f)


2. **安装openjdk21**

**由于opkg内没有openjdk库，需要借助Alpine进行安装**

先安装apk：
```bash
opkg update && opkg install apk alpine-keys alpine-repositories
```

编辑源文件：
```bash
vi /etc/apk/repositories
```

换源，官方源和清华源都可以

官方源：
```bash
https://dl-cdn.alpinelinux.org/alpine/latest-stable/main
https://dl-cdn.alpinelinux.org/alpine/latest-stable/community
```
清华源：
```bash
https://mirrors.tuna.tsinghua.edu.cn/alpine/latest-stable/main
https://mirrors.tuna.tsinghua.edu.cn/alpine/latest-stable/community
```

更换源后保存退出，并更新:
```bash
apk update
```

**由于https://www.alpinelinux.org/posts/Alpine-edge-signing-keys-rotated.html，更新了签名密钥，导致出现报错UNTRUSTED signature，可添加--allow-untrusted参数**
解决方法：

_官方源：_
```bash
apk add -X https://dl-cdn.alpinelinux.org/alpine/latest-stable/main -u alpine-keys  --allow-untrusted
```
_清华源：_
```bash
apk add -X https://mirrors.tuna.tsinghua.edu.cn/alpine/latest-stable/main -u alpine-keys  --allow-untrusted
```
这个命令是在Alpine Linux中添加一个软件包，并从指定的URL安装一个公钥。

apk add 是用来添加软件包的命令。
-X 参数后面跟的是公钥的URL，它告诉 apk 从哪里下载公钥。
-u 参数后面跟的是要添加的软件包的名称，这里是 alpine-keys。
--allow-untrusted 参数允许从不受信任的源安装软件包。

需要注意的是，使用 --allow-untrusted 参数可能会使你的系统面临安全风险，因为它允许从不受信任的源安装软件包。除非你完全信任这个源，否则在生产环境中应该避免使用这个参数。

先添加公钥，再用apk update就行了

这一步很重要，不然后期用apk del会系统崩的，一定要先安装以下系统组件，再去安装其它组件，不然系统会崩的，先运行apk add musl busybox busybox-binsh apk-tools，再apk add openjdk21-jre，不然后apk del openjdk21-jre会系统崩的，只能重新装系统了
```bash
apk add musl busybox busybox-binsh apk-tools
```

安装Java环境：
```bash
apk add openjdk21-jre
```
也可以选择完整的jdk
```bash
apk add openjdk21-jdk
```
java会安装在/usr/lib/jvm文件夹
![image](https://github.com/user-attachments/assets/792ad724-5a9f-4eaf-aa19-67d332f41edb)

此时输入指令查询Java版本号，确认是否安装Java21及以上
```bash
java -version
```

**Java环境到此安装完成**


3. 校验是否能正常运行
```bash
java -jar ***.jar -u username -p passwd -d
```
_结果图后续替换_

4. 编写run.sh脚本文件，并设置开机自启

创建run.sh:

```bash
vi /root/run.sh
```

输入：
```bash
   #!/bin/sh
java -jar ESurfingDialer-1.5.0-all.jar -u 你的账号 -p 你的密码 -d
 ```
保存退出后，设置权限：
```bash
chmod +x /root/run.sh
```

配置自启动脚本：
在/etc/rc.local中添加以下内容，以确保在系统启动时运行/root/run.sh：
```bash
vi /etc/rc.local
```

```bash
#!/bin/sh
/root/run.sh

exit 0
```

确保/etc/rc.local文件具有执行权限：

```bash
chmod +x /etc/rc.local
```

**做完上述操作，即可在开机时自动执行ESurfingDialer**


# 感谢Rsplwe大佬和250king耀的代码开源

# 代码引用
Rsplwe大佬源码
https://github.com/Rsplwe/ESurfingDialer?tab=readme-ov-file

openjdk安装教程
https://blog.csdn.net/qq_39858654/article/details/135777769

其他教程
https://www.bilibili.com/video/BV13142197va/?spm_id_from=333.337.top_right_bar_window_history.content.click&vd_source=264baa51a9392da602d95a80ad7aa577
