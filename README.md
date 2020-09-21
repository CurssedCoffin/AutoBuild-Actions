# AutoBuild-Actions
Actions for Building OpenWRT

Supported Devices: `d-team_newifi-d2`

本地环境搭建:
-
1. 首先装好 Ubuntu 64bit，推荐  Ubuntu  18 LTS x64

2. 命令行输入 `sudo apt-get update` ，然后输入
`
sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch python3 python2.7 unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint device-tree-compiler g++-multilib antlr3 gperf wget curl swig rsync
`

3. 使用 `git clone https://github.com/coolsnowwolf/lede` 命令下载好源代码，然后 `cd lede` 进入目录

4. ```bash
   ./scripts/feeds update -a
   ./scripts/feeds install -a
   make menuconfig
   ```

5. `./scripts/diffconfig.sh > defconfig` 生成config差异文件，并将其改名为.config上传到仓库

Github环境配置:
-
1. 进入仓库，点右边Settings，然后点Secrets，New Secrets，Name填写RELEASE_TOKEN，Value填你刚获取到的token, token在 https://github.com/settings/tokens/new 生成。

2. 在Customize/AutoUpdate.sh中第 6-10 行改为自己的信息。在Scrips/diy-script.sh中第 7 行改作者名，会在固件中显示。

3. 在Scrips/diy-script.sh中 Diy-Part1() 函数中，参照现有 ExtraPackages 语法添加第三方包，并在config文件中添加相应的包，即可将第三方包编译进固件。

一些边角料:
-
   有时会遇到 `opkg install` 提示Package size dismatch，此时可以通过添加 `--force-checksum` 参数强制不验证MD5值进行安装。
