---
title: 使用frp内网穿透
tags:
  - frp
  - 内网穿透
originContent: >-
  > frp地址：https://github.com/fatedier/frp


  # 本文目的


  1. 将一台个人电脑配置成一台可外网访问的服务器

  2. 用自己的个人电脑做各种和服务器一样事


  # 你需要准备

  1. 一台能外网访问的服务器

  2. 一台能长期开机当服务器跑的个人电脑


  # 术语及机器配置

  内网机：deepin最新版


  服务器：阿里云ecs最低配


  服务器IP：x.x.x.x


  port=7000：服务器端绑定此端口，提供给内网机使用


  port=8080：所有web访问


  port=6000：ssh访问需要指定的端口


  port=7500：访问web仪表盘需要带的端口


  port=5000：访问mongodb端口

  # 内网机需要做的事

  1. 安装ssh

  2. 开启22端口

  3. 配置frp

  4. 后台运行frp


  ## 安装ssh服务

  >
  关于deepin的shh服务，可以看官方教程：[https://wiki.deepin.org/index.php?title=SSH%E6%9C%8D%E5%8A%A1](https://wiki.deepin.org/index.php?title=SSH%E6%9C%8D%E5%8A%A1)


  总之，运行1个命令即可安装


  ```

  sudo apt-get install openssh-server

  ```


  查看是否运行

  ```

  ps -e |grep ssh

  ```


  如果返回有sshd，就是成功开启


  ## 开放22端口

  ```

  vim /etc/ssh/ssh_config

  ```

  找到`Port 22`

  删除前面的`#`，保存


  ```

  /etc/init.d/ssh restart 

  ```


  # 配置frp服务（主角）

  ## 下载frp

  1.
  在release下载最新版[https://github.com/fatedier/frp/releases](https://github.com/fatedier/frp/releases)

  2. 解压


  ## 文件说明


  ```

  frps.ini: 服务端配置文件

  frps: 服务端软件

  frpc.ini: 客户端配置文件

  frpc: 客户端软件

  ```

  ## 配置说明

  ```

  [common]        //必须设置

  bind_port       //是自己设定的frp服务端端口

  vhost_http_port //是自己设定的http访问端口

  [ssh]           //ssh反向代理(不是必须设置)

  listen_port     //是自己设定的ssh访问端口

  auth_token      //用于身份认证（以下皆是）。

  [web]           //http反向代理[]里的内容可以自己设定，但是客户端和服务端必须要对应(如[aaa],[bbb])；

  type            //为服务类型，可以设为http,https

  custom_domains  //为要映射的域名，记得域名的A记录要解析到外网主机的IP。

  [web2]          //同上(可设置多个)

  ```

  以上配置信息和文件说明主要参考：https://www.jianshu.com/p/93bf511ea72e，感谢

  ### 服务器端配置

  1. 上传`frps`、`frps.ini`到服务器，修改`frps.ini`

  ```

  # frps.ini

  [common]

  bind_port = 7000        //提供给内网机的端口，端口不能被占用

  vhost_http_port = 8080  //内网机http访问的端口，端口不能被占用

  ```

  2. 运行frps

  ```

  ./frps -c ./frps.ini

  ```


  **注意：所使用的端口，都要在阿里云控制面板中添加安全配置的规则**

  ### 外网机配置

  #### 1、添加ssh

  1. 修改`frpc.ini`

  ```

  # frpc.ini

  [common]

  server_addr = x.x.x.x

  server_port = 7000


  [ssh]

  type = tcp

  local_ip = 127.0.0.1

  local_port = 22

  remote_port = 6000

  ```

  **common模块：**


  `server_addr` 绑定服务器的ip地址


  `server_port` 外网机绑定服务器设置的端口


  **ssh模块：**


  规定了ssh的配置


  `remote_port`为ssh时指定的端口


  **运行**

  ```

  ./frpc -c ./frpc.ini

  ```

  此时，在其他linux端直接使用命令连接ssh，假设名为`test`

  ```

  ssh -oPort=6000 test@x.x.x.x

  ```

  或者用xshell等软件连接也可以

  #### 2、添加web服务

  ```

  # frpc.ini

  ...

  [web1]

  type = http

  local_port = 80

  custom_domains = www.aa.com

  ```

  `web1` 每个站点服务就写一个web服务，一个站点多个域名可以使用


  `custom_domains` 绑定站点的域名，例如，本机nginx上站点域名为www.aa.com，那此处就必须是www.aa.com


  **运行**

  ```

  ./frpc -c ./frpc.ini

  ```

  此时，将www.aa.com解析到服务器ip，浏览器访问`http://www.aa.com:8080`即可访问站点


  # 后台运行

  使用`nohup`运行上面的命令

  ```

  nohup ./frpc -c ./frpc.ini &

  ```

  **注意**


  `&`是必须的


  ```

  ps -ef |grep frp

  ```

  如果启动成功了，就会看到有frpc的进程了


  这时候，电脑别动，去别的机子访问ssh或者内网机设置的域名，看看内网机是否成为一台可以外网访问的服务器吧。
categories:
  - 网络应用
toc: true
date: 2018-11-13 15:36:15
---

> frp地址：https://github.com/fatedier/frp

# 本文目的

1. 将一台个人电脑配置成一台可外网访问的服务器
2. 用自己的个人电脑做各种和服务器一样事

# 你需要准备
1. 一台能外网访问的服务器
2. 一台能长期开机当服务器跑的个人电脑

<!-- more -->

# 术语及机器配置
内网机：deepin最新版

服务器：阿里云ecs最低配

服务器IP：x.x.x.x

port=7000：服务器端绑定此端口，提供给内网机使用

port=8080：所有web访问

port=6000：ssh访问需要指定的端口

port=7500：访问web仪表盘需要带的端口

port=5000：访问mongodb端口
# 内网机需要做的事
1. 安装ssh
2. 开启22端口
3. 配置frp
4. 后台运行frp

## 安装ssh服务
> 关于deepin的shh服务，可以看官方教程：[https://wiki.deepin.org/index.php?title=SSH%E6%9C%8D%E5%8A%A1](https://wiki.deepin.org/index.php?title=SSH%E6%9C%8D%E5%8A%A1)

总之，运行1个命令即可安装

```
sudo apt-get install openssh-server
```

查看是否运行
```
ps -e |grep ssh
```

如果返回有sshd，就是成功开启

## 开放22端口
```
vim /etc/ssh/ssh_config
```
找到`Port 22`
删除前面的`#`，保存

```
/etc/init.d/ssh restart 
```

# 配置frp服务（主角）
## 下载frp
1. 在release下载最新版[https://github.com/fatedier/frp/releases](https://github.com/fatedier/frp/releases)
2. 解压

## 文件说明

```
frps.ini: 服务端配置文件
frps: 服务端软件
frpc.ini: 客户端配置文件
frpc: 客户端软件
```
## 配置说明
```
[common]        //必须设置
bind_port       //是自己设定的frp服务端端口
vhost_http_port //是自己设定的http访问端口
[ssh]           //ssh反向代理(不是必须设置)
listen_port     //是自己设定的ssh访问端口
auth_token      //用于身份认证（以下皆是）。
[web]           //http反向代理[]里的内容可以自己设定，但是客户端和服务端必须要对应(如[aaa],[bbb])；
type            //为服务类型，可以设为http,https
custom_domains  //为要映射的域名，记得域名的A记录要解析到外网主机的IP。
[web2]          //同上(可设置多个)
```
以上配置信息和文件说明主要参考：https://www.jianshu.com/p/93bf511ea72e，感谢
### 服务器端配置
1. 上传`frps`、`frps.ini`到服务器，修改`frps.ini`
```
# frps.ini
[common]
bind_port = 7000        //提供给内网机的端口，端口不能被占用
vhost_http_port = 8080  //内网机http访问的端口，端口不能被占用
```
2. 运行frps
```
./frps -c ./frps.ini
```

**注意：所使用的端口，都要在阿里云控制面板中添加安全配置的规则**
### 外网机配置
#### 1、添加ssh
1. 修改`frpc.ini`
```
# frpc.ini
[common]
server_addr = x.x.x.x
server_port = 7000

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 6000
```
**common模块：**

`server_addr` 绑定服务器的ip地址

`server_port` 外网机绑定服务器设置的端口

**ssh模块：**

规定了ssh的配置

`remote_port`为ssh时指定的端口

**运行**
```
./frpc -c ./frpc.ini
```
此时，在其他linux端直接使用命令连接ssh，假设名为`test`
```
ssh -oPort=6000 test@x.x.x.x
```
或者用xshell等软件连接也可以
#### 2、添加web服务
```
# frpc.ini
...
[web1]
type = http
local_port = 80
custom_domains = www.aa.com
```
`web1` 每个站点服务就写一个web服务，一个站点多个域名可以使用

`custom_domains` 绑定站点的域名，例如，本机nginx上站点域名为www.aa.com，那此处就必须是www.aa.com

**运行**
```
./frpc -c ./frpc.ini
```
此时，将www.aa.com解析到服务器ip，浏览器访问`http://www.aa.com:8080`即可访问站点

# 后台运行
使用`nohup`运行上面的命令
```
nohup ./frpc -c ./frpc.ini &
```
**注意**

`&`是必须的

```
ps -ef |grep frp
```
如果启动成功了，就会看到有frpc的进程了

这时候，电脑别动，去别的机子访问ssh或者内网机设置的域名，看看内网机是否成为一台可以外网访问的服务器吧。