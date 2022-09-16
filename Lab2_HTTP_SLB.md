![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 2 - DNS & HTTP SLB
 + DNS Load Balancing
 + HTTP Load Balancing
 + HTTPs Load Balancing (SSL卸载)
 + HTTP Header 源地址插入
 + URL Switching

## DNS Load Balancing
#### 将以下配置粘贴到 vADC521_01
```
configure terminal
!
ip nat pool snat200 192.168.226.200 192.168.226.203 netmask /24
!
slb virtual-server vs80 192.168.226.80
  port 53 dns-tcp
    source-nat pool snat200
    service-group sg-dns-tcp53
  port 53 dns-udp
    source-nat pool snat200
    service-group sg-dns-udp53
!
end
write memory
!
clear slb all

```

#### 粘贴以下命令到 客户端，并检查相应的输出
+ HTTP 响应 404 Not Found？
```
for i in {1..100000}; do dig +short @192.168.226.80/xxx www.a10networks.com; done

```

#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
+ 由于没有 License，GUI 速度会有点慢
+ 点击 Dashboard > ADC
  + Total Throughput 有多少?
  + Global System Throughput 有多少?
  + L4 Conn/sec 有多少?
  + L7 Conn/sec 有多少?
  + SSL Conn/sec 有多少?
  + Total New Conns/sec 有多少?
  + 点击右上角 "?"
    + 查看 Total Throughput 是什么?
    + 查看 Global System Throughput 是什么?
+ 点击 ADC > Statistics > System
    + 查看 L4 Bandwidth (Byte/sec)?
    + 查看 L7 Bandwidth (Byte/sec)?
    + 查看 Total Throughput (Bit/sec)?
+ 点击 ADC > Statistics > L4
  + 查看 (a) TCP SYN per sec?
  + 查看 (b) TCP SYN received?
  + 查看 (c) L4 TCP Established?
  + 为什么 c = b x 2？
+ 点击 ADC > Statistics > L7 > HTTP Proxy
  + 选择
    + Virtual Server: vs80
    + HTTP Ports: 80
  + 查看 Request GET (Req GET)?
  + 查看 Response Status Code 404?
  + 查看 Response Time (Req xxx)?
  + 查看 Response Size (Rsp Sz xxx)?


#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
show cpu
!
show cpu history

```

```
!
show slb performance
!
show slb http-proxy

```

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
+ 有任何命令可以排查 source-nat auto？
  + 建议使用 ip nat pool
```
!
show ip nat ?

```

## HTTP Load Balancing
#### 将以下配置粘贴到 vADC521_01
```
configure terminal
!
ip nat pool snat200 192.168.226.200 192.168.226.203 netmask /24
!
slb server web23 192.168.226.23
  port 80 tcp
slb server web24 192.168.226.24
  port 80 tcp
!
slb service-group sg-http-tcp80 tcp
  member web23 80
  member web24 80

slb virtual-server vs80 192.168.226.80
  port 80 http
    source-nat pool snat200
    service-group sg-http-tcp80
!
end
write memory
!
clear slb all

```

#### 粘贴以下命令到 客户端，并检查相应的输出
```
for i in {1..6}; do curl http://192.168.226.80; done

```



#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
+ 由于没有 License，GUI 速度会有点慢
+ 点击 ADC > SLB > Virtual Servers
  + 有多少 Total Connection？
  + 有多少 Current Connection？
+ 点击 ADC > SLB > Service Groups
  + 默认 负载均衡算法是什么？
+ 点击 ADC > SLB > Server
  + 默认 健康检查是什么？
 
#### 使用 GUI 界面
+ 禁用 dns1
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
+ 替 vs53 添加 Virtual Port
  + Protocol: TCP
  + Port: 53
  + Source NAT Auto: Enable
  + Service Group: sg-dns-tcp53
+ 点击 Dashboard > Services Map
  + 替 vs53 打上钩 
+ 保存配置
  + 点击 "Save"  

#### 粘贴以下命令到 客户端，并检查相应的输出
+ 有多少 dns 响应？
```
for i in {1..10}; do dig +short +tcp @192.168.226.53 www.utstar.com; done

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
show run slb

```

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
