# rpm学习

## 一、rpm基本概念

​	rpm(redhat package manager) 原本是 Red Hat Linux 发行版专门用来管理 Linux 各项套件的程序，由于它遵循 GPL 规则且功能强大方便，因而广受欢迎。逐渐受到其他发行版的采用。RPM 套件管理方式的出现，让 Linux 易于安装，升级，间接提升了 Linux 的适用度。

### 1.1 rpm包的命名规则

**以zsh-5.0.32-14.el7.x86_64.rpm为例**

| zsh    | -5       | .0       | .2     | -7    | x86         | 64           |
| ------ | -------- | -------- | ------ | ----- | ----------- | ------------ |
| 软件名 | 主版本号 | 次版本号 | 修订号 | RHEL7 | CPU架构平台 | 支持系统位数 |

**el7：**Enterprise Linux 7   RHEL7 或 CentOS7

**-14：**第14次发行

## 1.2 常用查找rpm包的网站

http://www.rpmfind.net/linux/RPM/

http://rpm.pbone.net/

## 二、常见包管理方式

### 2.1 rpm软件包管理

**rpm参数列表**

```
rpm [-acdhilqRsv][-b<完成阶段><套间档>+][-e<套件挡>][-f<文件>+][-i<套件档>][-p<套件档>＋][-U<套件档>][-vv][--addsign<套件档>+][--allfiles][--allmatches][--badreloc][--buildroot<根目录>][--changelog][--checksig<套件档>+][--clean][--dbpath<数据库目录>][--dump][--excludedocs][--excludepath<排除目录>][--force][--ftpproxy<主机名称或IP地址>][--ftpport<通信端口>][--help][--httpproxy<主机名称或IP地址>][--httpport<通信端口>][--ignorearch][--ignoreos][--ignoresize][--includedocs][--initdb][justdb][--nobulid][--nodeps][--nofiles][--nogpg][--nomd5][--nopgp][--noorder][--noscripts][--notriggers][--oldpackage][--percent][--pipe<执行指令>][--prefix<目的目录>][--provides][--queryformat<档头格式>][--querytags][--rcfile<配置档>][--rebulid<套件档>][--rebuliddb][--recompile<套件档>][--relocate<原目录>=<新目录>][--replacefiles][--replacepkgs][--requires][--resign<套件档>+][--rmsource][--rmsource<文件>][--root<根目录>][--scripts][--setperms][--setugids][--short-circuit][--sign][--target=<安装平台>+][--test][--timecheck<检查秒数>][--triggeredby<套件档>][--triggers][--verify][--version][--whatprovides<功能特性>][--whatrequires<功能特性>]
```

**安装rpm包**

```shell
rpm -ivh 软件包路径
#一些软件包是存在依赖关系的，不考虑依赖强制安装如下
rpm -ivh 软件包路径 --nodeps #一般不建议这么操作
```

**rpm查询**

```shell
# 查询系统中是否已经已经安装了某个包
rpm -q 软件包名
# 查询某服务所需安装的包哪些安装了哪些没有安装，可以进行过滤
rpm -qa | grep httpd
# 查询某个包安装后产生了哪些目录或文件
rpm -ql 软件包名| more
# 查看某个命令是由那个包安装的
rpm -qf `which zsh`
# 在软件没有安装之前查询会产生哪些对应文件夹或文件
rpm -qpl 软件包的绝对路径
```

**软件包升级**

```shell
# 升级软件包
rpm -U 软件包名
```

**卸载软件包**

```shell
# 卸载软件包，可以只写软件包名，不需要写版本号
rpm -e 软件包名
```

**导入密钥减少warning**

```shell
# 不同的环境可能不一样
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

### 2.2 yum管理软件包

**yum解决依赖关系，自动下载软件包基于C/S架构**

Yum（全称为 Yellow dog Updater, Modified）是一个在Fedora和RedHat以及CentOS中的Shell前端软件包管理器。基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，无须繁琐地一次次下载、安装。 

**yum源介绍**

yum源可以分为本地yum源和网络yum源

**server：**可以直接使用光盘所在的根目录（/media 或  /mnt）通过file进行读取，或部署在远程服务器通过http/ftp访问远程yum源

**client：**配置yum源，可以本地或远程

```shell
# 客户端yum源配置文件示例
[rhel7-yum]					#yum源名称，唯一的，用来区分不同的yum源名
name=rhel7-source			#对yum源名描述信息
baseurl=file:///mnt			#yum的路径（repodata目录所在的目录）
enabled=1					#为1，表示启用yum源
gpgcheck=0					#为1，表示公钥检验rpm的正确性
```

使用前请注意清空缓存，再生成缓存

```shell
# 情况原先的yum缓存
yum clean all
# 生成新的yum源的缓存
yum list
```

**yum安装软件**

```shell
# 安装某个软件包
yum -y install 软件包名
# 安装一组软件包
yum groupinstall "包组名称"
```

**yum查询软件包**

```shell
# 列出已经安装的软件包名 例如：ab*
yum list 软件包名
# 查询一个软件包
yum search 软件包名
```

**yum删除软件包**

```shell
# 卸载安装的软件包
yum remove 软件包名
```

### 2.3 源码包管理

前提：系统安装开发工具，开发库

步骤

1. 获取源码包
2. 解压
3. 配置，检查安装环境
4. 编译
5. 安装

