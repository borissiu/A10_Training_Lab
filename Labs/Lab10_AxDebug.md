![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 10 - 抓包 (AxDebug)
 - Packet Capture Filter
 - Packet Capture Save

#### Packet Capture Filter
#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
show axdebug filter
!
axdebug
!
filter 1
ip 192.168.2.21 /32
!
filter 2
ip 192.168.2.23 /32
!
show axdebug filter

```

#### Packet Capture Save
```

```

#### 收集 AxDebug Files
#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
  + 点击 Systems > Diagnostics > Show AxDebug Files

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)