![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 2 - DNS & HTTP SLB
 + DNS Load Balancing
 + HTTP Load Balancing

#### 粘贴以下命令到 vADC521_01
```
write memory lab02
y
!
show startup-config all

```

## DNS Load Balancing
#### 将以下配置粘贴到 vADC521_01
```
configure terminal
!
ip nat pool snat 192.168.2.161 192.168.2.161 netmask /24
!
ip nat pool demo 172.16.0.11 172.16.0.15 netmask /24
!
no slb virtual-server vip
!
slb virtual-server vip 192.168.2.31
  port 53 dns-tcp
    source-nat pool snat
    service-group sg-dns-tcp53
  port 53 dns-udp
    source-nat pool snat
    service-group sg-dns-udp53
!
end
!

```

#### 粘贴以下命令到 客户端，并检查相应的输出
+ 有 dns 响应？
```
for i in {1..8000}; do dig +short @192.168.2.31 www.a10networks.com; done

```

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
+ 哪个 ip 发送DNS查询?
+ dns 查询发送到哪个vip?
+ dns 查询发送到哪个服务器？
+ dns 转发使用什么 IP NAT Pool 地址?
+ dns 服务器收到的 dns 查询, 源 IP 是什么?
```
!
show ip nat pool statistics
!
show ip nat pool statistics
!
show session | include Udp
!
!

```

#### 将以下配置粘贴到 vADC521_01，并检查相应的输出
+ dns 转发使用什么 IP NAT Pool 地址?
```
configure terminal
!
slb virtual-server vip 192.168.2.31
  port 53 dns-tcp
    source-nat auto
    service-group sg-dns-tcp53
  port 53 dns-udp
    source-nat auto
    service-group sg-dns-udp53
!
end
!
show run slb virtual-server

```

#### 将以下配置粘贴到 vADC521_01，并检查相应的输出
+ dns 转发使用什么 IP NAT Pool 地址?
```
configure terminal
!
slb virtual-server vip 192.168.2.31
  port 53 dns-udp
    source-nat auto precedence
    service-group sg-dns-udp53
!
end
!
show run slb virtual-server

```

#### 将以下配置粘贴到 vADC521_01，并检查相应的输出
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
!!
slb server web101 192.168.2.101
  port 80 tcp
slb server web102 192.168.2.102
  port 80 tcp
!
slb service-group sg-http-tcp80 tcp
  member web101 80
  member web102 80

slb virtual-server vip 192.168.2.31
  port 80 http
    source-nat pool snat
    service-group sg-http-tcp80
!
end
!
show run slb virtual-server

```

#### 粘贴以下命令到 客户端，并检查相应的输出
+ HTTP 响应 404 Not Found？
```
for i in {1..100000}; do curl http://192.168.2.31/xxx; done

```

#### 连接到 vADC521_01 GUI 界面 (https://192.168.2.21)
+ 点击 Dashboard > ADC
  + Total Throughput 有多少?
  + Global System Throughput 有多少?
  + L4 Conn/sec 有多少?
  + L7 Conn/sec 有多少?
  + SSL Conn/sec 有多少?
  + Total Current Conns 有多少?
  + Total New Conns/sec 有多少?
  + 点击右上角 "?" (记一下)
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
    + Virtual Server: vip
    + HTTP Ports: 80
  + 查看 Request GET (Req GET)?
  + 查看 Response Status Code 404?
  + 查看 Response Time (Req xxx)?
  + 查看 Response Size (Rsp Sz xxx)?
  + 点击右上角 "?"

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
show cpu

```

```
!
show cpu history

```

```
!
show slb performance

```

```
!
show slb http-proxy

```

#### 记一下
  + 建议使用 ip nat pool
  + show slb performance
  + show slb http-proxy

## 保存配置并休息一下
#### 粘贴以下命令到 vADC521_01
```
!
write memory lab03
y
!
show startup-config all

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)