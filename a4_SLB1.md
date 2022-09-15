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
slb virtual-server vs53 192.168.247.53
  port 53 udp
  source-nat auto
  service-group sg-dns-udp53
!
end
write memory
!
```

#### 将以下配置复制并粘贴到 vADC521_01
```
show slb server
show slb service-group
show slb virtual-server
!
```