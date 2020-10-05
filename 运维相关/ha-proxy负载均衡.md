# HAProxy负载均衡

## 一、HAProxy介绍

​	HAProxy是一个使用C语言编写的自由及开放源代码软件[1]，其提供高可用性、负载均衡，以及基于TCP和HTTP的应用程序代理。特别适用于那些负载特大的web站点，这些站点通常又需要会话保持或七层处理。

nginx

* 使用花括号，层级化的配置文件结构
* 除了自带的map、if语句可以实现简单逻辑，原生支持js/perl脚本，非官方支持lua
* 除了做负载均衡还可以做静态web服务器，缓存服务器（Haproxy不行）
* 模块化，按需编译
* 开源版本只有基本功能（但是通常够用），更多的功能要折腾第三方模块，或者花钱买nginx plus

haproxy

* 定义和引用，命令式的配置结构
* 支持acl，但不支持其他脚本语言（评论说现在可以支持了）
* 做负载均衡性能比nginx好
* 有一个状态统计页面
* 官方支持会话保持、健康检查等（nginx开源版不带）

## 二、安装流程

参考链接：https://www.cnblogs.com/smlile-you-me/p/13618408.html

先安装gcc编译环境

```shell
# 安装gcc、c++编译器以及内核文件
yum -y install gcc gcc-c++ kernel-devel
```

安装lua

```shell
wget -c http://www.lua.org/ftp/lua-5.3.5.tar.gz
yum -y install readline-devel gcc gcc-c++ systemd-devel pcre-devel openeel openssl-devel
tar -zxvf lua-5.3.5.tar.gz
cd lua-5.3.5
make linux
make install INSTALL_TOP=/usr/local/lua
```

下载haproxy源码包进行编译安装

```shell
# 下载文件
wget https://mirrors.huaweicloud.com/haproxy/2.2/src/haproxy-2.2.2.tar.gz
# 解压文件
tar -zxvf haproxy-2.2.2.tar.gz
# 根据内核版本设置TARGET
# 这里需要使用uname -r查看系统版本centos6.X需要使用TARGET=linux26  centos7.x使用linux31
cd haproxy-2.2.2
make -j $(nproc) TARGET=linux-glibc USE_OPENSSL=1 USE_ZLIB=1 USE_LUA=1 LUA_LIB=/usr/local/lua/lib/ LUA_INC=/usr/local/lua/include/ USE_PCRE=1 USE_SYSTEMD=1   
# 编译安装
make install PREFIX=/usr/local/haproxy
# 创建配置文件目录
mkdir /usr/local/haproxy/conf

cp /usr/local/haproxy/sbin/haproxy /usr/sbin/
cp ./examples/haproxy.init /etc/init.d/haproxy
chmod 755 /etc/init.d/haproxy
# 创建配置文件到配置目录
cd /usr/local/haproxy/conf/
vim haproxy.cfg
# 启动haproxy
haproxy -f /usr/local/haproxy/conf/haproxy.cfg
```

## 三、配置修改