![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## A10 Lab. Setup
1. 安装 (i) 2至3台 A10 vADC 
    + 下载 https://a10networks.sharefile.com/d-s50d9c08c840b49b68f50db1647596692
    + 每台 vADC 最少 4 vCPU (建议 4+ vCPU)
    + 每台 vADC 必须 2 网卡 (或更多)
    + 每台 vADC 最少 6GB 内存
    + 每台 vADC 最少 16G 磁盘
2. 安装 (ii)两个客户端 和 (iii)两个服务器
    + 下载 https://ubuntu.com/download/server
    + 可考虑用多个IP address(es), 把(ii)和(iii)配置在同一台服务器上
3. 配置 (i)vADC, (ii)客户端和 (iii)服务器 都能够上网

## IP Addresses
IP Addresses 示例
| Device | Mgmt | | Eth1 | Virtual Server | Source NAT | Eth2 | | Eth3 | Eth4 |
|---|---|---|---|---|---|---|---|---|---|
| 客户端_1    | 10.10.10.99 | | 10.240.70.xx | | | | | | |
| 客户端_2    |             | | 10.240.70.yy | | | | | | |
| 服务器_1    |             | | 10.240.70.101| | | | | | |
| 服务器_2    |             | | 10.240.70.102| | | | | | |
| vADC521_01 | 10.10.10.21 | | 10.240.70.21 | .70.31 | .70.161| 192.168.1.21 | | 10.240.70.21 | 192.168.2.21 |
| vADC521_02 | 10.10.10.22 | | 10.240.70.22 | .70.32 | .70.162| 192.168.1.22 | | 10.240.70.21 | 192.168.2.22 |
| vADC521_03 | 10.10.10.23 | | 10.240.70.23 | .70.33 | .70.43 | 192.168.1.23 | | 10.240.70.21 | 192.168.2.23 |
| vADC521_04 | 10.10.10.24 | | 10.240.70.24 | .70.34 | .70.44 | 192.168.1.24 | | 10.240.70.21 | 192.168.2.24 |
| vADC521_05 | 10.10.10.25 | | 10.240.70.25 | .70.35 | .70.45 | 192.168.1.25 | | 10.240.70.21 | 192.168.2.25 |
| vADC521_06 | 10.10.10.26 | | 10.240.70.26 | .70.36 | .70.46 | 192.168.1.26 | | 10.240.70.21 | 192.168.2.26 |
| vADC521_07 | 10.10.10.27 | | 10.240.70.27 | .70.37 | .70.47 | 192.168.1.27 | | 10.240.70.21 | 192.168.2.27 |
| vADC521_08 | 10.10.10.28 | | 10.240.70.28 | .70.38 | .70.48 | 192.168.1.28 | | 10.240.70.21 | 192.168.2.28 |
| vADC521_09 | 10.10.10.29 | | 10.240.70.29 | .70.39 | .70.49 | 192.168.1.29 | | 10.240.70.21 | 192.168.2.29 |
| vADC521_10 | 10.10.10.30 | | 10.240.70.30 | .70.40 | .70.170| 192.168.1.30 | | 10.240.70.21 | 192.168.2.30 |

## ESXi
## 安装第一台 (i) A10 vADC
```
Run VMWare ESXi
Create a NEW Virtual Machine
  + Name: vADC521_01
  + Guest OS family: Linux
  + Guest OS version: Ubuntu Linux (64-bit)
  + Standard storage
Virtual Hardware
  + 选择 4+ vCPU
  + 选择 4+ GB Memory
  + 选择 20GB disk
  + 必须 Network Adapter 1 (内网)
  + 必须 Network Adapter 2 (能够上网)
  + 可选 Network Adapter 3 (内网)
  + 可选 Network Adapter 4 (内网)
  + 可选 Network Adapter 5 (内网)
  + CD/DVD Drive: Data Store ISO file (选择 ACOS_vThunder_5_2_1-p5_114.ISO)
Check Virtual Machine Network Adapter 顺序
    + Network Adapter 1 (内网)
    + Network Adapter 2 (能够上网)
    + Network Adapter 3 (内网)
    + Network Adapter 4 (内网)
    + Network Adapter 5 (内网)
Power on the NEW Virtual Machine (Check NICs before Power On)
  + 等待 localhost login:
    + username=install, password=password
    + continue=YesS
Logon vADC and then Shutdown it
  + 等待 vThunder login: (vThunder(LOADING)> mean vADC NOT yet bootup)
    + username=admin, password=a10
    + enable (no enable password, just press "Enter")
    + show interface brief (you should see 5 NICs)

```

## 配置第一台 vADC (通过 VMWare 控制台) 
#### Start vADC521_01
```
  + 等待 vThunder login: (vThunder(LOADING)> mean vADC NOT yet bootup)
    + username=admin, password=a10
    + enable
```

#### 配置第一台 vADC (通过 VMWare 控制台) 
```
enable
!
configure terminal
!
interface ethernet 1
  enable
!
vlan 10
  untag ethernet 1
  route ve 10
!
interface ve 10
  ip address 10.240.70.21 /24
!
ip route 0.0.0.0 /0 10.240.70.1
!
enable-management service ssh
  ve 10
end
!
!
!
ping 8.8.8.8

```

#### 通过 SSH 添加配置 (e.g. SSH admin@10.240.70.21 )
#### 将以下配置复制并粘贴到 vADC
```
!
enable
!
configure terminal
!
system shared-poll-mode enable
!
slb common
  ssl-module software-tls13
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
  ip add 10.10.10.21 /24
  ip default-gateway 10.10.10.1
!
interface ethernet 2
  enable
!
vlan 20
  untag ethernet 2
  route ve 20
!
interface ve 20
  ip address 192.168.1.21 /24
  enable
  name Heartbeat
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
!
write memory lab00
y
!
reboot
!
yes

```

## 安装 (ii) 客户端和 (iii)服务器
```
Create a NEW Virtual Machine
Virtual machine name
  + ubuntu_101
  + 选择 20G disk, Store virtual disk into multiple files
  + 选择 Ubuntu-22.04.1-live-server-amd64.ISO, Linux, Ubuntu 64-bit
Customize Hardware
  + 选择 2+ GB Memory
  + 选择 4+ vCPU
  + Network Adapter 1 (内网)
  + Network Adapter 2 (能够上网)
Power on the NEW Virtual Machine
  + Install Ubuntu
  + 必须修改 NIC-1
      ip address 10.10.10.99/24 (Not DHCP)
  + 必须修改 NIC-2
      ip address 10.240.70.101/24 (Not DHCP)
      gateway 10.240.70.1
      dns 114.114.114.114
  + 必须添加安装
      Open SSH Server
Reboot after installing Ubuntu Server
  + Remove the ISO

```

## 配置客户端和服务器 
将以下配置复制并粘贴到 Ubuntu101
```
# 配置 NTP 
sudo timedatectl set-timezone Asia/Shanghai
sudo systemctl restart systemd-timesyncd.service

# 配置 net-tools 
sudo apt -y install net-tools

# 配置 WebServer 
sudo apt-get -y install nginx

# 备份 Network setting
sudo cp /etc/netplan/00-installer-config.yaml /etc/netplan/00-installer-config.yaml.bak
```

#### 配置示例 - 添加额外的ip地址
+ Change xx & yy to IP not yet use
```
# boris@ubuntu100:~$ more /etc/netplan/00-installer-config.yaml

# This is the network config written by 'subiquity'
network:
  ethernets:
    ens33:
      addresses:
      - 10.10.10.99/24
    ens34:
      addresses: [10.240.70.xx/24, 10.240.70.yy/24, 10.240.70.101/24, 10.240.70.102/24]
      gateway4: 10.240.70.1
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
!
shutdown
```

#### Clone 2台 vADC
```
Select vADC521_01
  + Click VM > Manage > Clone > Select "Current State" > Select "Create a FULL clone" > Change Virtual Name to vADC521_02
Select vADC521_01
  + Click VM > Manage > Clone > Select "Current State" > Select "Create a FULL clone" > Change Virtual Name to vADC521_03
```

#### 修改第2台 vADC 配置 (通过 VMWare 控制台)
```
configure terminal
!
hostname vADC521_02
!
interface management
  ip add 10.10.10.22 /24
!
interface ve 10
  ip address 10.240.70.22 /24
  no ip address 10.240.70.21 /24
!
interface ve 20
  ip address 192.168.1.22 /24
  no ip address 192.168.1.21 /24
!
write memory

```

#### 修改第3台 vADC 配置 (通过 VMWare 控制台)
```
configure terminal
!
hostname vADC521_03
!
interface management
  ip add 10.10.10.23 /24
!
interface ve 10
  ip address 10.240.70.23 /24
  no ip address 10.240.70.21 /24
!
interface ve 20
  ip address 192.168.1.23 /24
  no ip address 192.168.1.21 /24
!
write memory

```

## 基本测试
+ vADC521_02 和 vADC521_03, 都能 ping 通 114.114.114.114 
+ vADC521_02 和 vADC521_03, 都能 ping 通客户端和服务器
+ SSH 和 GUI 能登录 vADC521_02 和 vADC521_03 (用户名=admin, 密码=a10)

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)