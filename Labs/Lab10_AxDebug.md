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
dst port 80
exit
!
filter 2
ip 192.168.2.22 /32
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
  + Entries within a filter == AND ?
  + Entries between filters == OR ?
  + Support upto 5 filters?
  + Capture 3000 packets by default?
  + Capture 5 mins by default?
```
curl --interface 192.168.226.21 http://192.168.226.80
curl --interface 192.168.226.21 -k https://192.168.226.80

curl --interface 192.168.226.22 http://192.168.226.80
curl --interface 192.168.226.22 -k https://192.168.226.80

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
curl --interface 192.168.226.21 http://192.168.226.80
curl --interface 192.168.226.22 -k https://192.168.226.80

```

#### 粘贴以下命令到 客户端，并检查相应的输出
```
!
axdebug
!
show axdebug file
!
exit
!
export axdebug axdebug_1 ?
!

```

#### 收集 AxDebug Files
#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
  + 点击 Systems > Diagnostics > Show AxDebug Files

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)