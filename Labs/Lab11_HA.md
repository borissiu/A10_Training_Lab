![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)


## Lab. 11 - 高可用性
 - Active/Standby
 - Active/Active
 - 配置同步
 - Failover (自动/手动)
 - N+M (Active/Standby/Standby)

#### 粘贴以下命令到 vADC521_01
```
write memory lab11
y
!
show startup-config all

```

#### 高可用性 (H.A.)
+ 2人1组

| | ADC01 | ADC02 | | ADC03 | ADC04 | | ADC05 | ADC06 | | ADC07 | ADC08 | | ADC09 | ADC10 |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| set-id | 1 | 1 | | 2 | 2 | | 3 | 3 | | 4 | 4 | | 5 | 5 |
| device-id | 1 | 2 | | 1 | 2 | | 1 | 2 | | 1 | 2 | | 1 | 2 |


## Active/Standby
#### 粘贴以下命令到 vADC521_01，并检查相应的输出
+ 命令提示符有变化?
+ show vrrp-a all-partitions
  + 注意 Device ID, Partition, Vrid, Active Priority, Standby Priority
```
!
configure terminal
!
vrrp-a common
  device-id 1
  set-id 1
  enable

```

#### 连接到 vADC521_02 GUI 界面 (https://192.168.2.22)
+ 检查 VRRP-A 状态
  + 鼠标移到右上角 VRRP-A 图标
+ 点击 Systems > VRRP-A
  + VRRP-A Enable: Enable
  + Device ID: 2
  + Set ID: 1
  + 点击 "OK"
+ 检查 VRRP-A 状态
  + 鼠标移到右上角 VRRP-A 图标

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
+ VRRP-A 用所有端口发心跳包?
+ VRRP-A 可以看到 Peer IP?
+ Layer 2, Layer 3 配置有应否同步?
+ 检查 SLB, GSLB 配置有否同步?
  + show run slb
```
!
show vrrp-a detail

```

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
+ 检查 vADC521_01 日志?
  + show log
+ 检查 vADC521_02 日志?
  + show log
+ 检查 SLB, GSLB 配置有否同步?
  + show run slb
  + show run gslb
+ 检查 ip nat pool 配置有否同步?
  + show run ip nat
+ 检查 template, ssl cert, aflex...etc 配置有否同步?

```
!
configure terminal
!
configure sync all all-partitions auto-authentication 192.168.1.22

```

#### 粘贴以下命令到 客户端，并检查相应的输出
+ 检查 哪台 vADC 处理用户流量?
+ 检查 会话有否同步?
  + show session
  + 为什么?
```
for i in {1..300}; do curl --interface 192.168.2.100 -k https://192.168.2.31; sleep 1; done

```

## Active/Active
#### 粘贴以下命令到 vADC521_02，并检查相应的输出
+ 为什么需要在设备 2 上执行此操作?
```
!
configure terminal
!
vrrp-a vrid 1
  preempt-mode threshold 20
  blade-parameters
    priority 190
    tracking-options
      interface ethernet 1 priority-cost 30
      interface ethernet 2 priority-cost 30

```

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
+ 命令提示符有变化?
+ show vrrp-a all-partitions
  + 注意 Device ID, Partition, Vrid, Active Priority, Standby Priority
```
!
configure terminal
!
vrrp-a vrid 1
  preempt-mode threshold 20
  blade-parameters
    priority 180
    tracking-options
      interface ethernet 1 priority-cost 30
      interface ethernet 2 priority-cost 30
!
ip nat pool snat2 192.168.2.162 192.168.2.162 netmask /24 vrid 1
!
slb virtual-server vip2 192.168.2.32
  vrid 1
  port 443 https
    aflex log_curl_agent
    aflex log_tcp
    aflex reject_ip21_ip23
    source-nat pool snat2
    service-group sg-http-tcp80
    template connection-reuse connection_reuse_test
    template persist cookie cookie_test
    template http http_template_test
    template client-ssl www.a10.com

```

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
+ vrid 0 - 谁是 Active?
  + Active Priority 是多少?
+ vrid 1 - 谁是 Active?
  + Active Priority 是多少?
```
!
show vrrp-a all

```

#### 粘贴以下命令到 客户端，并检查相应的输出
+ 检查 哪台 vADC 处理用户流量?
  + 有什么问题吗?
    + 为什么?
  + 如何解决这个问题?
      + 自己试试?
```
for i in {1..900}; do curl --interface 192.168.2.100 -k https://192.168.2.32; sleep 1; done

```

## Failover (自动/手动)
#### 自动 Failover
#### 粘贴以下命令到 vADC521_02
+ curl 有影响吗?
```
!
configure terminal
!
interface ethernet 2
  disable

```

#### 粘贴以下命令到 vADC521_02，并检查相应的输出
+ vrid 0 - 谁是 Active?
  + Active Priority 是多少?
+ vrid 1 - 谁是 Active?
  + Active Priority 是多少?
```
!
show vrrp-a all

```

#### 粘贴以下命令到 vADC521_02
+ curl 有影响吗?
```
!
configure terminal
!
interface ethernet 2
  enable

```

#### 粘贴以下命令到 vADC521_02，并检查相应的输出
+ vrid 0 - 谁是 Active?
  + Active Priority 是多少?
+ vrid 1 - 谁是 Active?
  + Active Priority 是多少?
  + 为什么?
```
!
show vrrp-a all

```

#### 手动 Failover
#### 粘贴以下命令到 vADC521_01
+ curl 有影响吗?
```
!
configure terminal
!
vrrp-a force-self-standby vrid 1 enable

```

#### 粘贴以下命令到 vADC521_02，并检查相应的输出
+ vrid 0 - 谁是 Active?
  + Active Priority 是多少?
+ vrid 1 - 谁是 Active?
  + Active Priority 是多少?
+ vrid 1 - 谁是 Standby?
  + 为什么?
    + 如何恢复?
```
!
show vrrp-a all

```

## N+M (Active/Standby/Standby)
#### VRRP-a supports upto 8 Hardware appliances
#### VRRP-a supports upto 4 Virtual appliances
  + 有兴趣, 可以4人1组自己试试


## 保存配置并休息一下
#### 粘贴以下命令到 vADC521_01, vADC521_02
```
!
write memory lab11
y
!
show startup-config

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)