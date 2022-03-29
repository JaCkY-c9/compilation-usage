# 首先装好 Ubuntu 64bit，推荐 Ubuntu 20.04 LTS x64 
# 命令行输入 
  sudo apt-get update 
  然后输入 sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev   patch python3 python2.7 unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib     p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool   autopoint device-tree-compiler g++-multilib antlr3 gperf wget curl swig rsync

# Download and update the sources
  git clone https://github.com/openwrt/openwrt.git openwrt
  
  cd openwrt
  
  git pull
 
# Select a specific code revision
  git branch -a
  git tag
  git checkout v19.07.8

# 增加配置
  #echo 'src-git passwall https://github.com/xiaorouji/openwrt-passwall' >>feeds.conf.default（编译不成） 
  echo 'src-git chajian https://github.com/jack987-cloud/packages-diy.git' >>feeds.conf.default
  echo 'src-git openclash https://github.com/vernesong/OpenClash.git' >>feeds.conf.default

# 添加diskman
  mkdir -p package/luci-app-diskman && \
  wget https://raw.githubusercontent.com/lisaac/luci-app-diskman/master/applications/luci-app-diskman/Makefile -O     package/luci-app-diskman/Makefile
  mkdir -p package/parted && \
  wget https://raw.githubusercontent.com/lisaac/luci-app-diskman/master/Parted.Makefile -O package/parted/Makefile 

# 修改dockerman 官源有dockerman 增加dockerman2


https://github.com/lisaac/luci-app-dockerman.git ？？？？




#单独添加软件包（可选项）
如果需要单独添加软件，请拉取源码到 pack­age 目录之下。示例如下：

# 添加 OpenAppFilter 应用过滤插件
git clone https://github.com/destan19/OpenAppFilter package/OpenAppFilter

# 设置默认主题（可选项）
注：此操作请在编译前执行，设置 ar­gon 为默认主题，以此为例举一反三

# 删除自定义源默认的 argon 主题
rm -rf package/lean/luci-theme-argon

# 拉取 argon 原作者的源码
git clone -b 18.06 https://github.com/jerrykuku/luci-theme-argon.git package/lean/luci-theme-argon

# 替换默认主题为 luci-theme-argon
sed -i 's/luci-theme-bootstrap/luci-theme-argon/' feeds/luci/collections/luci/Makefile








# Update the feeds
./scripts/feeds update -a
./scripts/feeds install -a
 
# 下载官方config配置
wget https://downloads.openwrt.org/releases/21.02.0/targets/x86/64/config.buildinfo -O .config

# Configure the firmware image and the kernel
make menuconfig
make kernel_menuconfig
 
# Build the firmware image
make -j $(nproc) defconfig download clean world

# 二次编译：

cd lede
git pull
./scripts/feeds update -a && ./scripts/feeds install -a
make defconfig
make -j8 download
make -j$(($(nproc) + 1)) V=s
如果需要重新配置：
rm -rf ./tmp && rm -rf .config
make menuconfig
make -j$(($(nproc) + 1)) V=s
编译完成后输出路径：bin/targets
