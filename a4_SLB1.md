![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)


SLB配置1
 - UDP Load Balancing
 - TCP Load Balancing


#### 将以下配置复制并粘贴到 vADC521_01
```
configure terminal
!
slb server dns1 114.114.114.114
  port 53 udp
slb server dns5 223.5.5.5
  port 53 udp
slb server dns6 223.6.6.6
  port 53 udp
!
slb service-group sg-dns-udp53 udp
  member dns1 53
  member dns5 53
  member dns6 56
!
slb virtual-server vs53 192.168.226.53
  port 53 udp
  source-nat auto
  service-group sg-dns-udp53
!
end
write memory
!
```

#### 粘贴以下命令到 vADC521_01，并检查相应的输出：
```
show slb server
!
show slb service-group
!
show slb virtual-server
!
```

#### 粘贴以下命令到 客户端，并检查相应的输出：
```
for i in {1..9}; do dig +short @192.168.226.53 www.a10networks.com; done

```
