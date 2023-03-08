---
title: Pi Network节点搭建
date: 2023-03-07 14:05:53
author: 小派派
img: https://raw.githubusercontent.com/xhdou/image/main/image/FS7KGcVUAEPHwP.jpg
top: true
hide: false
cover: true
coverImg: https://raw.githubusercontent.com/xhdou/image/main/image/FS7KGcVUAEPHwP.jpg
toc: true
mathjax: false
categories: 计算机
tags:
  - 区块链
  - 节点
  - Pi Network
---
## 一、新人常见问题
### （一）版本选择，045还是047？
目前官方版本是`045`，但是大陆有墙，我们的节点无法正常与项目方的服务器进行通信，因此当时项目方开发了`047`版本，但是`047`版本虽然可以同步，但是通过抓包可以发现`047`无法向项目方服务器发送`heart`包（相当于工作打卡记录），因此`047`无法获取奖励。

其次，`047`需要公网`ip`，目前国内公网`ip`貌似很难申请，至少据我了解江苏境内的电信都已经停止了申请公网`ip`业务（移动、联通未了解）。而045需要购买境外云服，通过云服来做内网穿透及科学上网，云服费用`20-50RMB`/月不等。

有人说`045`无法竞争超级节点，其实吧，从一开始就坚持047的人我就不好说什么了，但是就现阶段，你说你即便选择`047`，你超级节点能竞争过那些坚持一两年的人么，而且还没奖励。所以我个人建议新人选择`045`，直接做普通节点拿奖励就好。

以下搭建教程也是针对`045`版本的。

### （二）需要什么电脑配置？
节点电脑主要看两个硬件，`CPU`和运行内存。

`CPU`的话，`i3`以上就行，越好奖励越高，运行内存`8G`以上。

现在也有很多人用洋垃圾（工作室用的那种电脑，能多开）做节点了，确实奖励很高，因为E系列`CPU`核心数和线程数高，像我自己最近换了一个`18`核`36`线程的，`X99`大板，`64`内存，也就`2000RMB`。当然洋垃圾质量存在一定风险，这个自行了解评判。

总之一个原则，核心数和线程数越高，节点的奖励也相对越高。

### （三）电脑系统选择？
目前普遍是`win10`专业版，版本尽可能新点就好，也看到有人用`win11`系统，系统安装的事情就自行解决了，本教程不负责。

如果电脑是专门用作节点的，建议将存储盘合并，只留一个C盘。

