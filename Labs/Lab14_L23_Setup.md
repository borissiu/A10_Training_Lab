![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

#### 粘贴以下命令到 vADC521_01
```
write memory lab14
y
!
show startup-config all

```

## Layer 2 & 3 Setup
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

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
write memory lab14
y
!
show startup-config

```


[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)