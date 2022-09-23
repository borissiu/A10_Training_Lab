![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)


## Lab. 11 - 高可用性
 - Active/Standby
 - Active/Active
 - N+M (Active/Standby/Standby)
 - 配置同步

#### 粘贴以下命令到 vADC521_01
```
write memory lab11
y
!
show startup-config all

```

#### 高可用性 (H.A.)
+ 2人1组

| | ADC01 | ADC02 | | ADC03 | ADC04 | | ADC05 | ADC06 | | ADC07 | ADC08 | | ADC09 | ADC10 |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| set-id | 1 | 1 | | 2 | 2 | | 3 | 3 | | 4 | 4 | | 5 | 5 |
| device-id | 1 | 2 | | 1 | 2 | | 1 | 2 | | 1 | 2 | | 1 | 2 |


#### 粘贴以下命令到 vADC521_01，并检查相应的输出
+ 命令提示符有变化?
+ show vrrp-a all-partitions
  + 注意 Device ID, Partition, Vrid, Active Priority, Standby Priority
```
!
configure terminal
!
vrrp-a common
  device-id 1
  set-id 1
  enable

```

#### 连接到 vADC521_02 GUI 界面 (https://192.168.2.22)
+ 检查 VRRP-A 状态
  + 鼠标移到右上角 VRRP-A 图标
+ 点击 Systems > VRRP-A
  + VRRP-A Enable: Enable
  + Device ID: 2
  + Set ID: 1
  + 点击 "OK"
+ 检查 VRRP-A 状态
  + 鼠标移到右上角 VRRP-A 图标

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
+ VRRP-A 用所有端口发心跳包?
+ VRRP-A 可以看到 Peer IP?

```
!
show vrrp-a detail

```




#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
write memory lab11
y
!
show startup-config

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)