![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 12 - 分区 (Partitions) 和 虚拟机箱 (aVCS)]
 - Shared & L3V Partition (Layer 3 虚拟分区)
 - aVCS

#### 粘贴以下命令到 vADC521_01
```
write memory lab12
y
!
show startup-config all

```

#### Shared & L3V Partition
#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
configure terminal
!
partition p1 id 1 application-type adc
!
active p1
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
  ip address 192.168.2.21 255.255.255.0
!
interface ve 40
  name Heartbeat
  ip address 192.168.1.21 255.255.255.0
!
end

```

#### 粘贴以下命令到 vADC521_02，并检查相应的输出
```
!
configure terminal
!
partition p1 id 1 application-type adc
!
active p1
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
  ip address 192.168.2.22 255.255.255.0
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
  + 不适用于一般情况
  + 适用于对于多租户 (不同的租户可能使用相同的Private IP)
```
!
ping 192.168.1.21
!
ping 192.168.2.21
!
show vrrp-a

```

#### 粘贴以下命令到 vADC521_02，并检查相应的输出
+ 命令提示符有变化?
+ Switch back to shared 分区
+ Switch to p1 分区
```
!
active-partition shared
!
active-partition p1

```

#### 粘贴以下命令到 vADC521_01, vADC521_02
```
!
write memory lab12
y
!
show startup-config

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)