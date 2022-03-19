# NGINX 离线安装

> centOS 7 离线安装 nginx 1.21.6

> **准备环境**
>
> 1、离线 centOS 7
>
> 2、nginx 安装文件 （nginx-***.tar.gz 已放到下面的链接中，请自行下载或自己去官网下载最新版）
>
> 3、nginx 依赖包 （目前已放到 lib 目录中，可自行下载）

目前所有安装所需要的包和依赖都可去以下地址自行根据所对应软件进行下载

[https://gitee.com/livekeys/linux-offline-installation-software.git](https://gitee.com/livekeys/linux-offline-installation-software.git)



## 一、安装依赖

1、进入到依赖库 lib 中，执行以下命令进行依赖安装

```bash
yum -y localinstall *.rpm
```

2、安装 pcre

```bash
tar -xzvf pcre-8.45.tar.gz
cd pcre-8.45
./configure && make && make install
```

3、安装 openssl 

[centOS 7 离线安装升级openssl 3.0.2](https://blog.csdn.net/livekeys/article/details/123605771)

**注**：可查看另外一片文章进行安装，提供的 lib 皆一致，安装一遍即可

4、安装 zlib

```bash
tar -xzvf zlib-1.2.11.tar.gz
cd zlib-1.2.11
./configure && make && make install
```



## 二、安装 nginx

1、解压 nginx 文件

```bash
tar -xzvf nginx-1.21.6.tar.gz
```

2、编译和安装

```bash
cd nginx-1.21.6
./configure
make && make install
```

**注**：上面 ./configure ，默认安装位置为 /usr/local/nginx ，也可以加参数指定位置，例如 `./configure --prefix=/usr/local/nginx`。也可指定加入相应的模块，例如 `./configure --prefix=/usr/local/nginx --with-http_ssl_module --with-http_sub_module`。

3、测试是否安装成功

```bash
启动：/usr/local/nginx/sbin/nginx 
```

如果出现以下信息，则代表安装成功

```bash
[root@localhost nginx-1.21.6]# ps -ef | grep nginx
root      47448      1  0 12:23 ?        00:00:00 nginx: master process /usr/local/nginx/sbin/nginx
nobody    47449  47448  0 12:23 ?        00:00:00 nginx: worker process
root      47451   1486  0 12:23 pts/0    00:00:00 grep --color=auto nginx
[root@localhost nginx-1.21.6]#
```

如果 80 端口策略是开放的，可以用浏览器访问 http://xxx.xxx.xxx.xxx，查看是否能正常访问。



## 三、配置系统环境变量

```bash
[root@localhost nginx-1.21.6]# echo "# nginx path" >> /etc/profile
[root@localhost nginx-1.21.6]# echo "export NGINX_HOME=/usr/local/nginx" >> /etc/profile
[root@localhost nginx-1.21.6]# echo "export PATH=$NGINX_HOME/sbin:$PATH" >> /etc/profile
[root@localhost nginx-1.21.6]# source /etc/profile
```



接下来，我们就可以直接在全局对 nginx 进行操作了。

```bash
启动：nginx
停止：nginx -s stop
重载：nginx -s reload
```



> 如有错误的地方，欢迎留言指正，谢谢！

