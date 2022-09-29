![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 4 - 负载均衡算法 (Load Balancing Algorithm)
+ Round Robin (Round Robin on SERVER level)
+ Lease Connection (Lease CURRENT Connection on SERVER level)
+ Service Least Connection (Lease Connection SERVICE PORT level)
+ Weighted Round Robin
+ Weighted Lease Connection
+ Service Weighted Round Robin
+ Service Weighted Least Connection

#### 粘贴以下命令到 vADC521_01
```
write memory lab04
y
!
show startup-config all

```

## Load Balancing Algorithm
#### 将以下配置粘贴到 vADC521_01
```
!
configure terminal
!
slb service-group sg-ssh-tcp22 tcp
  member web101 22
  member web102 22
!
slb virtual-server vip 10.240.70.31
  port 22 tcp
    source-nat pool snat
    service-group sg-ssh-tcp22
!
end
!
clear slb all
!
repeat 2 show slb server | include Service\|web

```

## Algorithm: Round Robin
#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
  + 修改 Service Group: sg-http-tcp80
    + Algorithm: Round Robin

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
```
for i in {1..10}; do curl http://10.240.70.31; sleep 1; done

```


## Algorithm: Least Connection (Lease CURRENT Connection on SERVER level)
#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
  + 修改 Service Group: sg-http-tcp80
    + Algorithm: Least Connection
    
#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
```
for i in {1..10}; do curl http://10.240.70.31; sleep 1; done

```

#### 打开另一个 SSH 客户端连接
#### 粘贴以下命令到 客户端
```
ssh 10.240.70.31

```

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
  + 为什么结果不一样?
```
for i in {1..10}; do curl http://10.240.70.31; sleep 1; done

```

## Algorithm: Service Least Connection
#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
  + 修改 Service Group: sg-http-tcp80
    + Algorithm: Service Least Connection

#### 执行以下命令
  + 客户端: "Ctrl+C" 取消 ssh 10.240.70.31 连接
  + vADC521_01: clear slb all
  + vADC521_01: repeat 2 show slb server | include Service/|web

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
```bash
for i in {1..10}; do curl http://10.240.70.31; sleep 1; done

```

#### 打开另一个 SSH 客户端连接
#### 粘贴以下命令到 客户端
```
ssh 10.240.70.31

```

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
  + 为什么结果不一样?
```
for i in {1..10}; do curl http://10.240.70.31; sleep 1; done

```

## Algorithm: Weighted Round Robin
#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
  + 修改 Service Group: sg-http-tcp80
    + Algorithm: Weighted Round Robin
  + 修改 Server: web102
    + Advanced Fields
      + Weight: 2

#### 执行以下命令
  + 客户端: "Ctrl+C" 取消 ssh 10.240.70.31 连接
  + vADC521_01: clear slb all
  + vADC521_01: repeat 2 show slb server | include Service\\\|web

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
```
for i in {1..9}; do curl http://10.240.70.31; sleep 1; done

```

## Algorithm: Weighted Least Connection
#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
  + 修改 Service Group: sg-http-tcp80
    + Algorithm: Weighted Least Connection
  + 修改 Server: web102
    + Advanced Fields
      + Weight: 2
    + 点击 "Update"

#### 执行以下命令
  + vADC521_01: clear slb all
  + vADC521_01: repeat 2 show slb server | include Service\\\|web

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
  + 为什么结果不一样?
```
for i in {1..10}; do curl http://10.240.70.31; sleep 1; done

```

## 如感兴趣，可以自己试试
+ Algorithm:
  + Service Weighted Round Robin
  + Service Weighted Least Connection

#### 记一下
+ 确保清楚理解
+ 多练习
+ 考很多题

## 保存配置并休息一下
#### 粘贴以下命令到 vADC521_01
```
!
write memory lab04
y
!
show startup-config all

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)