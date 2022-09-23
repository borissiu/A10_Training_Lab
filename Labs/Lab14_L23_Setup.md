![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

#### 粘贴以下命令到 vADC521_01
```
write memory lab14
y
!
show startup-config all

```

## Layer 2 & 3 Setup
+ 像 Cisco CLI
  + 尝试 show interface
  + 尝试 show arp, show mac, show vlan... etc.
  + 尝试 show ip route

#### Non VCS 配置
```
vlan 10
  untagged ethernet 1
  router-interface ve 10
!
vlan 20
  untagged ethernet 2
  router-interface ve 20
!
interface management
  ip address 10.10.10.21 255.255.255.0
  ip default-gateway 10.10.10.1
!
interface ethernet 1
  enable
!
interface ethernet 2
  enable
!
interface ethernet 3
!
interface ethernet 4
!
interface ve 10
  name Data
  ip address 192.168.2.21 255.255.255.0
!
interface ve 20
  name Heartbeat
  ip address 192.168.1.21 255.255.255.0
!
ip route 0.0.0.0 /0 192.168.2.1

```

#### VCS 配置
```
vlan 1/10
  untagged ethernet 1
  router-interface ve 10
!
vlan 1/20
  untagged ethernet 2
  router-interface ve 20
!
vlan 2/10
  untagged ethernet 1
  router-interface ve 10
!
vlan 2/20
  untagged ethernet 2
  router-interface ve 20
!
interface ethernet 1/1
  enable
!
interface ethernet 1/2
  enable
!
interface ethernet 1/3
  enable
!
interface ethernet 1/4
  enable
!
interface ethernet 2/1
  enable
!
interface ethernet 2/2
  enable
!
interface ethernet 2/3
  enable
!
interface ethernet 2/4
  enable
!
interface ve 1/10
  ip address 192.168.2.21 255.255.255.0
!
interface ve 1/20
  name Heartbeat
  ip address 192.168.1.21 255.255.255.0
!
interface ve 2/10
  ip address 192.168.2.22 255.255.255.0
!
interface ve 2/20
  name Heartbeat
  ip address 192.168.1.22 255.255.255.0
!
device-context 1
  ip route 0.0.0.0 /0 192.168.2.1
!
device-context 2
  ip route 0.0.0.0 /0 192.168.2.1
```

## 保存配置并休息一下
#### 粘贴以下命令到 vADC521_01
```
!
write memory lab14
y
!
show startup-config

```


[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)