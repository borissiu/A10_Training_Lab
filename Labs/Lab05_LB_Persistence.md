![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 5 - Load Balancing Persistence
  + Priority
  + Source IP Persist
  + Cookie Persist

#### 粘贴以下命令到 vADC521_01
```
write memory lab05
y
!
show startup-config all

```

## Priority
#### 将以下配置粘贴到 vADC521_01
```
!
clear slb all
!
repeat 2 show slb server | include Service\|web

```

#### 连接到 vADC521_01 GUI 界面 (https://192.168.2.21)
  + 修改 Server: web101
    + Advanced Fields
      + Weight: 3
  + 修改 Server: web102
    + Advanced Fields
      + Weight: 2
  + 修改 Service Group: sg-http-tcp80
    + Algorithm: Weighted Round Robin
    + Member: web102
      + priority: 10

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
  + {web101, weight=3} and {web102, weight=2}, 为什么所有连接接到 web102?
```
for i in {1..10}; do curl -k https://192.168.2.31; sleep 1; done

```

## Source IP Persist
#### 连接到 vADC521_01 GUI 界面 (https://192.168.2.21)
  + 修改 Server: web101
    + Advanced Fields
      + Weight: 1
  + 修改 Server: web102
    + Advanced Fields
      + Weight: 1
  + 修改 Service Group: sg-http-tcp80
    + Member: web101
      + priority: 10
  + 创建 Persistence Template
    + 点击 ADC > Templates > Persistence
      + 点击 Create "Persist Source IP"
        + Name: source_ip_test
  + 绑定 Persistence Template 到 vip:443
    + 点击 ADC > SLB > Virtual Servers
      + 修改 vip, port 443
      + Persist Type: Source IP 打上钩
        + Template Persist Source IP: 选择 source_ip_test
        + 点击 "Update"
      + 点击 "Update"  
  + 保存配置
    + 点击 "Save"  

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
```
for i in {1..2}; do curl -k https://192.168.2.31; sleep 1; done

```

#### 将以下配置粘贴到 vADC521_01
  + Source IP Persist 默认时间是多少?
```
!
show session persist

```

#### 将以下配置粘贴到 vADC521_01
```
!
clear session persist
!
show session persist

```

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
```
for i in {1..2}; do curl -k https://192.168.2.31; sleep 1; done

```

#### 将以下配置粘贴到 vADC521_01
+ 注意到什么变化?
```
!
show session persist

```

## Cookie Persist
#### 连接到 vADC521_01 GUI 界面 (https://192.168.2.21)
  + 创建 Persistence Template
    + 点击 ADC > Templates > Persistence
      + 点击 Create "Persist Cookie"
        + Name: cookie_test
        + Expiration (Seconds): 600
        + Cookie Name: cookie_test
        + Enable HTTP Only Attribute: 打上钩
        + Enable Secure Attribute: 打上钩
        + Encrypt Level: 0
  + 绑定 Persistence Template 到 vip:443
    + 点击 ADC > SLB > Virtual Servers
      + 修改 vip, port 443
      + Persist Type: Cookie 打上钩
        + Template Persist Cookie: 选择 cookie_test
        + 点击 "Update"
  + 保存配置
    + 点击 "Save"  

#### 粘贴以下命令到 客户端
  + 检查 相应的输出
    + Cookie Name ?
    + Cookie Value ?
    + Expries = 600 seconds?
    + Secure ?
    + HTTPOnly ?
```
curl -I -k https://192.168.2.31

```


## 保存配置并休息一下
#### 粘贴以下命令到 vADC521_01
```
!
write memory lab05
y
!
show startup-config all

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)