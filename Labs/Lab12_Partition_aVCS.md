![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)


[Lab. 11 - 高可用性]
 - Active/Standby
 - Active/Active
 - N+M (Active/Standby/Standby)
 - 配置同步

[Lab. 12 - 分区 (Partitions) 和 虚拟机箱 (aVCS)]
 - Shared Partition
 - L3V Partition
 - aVCS

## Lab. 10 - 抓包 (AxDebug)
 - Packet Capture Filter
 - Packet Capture Save

#### 粘贴以下命令到 vADC521_01
```
write memory lab10
y
!
show startup-config all

```

#### Packet Capture Filter
#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
show axdebug filter
!
axdebug
!
filter 1
ip 192.168.2.101 /32
dst port 80
exit
!
filter 2
ip 192.168.2.102 /32
dst port 443
exit
!
show axdebug filter

```

#### Start Packet Capture
```
!
capture brief

```

#### 粘贴以下命令到 客户端，并检查相应的输出
+ 背一下
  + Entries within a filter == AND ?
  + Entries between filters == OR ?
  + Support upto 5 filters?
  + Capture 3000 packets by default?
  + Capture 5 mins by default?
```
curl --interface 192.168.2.99 http://192.168.2.31
curl --interface 192.168.2.99 -k https://192.168.2.31

curl --interface 192.168.2.99 http://192.168.2.31
curl --interface 192.168.2.99 -k https://192.168.2.31

```

#### Packet Capture Save
#### 粘贴以下命令到 客户端，并检查相应的输出
```
!
axdebug
!
capture detail save axdebug_1
!

```

#### 粘贴以下命令到 客户端，并检查相应的输出
```
curl --interface 192.168.2.99 http://192.168.2.31
curl --interface 192.168.2.99 -k https://192.168.2.31

```

#### 粘贴以下命令到 客户端，并检查相应的输出
```
enter <ctrl c> to exit AxDebug

```

```
!
exit
!
show axdebug file
!
export axdebug axdebug_1 ?
!

```

#### AxDebug via GUI
#### 连接到 vADC521_01 GUI 界面 (https://192.168.2.21)
  + 收集 AxDebug Files
    + 点击 Systems > Diagnostics > Show AxDebug Files
    + 尝试使用 WireShark 打开 merge.* 文件
  + 修改 AxDebug Filter
    + 点击 Systems > Diagnostics > AxDebug Filter
  + 修改 AxDebug Capture
    + 执行 Systems > Diagnostics > AxDebug Capture

#### 去除 AxDebug Filter
#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
show axdebug filter
!
axdebug
!
no filter 1
!
no filter 2
!
exit
!
show axdebug filter

```

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
write memory lab10
y
!
show startup-config

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)