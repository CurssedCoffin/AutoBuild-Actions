# AutoBuild-Actions
项目原作者：[Hyy2001X](https://github.com/Hyy2001X)

Gitub Actions 自动编译 OpenWRT

支持的设备：`x86`、`d-team_newifi-d2`、`phicomm_k2p`

**已安装的服务：**

    多拨、UPNP、mwan3、Aria2、动态dns、硬盘休眠、TTYD终端、KMS服务器、FTP服务器、FRP内网穿透、USB网络共享

    WoL网络唤醒、Socat端口转发、samba网络共享、Serverchan、广告屏蔽大师 Plus +、网易云解锁 GoLang版本

    SSR Plus + （含SSR server）、Openclash （已安装依赖kmod-tun）、自动更新（Hyy2001X）

**路由器后台：192.168.1.1    登录密码：password**

本地环境搭建:
-
1. 首先装好 Ubuntu 64bit，推荐  Ubuntu  18 LTS x64

2. 命令行输入 `sudo apt-get update` ，然后输入
`
sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch python3 python2.7 unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint device-tree-compiler g++-multilib antlr3 gperf wget curl swig rsync
`

3. 使用 `git clone https://github.com/coolsnowwolf/lede` 命令下载好源代码，然后 `cd lede` 进入目录

4. ```
   cd package
   git clone https://github.com/kenzok8/openwrt-packages.git
   git clone https://github.com/kenzok8/small.git
   cd && cd lede
   ./scripts/feeds update -a
   ./scripts/feeds install -a
   make menuconfig
   ```

5. `./scripts/diffconfig.sh > defconfig` 生成config差异文件，并将其改名为.config上传到仓库

Github环境配置:
-
1. 进入仓库，点右边Settings，然后点Secrets，New Secrets，Name填写RELEASE_TOKEN，Value填你刚获取到的token, token在 https://github.com/settings/tokens/new 生成。

2. 在Customize/AutoUpdate.sh中第 6-11 行改为自己的信息。在Scrips/diy-script.sh中第 7 行改作者名，会在固件中显示。

3. 在Scrips/diy-script.sh中 Diy-Part1() 函数中，参照现有 ExtraPackages 语法添加第三方包，并在config文件中添加相应的包，即可将第三方包编译进固件。

通过指令更新固件:
-
1. 打开路由器后管理界面

2. 打开`系统`-`TTYD终端`

3. 在命令行输入 
  `curl -s https://raw.githubusercontent.com/CurssedCoffin/AutoBuild-Actions/master/Customize/AutoUpdate.sh | sh`

一些边角料:
-
1. 有时会遇到 `opkg install` 提示Package size dismatch，此时可以通过添加 `--force-checksum` 参数强制不验证MD5值进行安装。

2. #### 新3通过外接usb扩展系统空间。 
   修改之前请在 系统 - 挂载点 - 自动挂载 处取消勾选，已挂载的请卸载设备。

   使用 `block info` 来确定你的U盘位置，通常为 /dev/sdax ，其中x可能为空，也可能为数字，以下以 /dev/sda1 为例，若有出入则自行修改相应字段。
   
   使用 `mkfs.ext4 /dev/sda1` 命令格式化U盘为ext4格式。若提示找不到命令，则尝试安装e2fsprogs。
   ```
   mount /dev/sda1 /mnt
   mkdir /tmp/cproot
   mount --bind / /tmp/cproot
   tar -C /tmp/cproot -cvf - . | tar -C /mnt -xvf -
   sync
   umount /tmp/cproot
   ```
   如果U盘有两个或以上的分区，还可以设置好交换空间，并注意sda的序号
   ```
   mkswap /dev/sda2
   swapon /dev/sda2
   ```
   最后把当前的挂载状态写到fstab文件
   ```
   block detect > /etc/config/fstab
   ```

   拿出winscp找到/etc/config/fstab，用记事本开始编辑
   
   需要修改所有的 ***option enabled*** 后0为1，即开机自动挂载所有分区，再找到 ***option target '/mnt'*** 后改成 '/' ，即将u盘挂载到根目录
   
   重启即可看到可用空间增大了，enjoy ~
   
3. 单独编译的包在 ExtraPackages 中，可自行提取，安装。目前有：Netdata，Kodexplorer

鸣谢:
-
1. [Lean](https://github.com/coolsnowwolf/lede)：项目的基础
2. [Hyy2001X](https://github.com/Hyy2001X)：这个项目是他的，并且给我提供了很多帮助，真的很多
3. [P3TERX](https://github.com/P3TERX)：[云编译教程](https://p3terx.com/archives/build-openwrt-with-github-actions.html) 十分详细易懂
