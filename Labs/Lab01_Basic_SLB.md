![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 1 - Basic SLB
 + UDP Load Balancing
 + TCP Load Balancing

#### 粘贴以下命令到 vADC521_01
+ SSH : 10.240.70.21
+ username=admin, password=a10
```
write memory lab00
y
!
write memory lab01
y
!
show startup-config all

```

## UDP Load Balancing
#### 将以下配置粘贴到 vADC521_01
```
configure terminal
!
slb server dns5 172.18.9.35
  port 53 udp
slb server dns6 172.18.9.36
  port 53 udp
slb server dns8 8.8.8.8
  port 53 udp
!
slb service-group sg-dns-udp53 udp
  member dns5 53
  member dns6 53
  member dns8 53
!
slb virtual-server vip 10.240.70.31
  port 53 udp
    source-nat auto
    service-group sg-dns-udp53
!
end
!
write memory lab01
y

```

#### 粘贴以下命令到 客户端，并检查相应的输出
+ SSH : 10.240.70.242
+ 有多少 dns 响应？
```
for i in {1..9}; do dig +short @10.240.70.31 www.a10networks.com; done

```

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
show slb server
!
show slb service-group
!
show slb virtual-server

```

#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
+ 由于没有 License，GUI 速度会有点慢
+ 点击 ADC > SLB > Virtual Servers
  + 有多少 Total Connection？
  + 有多少 Current Connection？
+ 点击 ADC > SLB > Service Groups
  + 默认 负载均衡算法是什么？ (背一下)
+ 点击 ADC > SLB > Server
  + 默认 健康检查是什么？ (背一下, Default = Ping)
 
#### 使用 GUI 界面
+ 禁用 dns8
+ 点击 ADC > SLB > Service Groups
  + 有什么变化吗？
+ 点击 ADC > SLB > Virtual Servers
  + 有什么变化吗？

## TCP Load Balancing
#### 使用 GUI 界面
+ 替 dns5 添加 TCP 53 端口
+ 替 dns6 添加 TCP 53 端口
  + 创建 Service Group
    + 名称: sg-dns-tcp53
    + 负载均衡算: Least Connection
    + Member:
      + 添加 dns5, port 53
      + 添加 dns6, port 53
  + 替 Virtual Server "vip" 添加 Virtual Port
    + Protocol: TCP
    + Port: 53
    + Source NAT Auto: Enable
    + Service Group: sg-dns-tcp53
    + 点击 "Create"
    + 点击 "Update"
  + 点击 Dashboard > Services Map
    + 替 vip 打上钩 
  + 保存配置
    + 看看 "Save" 颜色 (右上角)  
    + 点击 "Save" 

#### 粘贴以下命令到 客户端，并检查相应的输出
+ 有多少 dns 响应？
```
for i in {1..10}; do dig +short +tcp @10.240.70.31 www.utstar.com; done

```

#### 使用 GUI 界面
+ 点击 ADC > SLB > Virtual Servers
  + 有多少 Total Connection？
  + 有多少 TCP Connection？
  + 有多少 UDP Connection？
+ 点击 ADC > SLB > Service Groups
  + 有多少个 Service Group？
+ 点击 ADC > SLB > Server
  + dns5 有多少 Total Connection？
  + dns5 有多少 TCP Connection？

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
show run slb server
!
show slb server

```

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
show run slb service-group
!
show slb service-group

```

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
show run slb virtual-server
!
show slb virtual-server

```

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
show run slb

```

#### 记一下
```
show slb server
show slb service-group
show slb virtual-server

```

## 保存配置并休息一下
#### 粘贴以下命令到 vADC521_01
```
!
write memory lab01
y
!
show startup-config all

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)