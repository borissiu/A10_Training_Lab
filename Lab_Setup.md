![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## A10 Lab. Setup
1. 安装 VMWare Workstation - https://www.vmware.com/go/getplayer-win (For Win8 or above)
2. 安装 (i) A10 vADC - https://a10networks.sharefile.com/d-s7cf6644f59004178b57aa135e5f7d531
    + 最少 4 vCPU (建议 4+ vCPU)
    + 必须 2 网卡 (或更多)
    + 最少 4GB 内存
    + 最少 16G 磁盘
3. 安装 (ii)一个客户端 和 (iii)两个服务器 - https://ubuntu.com/download/server
    + 可考虑用3个IP address(es), 把(ii)和(iii)配置在同一台服务器上
4. 配置 (i)vADC, (ii)客户端和 (iii)服务器 都能够上网

## 安装 (i) A10 vADC
```
Run VMWare Workstation
Create a NEW Virtual Machine - Typical
Installer disc image file (iso) - ACOS_vThunder_5_2_1-p5_114.iso (Linux, Ubuntu 64-bit)
Virtual machine name - vADC521_01 (20G disk, Store virtual disk into multiple files)
Customize Hardware
  + 4+ GB Memory
  + 4+ vCPU
  + Network Adapter = Custom VMnet1
  + Add Network Adapter 2 = Custom VMNet8
Power on the NEW Virtual Machine
  + localhost login:
    + username=install, password=password
    + continue=YesS
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
# /etc/netplan/00-installer-config.yaml


```

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