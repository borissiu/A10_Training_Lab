![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 4 - Load Balancing Algorithm
+ Round Robin (Round Robin on SERVER level)
+ Lease Connection (Lease Connection on SERVER level)
+ Service Least Connection (Lease Connection SERVICE PORT level)
+ Weighted Round Robin
+ Weighted Lease Connection
+ Service Weighted Round Robin
+ Service Weighted Least Connection

 - Priority

## Load Balancing Algorithm
#### 将以下配置粘贴到 vADC521_01
```
!
clear slb all
!
repeat 1 show slb server | include Service\|53/\tcp

```

#### 粘贴以下命令到 客户端
+ 并检查 vADC521_01 相应的输出
```
for i in {1..10}; do dig +tcp +short @192.168.226.53 www.a10networks.com; sleep 1; done

```

#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
+ 修改 Service Group: sg-dns-udp53
  + Algorithm: Least Connection
    
#### 粘贴以下命令到 客户端
+ 并检查 vADC521_01 相应的输出
```
for i in {1..10}; do dig +tcp +short @192.168.226.53 www.a10networks.com; sleep 1; done

```

#### 打开另一个 SSH 连接
#### 粘贴以下命令到 客户端，并检查相应的输出
```
telnet 192.168.226.53 53

```

#### 粘贴以下命令到 客户端
+ 并检查 vADC521_01 相应的输出
```
for i in {1..10}; do dig +tcp +short @192.168.226.53 www.a10networks.com; sleep 1; done

```








#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
show slb ssl-counters
!
show sessions

```

## HTTP Header 源地址插入
#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
+ 由于没有 License，GUI 速度会有点慢
+ 创建 HTTP Template
  + 点击 ADC > Templates > L7 Protocols > 
  + 点击 Create HTTP
    + Client IP Header Insert: 打上钩
    + Header Name: X-Forwarded-For
+ 绑定 HTTP Template 到 vs80:80
  + 点击 ADC > Virtual Servers
    + 修改 vs80, port 80
    + 添加 Template HTTP
      + 选择 http
+ 保存配置
  + 点击 "Save"  
    
#### 粘贴以下命令到 客户端，并检查相应的输出
```
sudo tcpdump -i $(ip addr | egrep -i "192.168.226.21" | awk '{print $NF}') -nn -A port 80 | egrep -i "X-Forwarded-For"

```

#### 打开另一个 SSH 连接
#### 粘贴以下命令到 客户端，并检查相应的输出
```
for i in {1..3}; do curl --interface 192.168.226.21 http://192.168.226.80; done

for i in {1..3}; do curl --interface 192.168.226.22 http://192.168.226.80; done
```

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
write memory
!
show run slb

```
