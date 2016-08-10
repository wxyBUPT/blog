#Centos6.7 下openvpn client 的安装与配置

因为央广爬虫项目的服务器需要使用到openvpn，央广内部人员提供的资料又是win环境下的openvpn，故简单的配置了一下环境。

```  
yum install openvpn 
```  
报错如下 

```  
[root@localhost crawler]# yum install openvpn
Failed to set locale, defaulting to C
Loaded plugins: fastestmirror, refresh-packagekit, security
Setting up Install Process
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
No package openvpn available.
Error: Nothing to do
```  


##下载源码，编译安装Openvpn（不管用）

###安装需求库

```  
yum install gcc make rpm-build autoconf.noarch zlib-devel pam-devel openssl-devel -y
```  

```  
yum install -y openssl openssl-devel lzo lzo-devel pam pam-devel automake pkgconfig
```  

###编译安装

```  
wget -c https://swupdate.openvpn.org/community/releases/openvpn-2.3.11.tar.gz
tar zxvf openvpn-2.3.11.tar.gz
cd openvpn-2.3.11
./configure
 make 
 make install 
 mkdir -p /etc/openvpn
 cp -Rf 
```  

##添加额外的repositories，安装openvpn（简单有效）

```  
yum install epel-release
yum install openvpn 
```  

##配置（适用于已经从服务器端获得ca证书和客户端证书）

创建openvpn的配置文件夹

```  
sudo mkdir /etc/openvpn
```  

将服务器端生成的key（类似于ca.crt,client.crt,client..key,ta.key）下载到该目录

添加 client.ovpn 

```  
vim client.ovpn
```  

添加如下内容

``` 
client
dev tun
proto udp
remote xxx.xxx.xxx.xxx 1194
ca ca.crt
cert xxx.crt
key xxx.key
; tls-auth ta.key 0 (这句要注释掉)
comp-lzo
user nobody （仅供非 windows 客户端配置，windows 请用“;”注释掉）
group nobody   （仅供非 windows 客户端配置，windows 请用“;”注释掉）
persist-key
persist-tun
```  

daemon 启动openvpn，建议daemon 启动之前先调试

```  
openvpn --daemon --config client.ovpn
```  

完


