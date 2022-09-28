![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

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
#### 粘贴以下命令到 vADC521_01
```
!
show axdebug filter
!
axdebug
!
filter 1
ip 10.240.70.242 /32
dst port 80
exit
!
filter 2
ip 10.240.70.243 /32
dst port 443
exit
!
show axdebug filter

```

#### Start Packet Capture
#### 粘贴以下命令到 vADC521_01，并检查相应的输出
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
curl --interface 10.240.70.241 http://10.240.70.31
curl --interface 10.240.70.241 -k https://10.240.70.31

curl --interface 10.240.70.242 http://10.240.70.31
curl --interface 10.240.70.242 -k https://10.240.70.31

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

#### 粘贴以下命令到 客户端
```
curl --interface 10.240.70.242 http://10.240.70.31
curl --interface 10.240.70.242 -k https://10.240.70.31

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
#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
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

## 保存配置并休息一下
#### 粘贴以下命令到 vADC521_01
```
!
write memory lab10
y
!
show startup-config

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)