![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## A10 Lab. Setup
1. 安装 VMWare Workstation Version
    + 下载v16版本 https://www.vmware.com/go/getplayer-win
      + v16版本, 不支持Win7
      + v12版本, 支持Win7
2. 安装 (i) 2至3台 A10 vADC 
    + 下载 https://a10networks.sharefile.com/d-s50d9c08c840b49b68f50db1647596692
    + 每台 vADC 最少 4 vCPU (建议 4+ vCPU)
    + 每台 vADC 必须 2 网卡 (或更多)
    + 每台 vADC 最少 4GB 内存
    + 每台 vADC 最少 16G 磁盘
3. 安装 (ii)两个客户端 和 (iii)两个服务器
    + 下载 https://ubuntu.com/download/server
    + 可考虑用多个IP address(es), 把(ii)和(iii)配置在同一台服务器上
4. 配置 (i)vADC, (ii)客户端和 (iii)服务器 都能够上网

## IP Addresses
Run VMWare Workstation
  + Click Edit > Virtual Network Editor
    + Check IP subnet of VMNet1 (Type : Host-Only)
    + Check IP subnet of VMNet8 (Type : NAT)

IP Addresses 示例 
|  | vADC521_01 | vADC521_02 | vADC521_03 | 客户端_1 | 客户端_2 | 服务器_1 | 服务器_2 |
|---|---|---|---|---|---|---|---|
| Mgmt | 192.168.247.11 | 192.168.247.12 | 192.168.247.13 | 192.168.247.21 | 192.168.247.21 | 192.168.247.21 | 192.168.247.21 |
| Data | 192.168.226.11 | 192.168.226.12 | 192.168.226.13 | 192.168.226.21 | 192.168.226.22 | 192.168.226.23 | 192.168.226.24 |
| H.A. | 10.10.10.11 | 10.10.10.12 | 10.10.10.13 |  |  |  |  |

## 安装第一台 (i) A10 vADC
```
Run VMWare Workstation
Create a NEW Virtual Machine
  + 选择 Typical
Installer disc image file (ISO)
  + 选择 ACOS_vThunder_5_2_1-p5_114.ISO, Linux, Ubuntu 64-bit
  + 记录安装路径 "drive:/directory/"
Virtual machine name
  + vADC521_01
  + 选择 20G disk, Store virtual disk into multiple files
Customize Hardware
  + 选择 4+ GB Memory
  + 选择 4+ vCPU
  + 选择 Network Adapter = Custom VMnet1
  + 必须 Add Network Adapter 2 = Custom VMNet8
  + 必须 Add Network Adapter 3 = Custom VMNet9
Power on the NEW Virtual Machine
  + 等待 localhost login:
    + username=install, password=password
    + continue=YesS
Logon vADC and then Shutdown it
  + 等待 vThunder login: (vThunder(LOADING)> mean vADC NOT yet bootup)
    + username=admin, password=a10
    + enable
    + shutdown
Edit vADC521_01.vmx file (driver:/directory/)
  + 保持不变 ethernet0.virtualDev = "e1000"
  + 必须修改 ethernet1.virtualDev = "vmx3net"
  + 必须修改 ethernet2.virtualDev = "vmx3net"
Start vADC521_01
  + vThunder login:
    + username=admin, password=a10
    + enable
    + show interface brief (you should see 3 NICs)
```

## 配置第一台 vADC (通过 VMWare Workstation 控制台) 
#### Start vADC521_01
```
  + 等待 vThunder login: (vThunder(LOADING)> mean vADC NOT yet bootup)
    + username=admin, password=a10
    + enable
```

#### 将以下配置复制并粘贴到 vADC
```
configure terminal
!
system shared-poll-mode enable
!
hostname vADC521_01
timezone Asia/Shanghai
ip dns primary 114.114.114.114
ntp server stdtime.gov.hk
authentication login privilege-mode local
multi-config enable
!
terminal idle-timeout 0
web-service gui-timeout-policy idle 0
!
interface management
  ip add 192.168.247.11 /24
!
interface ethernet 1
  enable
interface ethernet 2
  enable
!
vlan 10
  untag ethernet 1
  route ve 10
interface ve 10
  ip address 192.168.226.11 /24
  enable
  name Data
!
vlan 20
  untag ethernet 2
  route ve 20
interface ve 20
  ip address 10.10.10.11 /24
  enable
  name Heartbeat
!
ip route 0.0.0.0 /0 192.168.226.2
!
enable-management service ssh
  ve 10
  ve 20
enable-management service https
  ve 10
  ve 20
enable-management service snmp
  ve 10
end
write memory
!
```

## 安装 (ii) 客户端和 (iii)服务器
```
Run VMWare Workstation
Create a NEW Virtual Machine
  + 选择 Typical
Installer disc image file (ISO)
  + 选择 Ubuntu-22.04.1-live-server-amd64.ISO, Linux, Ubuntu 64-bit
Virtual machine name
  + ubuntu_01
  + 选择 20G disk, Store virtual disk into multiple files
Customize Hardware
  + 选择 2+ GB Memory
  + 选择 4+ vCPU
  + 选择 Network Adapter = Custom VMnet1
  + 必须 Add Network Adapter 2 = Custom VMNet8
Power on the NEW Virtual Machine
  + Install Ubuntu
  + 必须修改 NIC-1
      ip address 192.168.247.21/24 (Not DHCP)
  + 必须修改 NIC-2
      ip address 192.168.226.21/24 (Not DHCP)
      gateway 192.168.226.2
      dns 114.114.114.114
  + 必须添加安装
      Open SSH Server
```

## 配置客户端和服务器 
将以下配置复制并粘贴到 Ubuntu01
```
# 配置 NTP 
sudo timedatectl set-timezone Asia/Shanghai
sudo systemctl restart systemd-timesyncd.service

# 配置 WebServer 
sudo apt-get -y install nginx

# 备份 Network setting
sudo cp /etc/netplan/00-installer-config.yaml /etc/netplan/00-installer-config.yaml.bak
```

#### 配置示例 - 添加额外的ip地址 
```
# boris@ubuntu100:~$ more /etc/netplan/00-installer-config.yaml

# This is the network config written by 'subiquity'
network:
  ethernets:
    ens33:
      addresses:
      - 192.168.247.21/24
    ens34:
      addresses: [192.168.226.21/24, 192.168.226.22/24, 192.168.226.23/24, 192.168.226.24/24]
      gateway4: 192.168.226.2
      nameservers:
        addresses: [114.114.114.114]
        search: []
  version: 2
```

#### 添加额外的ip地址
```
sudo vi /etc/netplan/00-installer-config.yaml

sudo netplan apply
```

## 基本测试
+ vADC521_01, 客户端和服务器, 都能 ping 通 114.114.114.114 
+ vADC521_01 能 ping 通客户端和服务器
+ SSH 和 GUI 能登录 vADC (用户名=admin, 密码=a10)
+ SSH 能登录客户端和服务器

## 配置第 2 和第 3 台 A10 vADC 
#### Shutdown vADC521_01 before Clone
```
write memory
shutdown
```

#### Clone 2台 vADC
```
Select vADC521_01
  + Click VM > Manage > Clone > Select "Current State" > Select "Create a FULL clone" > Change Virtual Name to vADC521_02
Select vADC521_01
  + Click VM > Manage > Clone > Select "Current State" > Select "Create a FULL clone" > Change Virtual Name to vADC521_03
```

#### 修改第2台 vADC 配置
```
configure terminal
!
hostname vADC521_02
!
interface ve 10
  ip address 192.168.226.12 /24
  no ip address 192.168.226.11 /24
interface ve 20
  ip address 10.10.10.12 /24
  no ip address 10.10.10.11 /24
!
write memory
!
interface management
  ip add 192.168.247.12 /24
!
!!! Connection Lost after changing mgmt IP
!!! Reconnect via NEW mgmt IP and then "Write Memory"
```

#### 修改第3台 vADC 配置
```
configure terminal
!
hostname vADC521_03
!
interface ve 10
  ip address 192.168.226.13 /24
  no ip address 192.168.226.11 /24
interface ve 20
  ip address 10.10.10.13 /24
  no ip address 10.10.10.11 /24
!
write memory
!
interface management
  ip add 192.168.247.13 /24
!
!!! Connection Lost after changing mgmt IP
!!! Reconnect via NEW mgmt IP and then "Write Memory"
```

## 基本测试
+ vADC521_02 和 vADC521_03, 都能 ping 通 114.114.114.114 
+ vADC521_02 和 vADC521_03, 都能 ping 通客户端和服务器
+ SSH 和 GUI 能登录 vADC521_02 和 vADC521_03 (用户名=admin, 密码=a10)
