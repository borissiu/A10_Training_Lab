![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Layer 2 & 3 Setup
```

```

## Show Layer 2 & 3 Info.
#### 显示 Layer 1 信息
```
show interface brief
show interface ethernet
show interface ve
show interface trunk
show interface media
show interface statistics
```

#### 显示 Layer 2 信息
```
show mac
show arp
show vlan counters
show run vlan
```

#### 显示 Layer 3 信息。
```
show run ip route
show ip route
```

#### 显示 syslog 信息。
```
show run logging
show log
show log debug
```

## Check Connectivity
```
show run ip dns
ping 114.114.114.114
ping 114.114.114.114 use-mgmt-port
ping www.baidu.com
```

## Check Mgmt setting
```
show run enable-management
```

## Check Access Control List
```
show run access-list
show access-list
```

## Check Clock
```
show run ntp
show ntp status
show clock
show timezone
```
