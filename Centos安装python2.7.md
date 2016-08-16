#Centos python2.6 升级为 python2.7 

##升级python

* *下载 python-2.7.12:*  wget https://www.python.org/ftp/python/2.7.12/Python-2.7.12.tar.xz
* *安装xz:*  yum -y install xz
* *解压:* xz -d Python-2.7.12.tar.xz
* tar -xvf Python-2.7.12.tar
* *安装:* ./configure
* make all
* make install
* make clean 
* make distclean 
* *查看python版本信息:* /usr/local/bin/python2.7 -V\
* *建立软连接，使用系统默认的python 指向 python2.7:* mv /user/bin/python /usr/bin/python2.6.6 
* ln -s /usr/local/bin/python2.7 /usr/bin/python
* *重新检验python 版本:* python -V
* *解决系统python软连接指向python2.7版本之后，yum 不能正常工作:* vim /usr/bin/yum ,将头部文件的 #!/usr/bin/python  #!/usr/bin/python2.6.6

##解决编译安装python 之前没有编译安装类似于openssl 这样的ssl库
* yum install openssl-devel 
* 重新编译python（执行上述步骤,从configure 开始)

##安装pip

* yum install python-pip

###解决 No module named pkg_resources 问题 
####原因

```  
[root@iZ28jtz3hm3Z bin]# pip install
Traceback (most recent call last):
  File "/usr/bin/pip", line 5, in <module>
    from pkg_resources import load_entry_point
ImportError: No module named pkg_resources
```  
发现这种问题的原因是：虽然升级了python 到2.7 版本，但是pip 版本仍然是原来的

####解决  
* *卸载pip:* yum erase python-pip
* *安装easy_install:* wget https://pypi.python.org/packages/f3/2b/e97c01487b7636ba3fcff5e73db995c66c524d54d0907d238311524f67c8/distribute-0.6.49.tar.gz#md5=89e68df89faf1966bcbd99a0033fbf8e
* tar -xzv distribute-0.6.49.tar.gz
* cd distribute-0.6.49.tar.gz
* python distribute_setup.py

**执行上述步骤，easy_install 已经被安装** 

* easy_install pip
* *检验pip是否安装:* /usr/local/bin/pip -V
* 创建软连接  ln -s /usr/local/bin/pip /usr/bin/pip

###安装virtualenv

* pip install virtualenv