## 二、购买云服务器（VPS）
服务器厂家太多了，自行了解吧，总之一个原则，没有又便宜又稳定的云服。我个人是选择的[vultr](https://www.vultr.com/)，洛杉矶机房，`Ubuntu`系统，5$/月的套餐，购买教程就不出了，网上多的很，也可以参考这篇[教程](https://www.vultrcn.com/1.html)。

购买好后可以用[FinalShell](http://www.hostbuf.com/)远程登录,小白可以参考这篇[教程](https://blog.csdn.net/weixin_44024740/article/details/122277082)。

## 三、魔法上网
折腾过`OpenVpn`、`WireGuard`、`V2ray`、`Navie`，最终还是选择了`OpenVpn`（踩过的坑太多，都是泪~）。

### （一）服务端安装
用`FinalShell`远程登录你的`VPS`，如果你购买的是腾讯云的，可能需要先获得`root`权限，输入命令`sudo -i`即可。

#### 1、安装服务

一件安装脚本：
```shell
wget https://git.io/vpn -O openvpn-install.sh && bash openvpn-install.sh
```
![](https://raw.githubusercontent.com/xhdou/image/main/20230308113824.png)

下图中，`52.68.245.48`是你的`vps`的`IP`地址，可以直接回车默认，或者输入提前解析到这个`ip`地址的域名，回车。
![](https://raw.githubusercontent.com/xhdou/image/main/20230308113915.png)

如下图：
![](https://raw.githubusercontent.com/xhdou/image/main/20230308142922.png)
1. 选择协议：输入`1`；
2. 是选择`openvpn`使用的端口，默认`1194`，或自定义；
3. 是选择`DNS`,我喜欢用 `Google`；
4. 是选择`openvpn`配置文件的文件名，自定义或默认，我选择默认，直接回车；
5. 按任意键安装`Openvpn`，回车即可开始安装。

安装完毕，下载配置文件，位于 `/root/client.ovpn`,点击下图中的刷新，即可看到。右键下载即可。
![](https://raw.githubusercontent.com/xhdou/image/main/20230308121919.png)

#### 2、固定内网ip（重点）

脚本安装的`openvpn`分配的内网`ip`会在`10.8.0.2`和`10.8.0.3`之间变化，但后面端口转发绑定的`ip`是`10.8.0.2`，因此需要将`openvpn`分配的ip固定。我就固定在了`10.8.0.2`，因此教程也按照此来写的。

（1）创建CCD文件夹
```shell
sudo mkdir /etc/openvpn/server/ccd
```
![](https://raw.githubusercontent.com/xhdou/image/main/20230308122507.png)

（2）在`ccd`中创建文件，文件命要和客户端命名的一致，我的就是默认的`client`
```shell
sudo touch /etc/openvpn/server/ccd/client
```
（3）在上一步创建的`client`文件中输入：
```base
ifconfig-push 10.8.0.2 255.255.255.0    
```
`10.8.0.2`是你要固定`ip`，`255.255.255.0`是子网掩码，每个人可能不一样，以下是查询方法：
  - 命令输入：`ifconfig`
  - 查看网卡`tun0`的`netmask`
![](https://raw.githubusercontent.com/xhdou/image/main/20230308122812.png)

（4）修改`/etc/openvpn/server/server.conf`，增加：
```
client-config-dir ccd
```
![](https://raw.githubusercontent.com/xhdou/image/main/20230308122933.png)

（5）重启服务：
```shell
systemctl restart openvpn-server@server
```

### （二）客户端安装
首先下载`windows`客户端软件，[OpenVPN客户端下载](https://openvpn.net/vpn-client/)。

安装完毕后，把之前下载的`client.ovpn`文件拖拽到下图虚线框中。
![](https://raw.githubusercontent.com/xhdou/image/main/20230308130849.png)

点击，`CONNECT`，点击图中的开关，变成绿色，下面出现流量波形，看到有上传下载数据了，就是连上服务器了，你的`VPN`可以正常使用了。
![](https://raw.githubusercontent.com/xhdou/image/main/dfed0e0f3be516401cda037392f2a2c.jpg)

>备注：如果用的是腾讯云这种有防火墙面板的云服，则需要在防火墙管理面板中，打开相应的端口（即`1194`），否则会连接不上。
## 四、端口转发
1. 远程登录`vps`，关闭防火墙。
```shell
sudo ufw disable   # 关闭防火墙
sudo ufw enable    # 其用防火墙
sudo ufw status    # 查看防火墙状态
```
2. 安装`rintrd`
```shell
apt-get install rinetd 
```
3. 配置`/etc/rinetd.conf` 文件
```
0.0.0.0     31400       10.8.0.2      31400
0.0.0.0     31401       10.8.0.2      31401
0.0.0.0     31402       10.8.0.2      31402
0.0.0.0     31403       10.8.0.2      31403
0.0.0.0     31404       10.8.0.2      31404
0.0.0.0     31405       10.8.0.2      31405
0.0.0.0     31406       10.8.0.2      31406
0.0.0.0     31407       10.8.0.2      31407
0.0.0.0     31408       10.8.0.2      31408
0.0.0.0     31409       10.8.0.2      31409
```
![](https://raw.githubusercontent.com/xhdou/image/main/20230308144202.png)

>备注：
>`0.0.0.0`表示本机绑定所有可用地址
>`10.8.0.2`是`OpenVPN`分配的ip4地址

4. 关闭程序：
```shell
pkill rinetd
```

5. 启动程序：
```shell
rinetd -c /etc/rinetd.conf 
```

6. `rinted`常用命令:
```base
systemctl start rinetd      # 启动rinetd服务
systemctl restart rinetd    # 重启rinetd服务
systemctl stop rinetd       # 停止rinetd服务
systemctl enable rinetd     # 设置rinetd开机自启动
systemctl reload rinetd     # 修改/etc/rinetd.conf之后，重新加载配置
```

## 五、Docker安装
### 1、系统设置
首先需要进入`Bios`，打开虚拟化功能。
![](https://raw.githubusercontent.com/xhdou/image/main/20230308132159.png)

将`windows`功能中的`Hyper-V`和`适用于Linux的windows子系统`勾选。
![](https://raw.githubusercontent.com/xhdou/image/main/20230308132824.png)

### 2、WSL 2安装
参考Microsoft的[官方文档](https://learn.microsoft.com/en-us/windows/wsl/install)。
>方法一
以管理员模式打开 `PowerShell` 或` Windows` 命令提示符，输入 `wsl --install` 命令，然后重新启动计算机。

>方法二
[下载安装包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)，下载完成后直接安装，让后重启计算机。
>>备注：
>>如果使用的是 `ARM64` 计算机，请下载 `ARM64` 包。 如果不确定自己计算机的类型，请打开命令提示符或 `PowerShell`，并输入：`systeminfo | find "System Type"`。 `Caveat`： 在非英文版 `Windows` 上，你可能必须修改搜索文本，对`“System Type”`字符串进行翻译。 你可能还需要对引号进行转义来用于 `find` 命令。 例如，在德语版中使用 `systeminfo | find '"Systemtyp"'`。
### 3、Docker安装
`Docker`的安装教程可以参考[官方文档](https://docs.docker.com/desktop/install/windows-install/)。
[下载Docker客户端](https://docs.docker.com/desktop/install/windows-install/)（也可以命令安装，自己看官方文档），也可以下载[往期版本](https://docs.docker.com/desktop/release-notes/)。

下载完成后直接安装就行了，安装完成后在设置里把自动更新给关掉。

## 六、Pi Node安装
### 1、Pi Node安装
在`Pi Network`官网上[下载Pi Node](https://node.minepi.com/node/)(打开网站需要魔法)，选择windows版本。
![](https://raw.githubusercontent.com/xhdou/image/main/20230308131654.png)

下载后直接安装，默认就是安装在C盘的。

安装完成后打开，会提示在手机端验证绑定。
![](https://raw.githubusercontent.com/xhdou/image/main/20230308135834.png)

在手机端`APP`里`Node`里输入对应的验证码即可。
![](https://raw.githubusercontent.com/xhdou/image/main/20230308140032.png)


### 2、拉取容器
点击界面上`Node`
![](https://raw.githubusercontent.com/xhdou/image/main/20230308140158.png)

依次点击`Continue`,`Troubleshooting`
![](https://raw.githubusercontent.com/xhdou/image/main/9b8f4b20460220f3aaf633495c4328b.jpg)

随后等待同步即可，下图为已同步的正常节点。
![](https://raw.githubusercontent.com/xhdou/image/main/20230308141641.png)

>有些新节点可能拉取不出来，可以[下载047版本](https://d.pinode.cc/download/pi-node-0.4.7.exe)覆盖安装，等容器拉取出来后，再安装`045`版本覆盖。

## 七、节点电脑系统其他设置
### 1、电源设置
![](https://raw.githubusercontent.com/xhdou/image/main/20230308142029.png)

### 2、禁用系统更新
![](https://raw.githubusercontent.com/xhdou/image/main/20230308142112.png)
![](https://raw.githubusercontent.com/xhdou/image/main/20230308142138.png)
![](https://raw.githubusercontent.com/xhdou/image/main/20230308142159.png)

### 3、时间和日期
![](https://raw.githubusercontent.com/xhdou/image/main/20230308142226.png)

