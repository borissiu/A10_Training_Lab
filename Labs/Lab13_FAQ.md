![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 13 - 常见问题 (FAQ)
 - 备份和恢复
 - 软件升级
 - 收集 show techsupport
 - 收集 backup logs

#### 粘贴以下命令到 vADC521_01
```
write memory lab13
y
!
show startup-config all

```

#### 备份和恢复
#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
  + 备份
    + 点击 Systems > Maintenance > Backup > System
  + 恢复 
    + 点击 Systems > Maintenance > Backup > Restore

#### 软件升级
#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
  + 软件升级
    + 点击 Systems > Maintenance > Upgrade
      + Destination: 选择 primary 或 secondary，取决于升级策略
      + Reboot After Upgrade: 选择 enable 或 disble，取决于升级策略

#### System Logs
```
!
show log
!
show log | include user

```

```
ACOS-Active#show log | ?
  begin    Begin with the line that matches
  include  Include lines that match
  exclude  Exclude lines that match
  section  Filter a section of output
  grep     Print lines matching a pattern
  awk      Print selected fields
  cut      Remove sections from each line
  sort     Sort lines
  uniq     Report or omit repeated lines
ACOS-Active#
```

#### Audit Logs
```
!
show audit

```

```
ACOS-Active#show audit | ?
  begin    Begin with the line that matches
  include  Include lines that match
  exclude  Exclude lines that match
  section  Filter a section of output
  
```

#### Config Check
```
!
show run slb
!
show run ip nat
!
show run gslb
!
show run health
!
show run template
!
show run vlan
!
show run interface
!
show run enable-management

```

#### Server Status Check (slb, gslb)
```
!
show slb server
!
show slb service-group
!
show slb virtual-server
!
show gslb service-ip
!
show gslb service-port
!

```

#### HTTP Check
```
!
show slb http-proxy
!
show slb http-proxy vs80 80
!
show slb http-proxy vs80 443
!
show slb http-proxy detail

```

#### HTTPs Check
```
!
show slb ssl-counters
!
show slb ssl stats
!
show slb ssl error
```


#### General Check (slb performance, session, version, uptime, interface, vlan, MAC, ARP, route table, vrrp-a, processes ...etc)
```
!
show slb performance
!
show session
!
show version 
!
show interface brief
!
show interface ethernet
!
show interface ve
!
show interface trunk
!
show interface media
!
show interface statistics
!
show vlan
!
show vlan counters
!
show mac
!
show arp
!
show ip route
!
show vrrp-a all
!
show process system detail

```

#### Hardware Check (CPU, Memory, Fan, Disk, PS, SSL Card)
```
!
show cpu
!
show cpu history
!
Show memory
!
Show disk
!
show environment
!
show hardware

```

#### 显示 SSH/GUI/AxAPI 信息
```
show admin session
clear admin session all
```

#### 显示 bootimage & startup-config 信息
```
show bootimage
show startup-config all
show startup-config
show startup-config all-partition
```

#### 显示 snmp 信息
```
show snmp stats
```

## Check Connectivity
```
show run ip dns
ping 114.114.114.114
ping 114.114.114.114 use-mgmt-port
ping www.baidu.com
```

## Check Mgmt setting
```
show run enable-management
```

## Check Access Control List
```
show run access-list
show access-list
```

## Check Clock
```
show run ntp
show ntp status
show clock
show timezone
```


#### 收集 show techsupport
![收集 show techsupport](/Images/ShowTech.png)

#### 收集 backup logs
![收集 backup logs](/Images/BackupLog.png)


## 保存配置并休息一下
#### 粘贴以下命令到 vADC521_01
```
!
write memory lab13
y
!
show startup-config

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)