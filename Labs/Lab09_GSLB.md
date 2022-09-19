![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 9 - GSLB
- GSLB 配置
- Geo Location DB

#### 将以下配置粘贴到 vADC521_01
```
!
configure terminal
!
gslb dns logging both
!
gslb protocol enable controller
!
gslb protocol enable device
!
gslb service-ip 1.2.3.4 1.2.3.4
  health-check-protocol-disable
  health-check-disable
!
gslb service-ip 2.2.3.4 2.2.3.4
  health-check-protocol-disable
  health-check-disable
!
gslb service-ip 3.2.3.4 3.2.3.4
  health-check-protocol-disable
  health-check-disable
!
gslb service-ip 4.2.3.4 4.2.3.4
  health-check-protocol-disable
  health-check-disable
!
gslb service-ip vs80 192.168.226.80
  external-ip 111.111.111.80
!
exit
!
gslb site primary
  ip-server 1.2.3.4
  ip-server 2.2.3.4
  slb-dev 127.0.0.1 127.0.0.1
    vip-server vs80
!
gslb site secondary
  ip-server 3.2.3.4
  ip-server 4.2.3.4
!
gslb policy lab
  dns server
!
gslb zone a10.com
  policy lab
  service 0 vpn
    dns-a-record 1.2.3.4 static
    dns-a-record 3.2.3.4 static
  service 0 ftp
    dns-a-record 2.2.3.4 static
  service 0 www
    dns-a-record vs80 static
exit
exit
!
slb virtual vs80
  port 53 dns-udp
    gslb-enable
  port 53 dns-tcp
    gslb-enable
end

```

#### 粘贴以下命令到 客户端，并检查相应的输出
```
for i in {1..4}; do dig +short @192.168.226.80 vpn.a10.com; sleep 1; done

for i in {1..4}; do dig +short @192.168.226.80 ftp.a10.com; sleep 1; done

dig +short @192.168.226.80 www.a10.com
```
#### 将以下配置粘贴到 vADC521_01
```
!
show run gslb
!
show gslb service-ip
!
show log
!
```




#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
write memory
!
show run slb

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)