![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## A10 Lab. Setup
1. 安装 VMWare Workstation ver16 - https://www.vmware.com/go/getplayer-win
    + (ver16 do NOT support Win7.  Search Workstation ver12 for Win7)
2. 安装 (i) A10 vADC - https://a10networks.sharefile.com/d-s50d9c08c840b49b68f50db1647596692
    + 最少 4 vCPU (建议 4+ vCPU)
    + 必须 2 网卡 (或更多)
    + 最少 4GB 内存
    + 最少 16G 磁盘
3. 安装 (ii)两个客户端 和 (iii)两个服务器 - https://ubuntu.com/download/server
    + 可考虑用4个IP address(es), 把(ii)和(iii)配置在同一台服务器上
4. 配置 (i)vADC, (ii)客户端和 (iii)服务器 都能够上网

## 安装 (i) A10 vADC
```
Run VMWare Workstation
Create a NEW Virtual Machine - Typical
Installer disc image file (ISO) - ACOS_vThunder_5_2_1-p5_114.ISO (Linux, Ubuntu 64-bit)
  + Record the installation "drive:/directory/"
Virtual machine name - vADC521_01 (20G disk, Store virtual disk into multiple files)
Customize Hardware
  + 4+ GB Memory
  + 4+ vCPU
  + Network Adapter = Custom VMnet1
  + Add Network Adapter 2 = Custom VMNet8
  + Add Network Adapter 3 = Custom VMNet9
Power on the NEW Virtual Machine
  + localhost login:
    + username=install, password=password
    + continue=YesS
Logon vADC and shutdown it (vThunder(LOADING)> mean vADC NOT yet bootup)
  + vThunder login:
    + username=admin, password=a10
    + enable
    + shutdown
Edit vADC521_01.vmx file (driver:/directory/)
  + keep ethernet0.virtualDev setting remain unchange
  + change ethernet1.virtualDev from e1000 to vmx3net
  + change ethernet2.virtualDev from e1000 to vmx3net
Start vADC521_01
  + vThunder login:
    + username=admin, password=a10
    + enable
    + show interface brief (you should see 3 NICs)
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