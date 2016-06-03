#Tair 安装笔记

##多次尝试在Ubuntu上安装，并未安装成功

##成功安装在Centos 上（阿里云）

**具体安装过程如下(使用root账户)** 

* 安装依赖软件包
 * automake yum install automak.noarch
 * 安装 libtool yum install libtool.x86_64
 * 用于数据压缩解压缩 libz-devel  yum install zlib-devel.x86_64
 * uuid-dev   yum install e2fsgrogs-devel.x86_64
* 安装tb-common-util 
 * 设置 TBLIB_ROOT 环境变量，在 ~/.bashrc 中加入 export TBLIB_ROOT=/root/.tblib_root，然后执行 source ~/.bashrc
 * 下载源码  svn co -r 18 http://code.taobao.org/svn/tb-common-utils/trunk tb-common-utils
 * 编译安装  cd tb-common-utils   sh build.sh
* 安装Tair
 * svn checkout http://code.taobao.org/svn/tair/trunk/ tair
 * 进入 tair 目录
 * ./bootstrap.sh
 * ./configure --with-release=yes
 * make
 * make install 

 