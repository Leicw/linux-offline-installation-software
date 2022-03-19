# openssl 离线安装升级

> centOS 7 离线安装升级 openssl 3.0.2 版本

> **准备环境**
>
> 1、离线 centOS 7
>
> 2、openssl 安装文件 （openssl-***.tar.gz，已放到文件里面，也可自行下载最新版）
>
> 3、openssl 依赖包 （目前已放到 lib 目录中，可自行下载）

目前所有安装所需要的包和依赖都可去以下地址自行根据所对应软件进行下载

[https://gitee.com/livekeys/linux-offline-installation-software.git](https://gitee.com/livekeys/linux-offline-installation-software.git)



## 一、安装依赖

进入到依赖库 lib 中，执行以下命令进行依赖安装

```bash
yum -y localinstall *.rpm
```

**注：**这里的依赖有很多，有些也可以不安装，如果没有特殊需求，可以全部进行安装。



## 二、安装 openssl 

1、解压 openssl 文件

```bash
tar -xzvf openssl-3.0.2.tar.gz
```

2、编译和安装

```bas
进入到目录
cd openssl-3.0.2
./config --prefix=/usr/local/openssl
make && make install
```

3、备份旧的 openssl 文件

```bash
mv /usr/bin/openssl /usr/bin/openssl.bak
```

4、对新安装的 openssl 创建软链接

```bash
ln -sf /usr/local/openssl/bin/openssl /usr/bin/openssl
```

5、将 openssl 的 lib 库写进配置中，并使配置生效

```bash
echo "/usr/local/openssl/lib64" >> /etc/ld.so.conf
ldconfig -v 
```

6、检查安装是否成功

```bash
openssl version
```

> 如果出现以下信息，则代表安装成功

```bash
[root@localhost openssl]# openssl version
OpenSSL 3.0.2 15 Mar 2022 (Library: OpenSSL 3.0.2 15 Mar 2022)
```



> 如有错误的地方，欢迎留言指正，谢谢！

