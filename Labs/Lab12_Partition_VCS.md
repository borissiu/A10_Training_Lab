![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 12 - 分区 (Partitions) 和 虚拟机箱 (aVCS)
- Shared & L3V Partition (Layer 3 虚拟分区)
  + 单台设备作为多台虚拟设备使用
- VCS (Virtual Chassis System)
  + 单一 IP 管理多台设备
  + 配置自动同步

#### 粘贴以下命令到 vADC521_01
```
write memory lab12
y
!
show startup-config all

```

## Shared & L3V Partition
+ 2人1组
#### 粘贴以下命令到 vADC521_01
```
!
configure terminal
!
partition p1 id 1 application-type adc
!
active-partition p1
!
vlan 30
  untagged ethernet 3
  router-interface ve 30
!
vlan 40
  untagged ethernet 4
  router-interface ve 40
!
interface ethernet 3
  enable
!
interface ethernet 4
  enable
!
interface ve 30
  ip address 10.240.70.21 255.255.255.0
!
interface ve 40
  name Heartbeat
  ip address 192.168.1.21 255.255.255.0
!
end

```

#### 粘贴以下命令到 vADC521_02
```
!
configure terminal
!
partition p1 id 1 application-type adc
!
active-partition p1
!
vlan 30
  untagged ethernet 3
  router-interface ve 30
!
vlan 40
  untagged ethernet 4
  router-interface ve 40
!
interface ethernet 3
  enable
!
interface ethernet 4
  enable
!
interface ve 30
  ip address 10.240.70.22 255.255.255.0
!
interface ve 40
  name Heartbeat
  ip address 192.168.1.22 255.255.255.0
!
end

```

#### 粘贴以下命令到 vADC521_02，并检查相应的输出
+ Ping Ok?
+ shared partition & p1 partition 支持相同的ip地址?
  + 不适用于一般企业网络情况
  + 适用于多租户网络情况 (例如电信提供虚拟 ADC 给租户, 但不同的租户可能使用相同的Private IP)
```
!
ping 192.168.1.21
!
ping 10.240.70.21
!
show vrrp-a

```

#### 粘贴以下命令到 vADC521_02，并检查相应的输出
+ 命令提示符有变化?
+ 如何 Switch to shared 分区
+ 有独立的 MAC 表, ARP 表, 路由表?
```
!
active-partition shared
!
show mac
!
show arp
!
show ip route

```

#### 粘贴以下命令到 vADC521_02，并检查相应的输出
+ 命令提示符有变化?
+ 如何 Switch to p1 分区
+ 有独立的 MAC 表, ARP 表, 路由表?
```
!
active-partition p1
!
show mac
!
show arp
!
show ip route

```

#### 连接到 vADC521_02 GUI 界面 (https://10.240.70.22)
+ 检查 Partition 状态
  + 鼠标移到右上角 Partition 图标
+ 点击 p1
  + 支持相同的 SLB, GSLB 功能吗?


## aVCS
+ 2人1组 
#### 备份
#### 连接到 vADC521_01 & vADC521_02
  + 先备份 vADC521_01
    + 点击 Systems > Maintenance > Backup > System
  + 先备份 vADC521_02
    + 点击 Systems > Maintenance > Backup > System

#### 粘贴以下命令到 vADC521_01
+ 命令提示符有变化?
+ 检查 show vcs summary
```
!
clear admin session all
!
configure terminal
!
no ip slb virtual-server vip2
!
no ip nat pool snat2
!
vcs enable
!
vcs floating-ip 10.240.70.32 /24
!
vcs device 1
  interfaces ve 10
  priority 240
  enable
  vcs reload
!
yes
!

```

#### 粘贴以下命令到 vADC521_02
+ 命令提示符有变化?
+ 检查 show vcs summary
```
!
clear admin session all
!
configure terminal
!
no slb virtual-server vip2
!
no ip nat pool snat2
!
vcs enable
!
vcs floating-ip 10.240.70.32 /24
!
vcs device 2
  interfaces ve 10
  priority 230
  enable
  vcs reload
!
yes
!

```

#### 使用 VCS Floating IP 连接到 GUI 界面 (https://10.240.70.32)
+ 检查 VCS
  + 鼠标移到右上角 VCS 图标
+ 选择 Device
  + 鼠标移到右上角 Device 图标

#### 如有需要，使用以下命令删除旧密钥
+ ssh-keygen -R 10.240.70.22
+ ssh-keygen -R 10.240.70.32

#### 使用 VCS Floating IP 连接到 CLI 界面 (SSH:10.240.70.32)
+ 检查 show vcs summary
+ 检查 show run 
+ 创建 partiton p2
  + 需要配置同步吗?
+ 检查 show vrrp-a all
  + p2 没有 Standby, 正常吗?
  + 为什么？

#### 单一 VCS Floating IP 可以控制 2 个设备吗?
+ 尝试禁用 device 1, interface 2
  + 做到吗?
+ 尝试禁用 device 2, interface 2
  + 做到吗?
+ 检查 show vrrp-a all
  + Device 1, Priority 有改变吗?
  + Device 2, Priority 有改变吗?

#### 备份 VCS setting
  + 备份
    + 点击 Systems > Maintenance > Backup > System

#### VCS Takeover
+ SSH to VCS vBlade
  + 检查 show vcs summary
  + 粘贴以下命令到 VCS vblade
  + 检查 show vcs summary
```
!
vcs vcs-take-over 250

```

#### VCS Reboot
记一下
  + Reboot all
  + Reboot device
  + Reboot
```
vADC521_02-vMaster[1/2](NOLICENSE)#reboot ?
  all                 Reboot all devices when VCS is enabled, or only this device itself if VCS is not
                      enabled
  at                  Reboot at a Specific time/date
  cancel              Cancel Pending Reboot
  device              Reboot a specific device when VCS is enabled
  in                  Reboot after a time interval
  NAME<length:1-127>  Reason for Reboot
  <cr>
```

#### VCS Reload
记一下
  + Reload all
  + Reload device
  + Reboot vs Reload 之间的差异?
```
vADC521_01-vMaster[1/1](NOLICENSE)#reload ?
  all     Reload all devices when VCS is enabled, or only this device itself if VCS is not enabled
  device  Reload a specific device when VCS is enabled
  <cr>
```

## 保存配置并休息一下
#### 粘贴以下命令到 vADC521_01
```
!
write memory lab12
y
!
show startup-config

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)