![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 5 - Load Balancing Persistence
  + Priority
  + Source IP Persist
  + Cookie Persist

## Priority
#### 将以下配置粘贴到 vADC521_01
```
!
clear slb all
!
repeat 2 show slb server | include Service\|web

```

#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
  + 修改 Server: web23
    + Advanced Fields
      + Weight: 3
  + 修改 Server: web24
    + Advanced Fields
      + Weight: 2
  + 修改 Service Group: sg-http-tcp80
    + Algorithm: Round Robin
    + Member: web24
      + priority: 10

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
  + {web23, weight=3} and {web24, weight=2}, 为什么所有连接接到 web24?
```
for i in {1..10}; do curl -k https://192.168.226.80; sleep 1; done

```

## Source IP Persist
#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
  + 创建 Persistence Template
    + 点击 ADC > Templates > Persistence
      + 点击 Create "Persist Source IP"
        + Name: SourceIP
  + 绑定 Persistence Template 到 vs80:443
    + 点击 ADC > Virtual Servers
      + 修改 vs80, port 443
      + Persist Type: Source IP 打上钩
        + Template Persist Source IP: 选择 SourceIP
  + 保存配置
    + 点击 "Save"  

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
```
for i in {1..10}; do curl -k https://192.168.226.80; sleep 1; done

```

#### 将以下配置粘贴到 vADC521_01
```
!
show session persist

```


## Cookie Persist




#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
write memory
!
show run slb

```
.