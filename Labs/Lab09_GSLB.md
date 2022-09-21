![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 9 - GSLB
- GSLB 配置

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
gslb service-ip vip 192.168.2.31
!
exit
!
gslb site pri
  ip-server 1.2.3.4
  ip-server 2.2.3.4
  slb-dev vADC521_01 127.0.0.1
    vip-server vip
!
gslb site sec
  ip-server 3.2.3.4
  ip-server 4.2.3.4
!
gslb policy lab
  dns active-only
  dns server
!
gslb zone a10.com
  policy lab
  service 0 vpn
    dns-a-record 1.2.3.4 static
    dns-a-record 3.2.3.4 static
  service 0 ftp
    dns-a-record 2.2.3.4 static
  service 80 www
    dns-a-record vip static
exit
exit
!
slb virtual vip
  port 53 dns-udp
    gslb-enable
  port 53 dns-tcp
    gslb-enable
end

```

#### 粘贴以下命令到 客户端，并检查相应的输出
```
for i in {1..4}; do dig +short @192.168.2.31 vpn.a10.com; sleep 1; done

for i in {1..4}; do dig +short @192.168.2.31 ftp.a10.com; sleep 1; done

for i in {1..10000}; do dig +short @192.168.2.31 www.a10.com; sleep 1; done

```

#### 将以下配置粘贴到 vADC521_01
```
!
show run gslb
!
show gslb service-ip
!
show gslb service-port
!
show log
!
```

#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
+ disable web101
+ disable web102
  + 等待30秒
  + 检查 客户端 相应的输出
+ enable web101
+ enable web102
  + 等待30秒
  + 检查 客户端 相应的输出


#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
write memory lab09
y
!
show startup-config

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)