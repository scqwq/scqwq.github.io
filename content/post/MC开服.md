---
title: 'MC服务器'
draft: false
categories: ["专业课"]
cover: "images/covers/5.jpg"
date: 2026-07-11T21:00:00+08:00
lastmod: 2026-07-11T21:00:00+08:00
---



推荐阅读/参考：https://www.zouht.com/2954.html

以下是MC购买服务器/开服的具体流程，可能会有没注意到的bug，可以自行AI搜索或查找其他资料

### 一、先购买服务器

什么云自己看着办，记得要买弹性公网ip，一般推荐ubuntu操作系统

#### 安全组：

该地址可以让人访问的端口，为了方便和联机，把以下端口放开：

MCsmanager：23333、24444
MC客户端登陆：25565

#### 本地ps/cmd登录远程服务器

创建服务器选择密码登录，则

在本地的powershell中，可以使用

```bash
ssh root@xxx.xxx.xxx.xxx
```

这个root是具体看你服务器的用户名，你的用户名是ubuntu那就是换上，具体出错的情况问ai

xxx是你的地址，之后输入密码就连接成功了

#### 可能会出现的

```bash
@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
```

出现这种情况，一般代表**你本地电脑记录的服务器指纹和现在的不一致了**

这通常发生在服务器被重装系统、更换密钥或IP被复用后。SSH 出于安全考虑会拒绝连接，以防中间人攻击。这不是密码问题，而是本地缓存需要清理。

解决：

```bash
ssh-keygen -R xxx.xxx.xxx.xxx
```

就可以清除指纹记录了

### 二，下载必要的东西

#### MC服务端

本机准备：要开服的服务端

一般原版可以在各个网站中下载到，比如：https://mcversions.net/

而对于整合包，大部分也应该提供相应的服务端，并且里面有适配linux的启动命令的，如果不适配可以让ai改一改（我不清楚）

#### 服务器环境

##### java下载

服务器登录后，下载java，以下是在ubuntu中下载java的命令

```bash
apt update -y 或者 sudo yum update -y # 更新软件包列表

# 有sudo 
#java 8
sudo yum install java-1.8.0-openjdk-devel -y 
#java 11
sudo yum install java-11-openjdk-devel -y
#java 17
sudo yum install java-17-openjdk-devel -y
#java 21
sudo apt install openjdk-21-jdk -y

# 直接下载(用户名是root则不用sudo)
# Java 8
apt install openjdk-8-jdk -y
# Java 11
apt install openjdk-11-jdk -y
# Java 17
apt install openjdk-17-jdk -y
# Java 21
apt install openjdk-21-jdk -y

# 查看java版本
java -version
```

##### mcsmanager下载

```bash
# 下载
sudo wget -qO- https://gitee.com/mcsmanager/script/raw/master/setup_cn.sh | sudo bash

# 旧版：
# 启用 MCSManager 服务：
systemctl enable mcsm-{daemon,web}.service
# 运行 MCSManager 服务：
systemctl start mcsm-{daemon,web}.service
# 检查 MCSManager 服务状态：
systemctl status mcsm-{daemon,web}.service

# 新版：
  systemctl start   mcsm-daemon.service
  systemctl stop    mcsm-daemon.service
  systemctl restart mcsm-daemon.service
  systemctl status  mcsm-daemon.service
  systemctl start   mcsm-web.service
  systemctl stop    mcsm-web.service
  systemctl restart mcsm-web.service
  systemctl status  mcsm-web.service
```

运行好了之后在**服务器地址**的23333端口上打开面板即可

官方文档:
  https://docs.mcsmanager.com/zh_cn/

### 开服流程

这里我们可以打开mcsmanager进行操作，有个web作图形化界面还是很方便的

点击新建应用/创建实例

然后导入压缩包（即服务端.jar文件）

#### 一、运行服务端.jar文件

在MCSmanager中将服务端放入服务器中（其他放上去的方式各显神通），此时实例类型可以选择MCjava版服务端，这样它可以帮我们配置很多东西

首先，我们要找到服务端核心对应的.jar文件，也就是从https://mcversions.net/或者其他整合包来源地下载的那个，如果上传时找不到，是因为他只能识别.zip文件，将服务端的.jar文件压缩即可，他会帮我们自动解压

然后在服务器中运行：

```bash
# 假设你的服务器有GUI（图形化）界面（虽然一般没有）
java -jar forge-1.20.1-47.2.0-installer.jar 
# 假设你的服务器是纯CLI（命令行），则
java -jar forge-1.20.1-47.2.0-installer.jar --installServer
# 注意：有些时候一个不行就换另一个，这个面板有时候比较奇怪

# 如果你想直接在服务器上下载.jar，可以例如：
wget https://maven.minecraftforge.net/net/minecraftforge/forge/1.20.1-47.2.0/forge-1.20.1-47.2.0-installer.jar
# 具体可以问ai，让ai给你相应版本的下载指令
```

这一步会生成我们真正需要的服务端核心文件的文件夹，有些情况下会直接帮你启动，但是如果只是帮你生成了完整服务端，则使用下面命令贴到Mcsmanager的启动命令中

#### 二、运行/启动

```bash
# 方案A：使用生成的启动脚本（推荐）
./run.sh

# 方案B：手动指定Java命令
java -Xmx4G -jar forge-1.20.1-47.2.0-universal.jar nogui
```

一般推荐使用方案A

#### 三、配置

##### 同意eula

eula配置可以在MCsmanager中服务端配置文件中找到找到

##### 启动命令/实例种类

mcsmanager中选择应用实例设置可以修改



