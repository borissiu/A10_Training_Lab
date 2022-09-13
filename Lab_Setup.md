![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## A10 Lab. Setup
1. 安装 VirtualBox - https://www.virtualbox.org/wiki/Downloads
1. 安装 VMWare Workstation - https://www.vmware.com/go/getplayer-win
2. 安装 (i) A10 vADC - https://a10networks.sharefile.com/d-s7cf6644f59004178b57aa135e5f7d531
    + 最少 4 vCPU (建议 4+ vCPU)
    + 最少 2 网卡 (必须 2 网卡或更多)
    + 建议 8GB Memory
    + 最少 16G 磁盘 (建议 30GB)
3. 安装 (ii)一个客户端 和 (iii)两个服务器 - https://ubuntu.com/download/server
    + 可考虑用3个IP address(es), 把(ii)和(iii)配置在同一台服务器上
4. 配置 (i)vADC, (ii)客户端和 (iii)服务器 都能够上网


## 安装 (i) A10 vADC
```
Run VirtualBox (version 6.1.38)
Create a NEW Machine (Name=vADC01, Type=Linux, Version=Ubuntu (64-bits)
Create a virtual hard disk (10.00GB)
Select VDI (VirtualBox Disk Image)
Select Dynamically Allocated
Change Setting
  + Base Memory 8GB
  + 4 vCPU
  + Add 2 NICs
  + Storage
Start Virtual Machine to install vADC
  + username=install, password=password
  + YesS
Remove ISO after install vADC
Start vADC (It may takes 7 mins)
  + username=admin, password=a10
```


##


## vADC 配置 (通过VirtualBox控制台) 
```

```

## 客户端和服务器 配置
```
# NTP 配置
sudo systemctl set-timezone Asia/Shanghai
sudo systemctl restart systemd-timesyncd.service
```

## 基本测试
+ vADC, 客户端和服务器, 都能ping通 114.114.114.114 
+ vADC 能ping 通客户端和服务器
+ SSH 和GUI 能登录vADC (用户名=admin, 密码=a10)
+ SSH 能登录客户端和服务器