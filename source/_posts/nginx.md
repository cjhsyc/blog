---
title: nginx
date: 2022-03-30 22:13:33
categories: [nginx]
tags: 
- nginx
- CentOS
---

# 前言

已准备好如下软件：

- VMware
- Xshell和Xftp

# 安装CentOS7.4

## 下载

[CentOS7.4下载地址](https://vault.centos.org/7.4.1708/isos/x86_64/)

选择CentOS-7-x86_64-Minimal-1708.iso进行下载

使用VMware运行虚拟机

## 联网

修改网卡配置文件：

`vi /etc/sysconfig/network-scripts/ifcfg-ens33`

将`ONBOOT=no`改为`ONBOOT=yes`

保存退出后，执行：（重启网卡）

`systemctl restart network`

使用`ping`命令测试可以联网

重启网卡失败有可能是VMware软件的问题，可卸载重装

## 设置静态ip

使用Xshell连接虚拟机（Xshell终端的操作更方便）：

使用`ip addr`查看ens33的IP地址

打开Xshell新建连接：

- 名称随意、主机填写IP地址
- 填写用户名（root）和密码、完成连接

修改配置文件：

`vi /etc/sysconfig/network-scripts/ifcfg-ens33`

将`BOOTPROTO=dhcp`改为`BOOTPROTO=static`

添加如下配置ip地址、子网掩码、网关、DNS：（每人的ip地址不同）

```
IPADDR=192.168.181.128
NETMASK=255.255.255.0
GATEWAY=192.168.181.2
DNS1=8.8.8.8
```

查看网关是否正确：

- VMware左上角点击编辑
- 虚拟网络编辑器
- 更改设置
- 选择VMnet8
- 点击`NET设置`即可查看网关

重启网卡：`systemctl restart network`

`ping`命令测试联网成功（ping 8.8.8.8）

# Nginx安装和启动

在安装之前可将虚拟机克隆一份：

- 虚拟机关机
- 右键-管理-克隆

[nginx官网](http://nginx.org/)

下载地址：[ nginx-1.21.6](http://nginx.org/en/download.html)

点击`nginx-1.21.6`进行下载、完成后通过Xftp将压缩包传到虚拟机上

也可以直接在虚拟机里使用`wget`下载

[可参考文章](https://blog.csdn.net/weixin_45745641/article/details/119223601)

安装所需环境：

- 安装gcc：`yum install -y gcc`

- 安装PCRE pcre-devel：`yum install -y pcre pcre-devel`
- 安装zlib：`yum install -y zlib zlib-devel`

解压安装nginx：

- 解压：`tar zxvf nginx-1.21.6.tar.gz` 
- 进入 ：`cd nginx-1.21.6`
- 配置并指定安装目录：`./configure --prefix=/usr/local/nginx`
- 编译、安装：`make`；`make install`

启动nginx：

- 进入安装目录：`cd /usr/local/nginx/sbin/`
- 启动：`./nginx`
- 验证：浏览器访问虚拟机的ip地址（需关闭防火墙）

防火墙：

- 关闭：`systemctl stop firewalld.service`
- 禁止防火墙开机启动：`systemctl disable firewalld.service`

nginx服务脚本：

- 创建脚本文件：`vi /usr/lib/systemd/system/nginx.service`

- 文件内容：

  ```
  [Unit]
  Description=nginx - web server
  After=network.target remote-fs.target nss-lookup.target
  [Service]
  Type=forking
  PIDFile=/usr/local/nginx/logs/nginx.pid
  ExecStartPre=/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf
  ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
  ExecReload=/usr/local/nginx/sbin/nginx -s reload
  ExecStop=/usr/local/nginx/sbin/nginx -s stop
  ExecQuit=/usr/local/nginx/sbin/nginx -s quit
  PrivateTmp=true
  [Install]
  WantedBy=multi-user.target
  ```

- 保存退出

- 重新加载系统服务：`systemctl daemon-reload`

- 关闭nginx：`./nginx -s stop`

- 启动服务：`systemctl start nginx.service`

- 查看：`systemctl status nginx.service`，出现`active (running)`表示成功

- 开机启动： `systemctl enable nginx.service`

- 重启：`reboot`，等待一会后浏览器可访问虚拟机ip地址表示脚本成功执行

# nginx基本使用

在修改配置之前克隆一份虚拟机

## 目录结构

可使用Xftp查看/usr/local/nginx/

- conf：配置文件
- html：静态页面
- logs：日志
- sbin：nginx的主程序

"_temp"结尾的文件夹是nginx运行时才生成的

## 基本运行原理

nginx启动后有一个主进程（Master）和多个子进程（Worker）

Master校验配置文件，协调子进程

Worker处理和响应请求

## 配置文件（nginx.conf）

进入/usr/local/nginx/conf/

使用Xftp时，右键文件可以通过记事本进行编辑

编辑nginx.conf：（如下是开头的两行）

```
#user  nobody;#该符号表示注释
worker_processes  1;
```

先不看带注释的配置：（最小配置文件）

```
worker_processes  1; #子进程个数
events {
    worker_connections  1024; #单个子进程可接受的连接数
}
http {
    include       mime.types; #include：引入其他配置文件；mime.types：指定各种文件后缀的资源类型
    default_type  application/octet-stream; #默认类型
    
    sendfile        on; #使用linux的 sendfile(socket, file, len) 高效网络传输，也就是数据0拷贝。
    
    keepalive_timeout  65;
    
    server { #虚拟主机，可以有多个（vhost）
        listen       80; #监听的端口号
        server_name  localhost; #域名或主机名（可以写多个，用空格隔开）
        location / { #uri
            root   html; #根目录（html相当于/usr/local/nginx/html/）
            index  index.html index.htm; #默认页
        }
        error_page   500 502 503 504  /50x.html; #服务器错误时的页面
        location = /50x.html {
            root   html;
        }
    }
}
```

### 虚拟主机

配置多个server：

```
server { 
    listen       80; 
    server_name  www.my.com; #(需在阿里云购买域名)
    location / {
        root   /www/www; #在虚拟机上创建对应的目录和目录下的资源
        index  index.html index.htm; 
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }
}

server { 
    listen       80; 
    server_name  *.my.com; # *号表示通配符（匹配所有的三级域名），域名解析时先匹配前面的server，再匹配第二个
    location / {
        root   /www/video;
        index  index.html index.htm; 
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }
}
```

修改配置后重新加载nginx：`ststemctl reload nginx`

server_name:还可以使用正则表达式进行匹配

可以修改本机的hosts文件进行server_name的测试：C:\Windows\System32\drivers\etc\hosts（需管理员权限）

在最后添加：`192.168.181.128 x.com`,前者为虚拟机ip地址，后者为自定义域名

修改后可以在本机通过域名访问虚拟机ip地址

## 反向代理

反向代理服务器（比如nginx）位于用户和目标服务器（比如Tomcat）之间，用户无法直接访问Tomcat，而由nginx接收请求，然后转发给Tomcat，返回数据给nginx后，再返回给用户。

### proxy_pass

配置反向代理：

    server {
        listen       80;
        server_name  localhost;
        location / { #在这里配置proxy_pass，且不需要root和index
        	proxy_pass  http://www.qq.com; #指定服务器
            #root   html;
            #index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }

重新加载nginx：`systemctl reload nginx`

浏览器再次访问虚拟机ip会重定向到腾讯网（外网服务器）(301：重定向，地址栏改变)

测试代理内网服务器：

- 克隆一份虚拟机（刚安装配置好nginx的状态）

- 修改配置文件：`vi /etc/sysconfig/network-scripts/ifcfg-ens33`


- 修改`IPADDR=192.168.181.128`为`IPADDR=192.168.181.129`

- 重启网卡：`systemctl restart network`
- Xshell连接192.168.181.129
- 修改/usr/local/nginx/html/index.html
- Xshell连接192.168.181.128
- 修改配置：`proxy_pass http://192.168.181.129 ; #指定服务器`
- 重新加载nginx：`systemctl reload nginx`
- 浏览器访问192.168.181.128，显示192.168.181.129上的index.html（地址栏不变）

### 负载均衡

反向代理多个服务器，当nginx接收到请求时，通过轮询的方式将请求转发给服务器（轮流）

按照之前的流程再克隆一个虚拟机，ip设置为192.168.181.130并修改index.html

Xshell连接192.168.181.128，进行配置：

```
upstream httpds{ #upstream:对应多组服务器，httpds：自定名称
	server 192.168.181.129;
	server 192.168.181.130;
}

server {
    listen       80;
    server_name  localhost;
    location / {
    	proxy_pass  http://httpds; #httpds:对应upstream的名称
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }
}
```

upstream和server同级

重新加载nginx：`systemctl reload nginx`

浏览器多次访问192.168.181.128，轮流显示两个服务器中index.html的内容

#### 负载均衡策略

- 权重 weight

  ```
  upstream httpds{
  	server 192.168.181.129 weight=8; #添加weight：值越大，访问该服务器的概率越大
  	server 192.168.181.130 weight=2;
  }
  ```

- 下线 down

  ```
  upstream httpds{
  	server 192.168.181.129 weight=8 down; #down：该服务器不会被访问（出现故障时下线该服务器）
  	server 192.168.181.130 weight=2;
  }
  ```

- 备用 backup

  ```
  upstream httpds{
  	server 192.168.181.129 weight=8 down;
  	server 192.168.181.130 weight=2 backup; #backup：备用机，只有其他服务器都不可用时才会被使用
  }
  ```

都需要手动配置文件然后reload nginx

## 动静分离

将静态资源（比如图片、css、js）放在nginx上

在nginx配置中的server下配置多个location即可（也可使用正则）：

    location / {
    	proxy_pass  192.168.181.129;
    }
    
    # ~号表示开始正则匹配，*号表示不区分大小写
    location ~*/(img|css|js) { #匹配根目录下的三种文件夹（img|css|js），优先级比location /高
    	root html; #根目录：/usr/local/nginx/html/
    	index index.html index.htm;
    }

## URLRewrite

重写url，可以隐藏真实的url

```
location / {
	rewrite  ^/([0-9]+).html$  /index.html?pageNum=$1  break;
	proxy_pass  192.168.181.129;
}
```

rewrite  <正则>  <真实uri>  <标识>;

^:正则开始，$:正则结束

将匹配到的正则替换为真实uri（$1:表示正则表达式的第一个括号里匹配到的内容）

四种标识:

- last #本条规则匹配完成后，继续向下匹配新的location URI规则 
- break #本条规则匹配完成即终止，不再匹配后面的任何规则 
- redirect #返回302临时重定向，浏览器地址会显示跳转后的URL地址 
- permanent #返回301永久重定向，浏览器地址栏会显示跳转后的URL地址



[参考视频](https://www.bilibili.com/video/BV1yS4y1N76R?p=3&t=35.2)