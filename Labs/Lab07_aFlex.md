![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 7 - aFlex
  + aFlex Syntex
    + Event(s)
    + Operator(s)
    + Commands(s)
  + aFlex Example 1 (应用层)
  + aFlex Example 2 (网络层)
  + aFlex example 3 (log_tcp)
  + aFlex statistics

#### 粘贴以下命令到 vADC521_01
```
write memory lab07
y
!
show startup-config all

```

## aFlex Syntex
  + Event: HTTP_REQUEST
  + Operator: ends_with
  + Commands: pool
```
when HTTP_REQUEST {
  if { [HTTP::uri] ends_with ".html" } {
    pool static_service_group
  } elseif { [HTTP::uri] ends_with ".asp" } {
    pool dynamic_service_group
  }
}

```

## aFlex example 1
#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
  + 创建 aFlex
    + 点击 Create
      + Name: log_curl_agent
      + Definition: 复制并粘贴以下 aFlex example 1
  + 绑定 log_curl_agent 到 vip:443
    + 点击 ADC > SLB > Virtual Servers
      + 修改 vip, port 443
      + 点击 Advanced Fields
        + aFlex Scripts: log_curl_agent 打上钩
        + 点击 Update
      + 点击 Update
  + 保存配置
    + 点击 "Save"  

#### aFlex example 1
  + Event used: HTTP_REQUEST
    + Operator used: contains
    + Commands used: [IP::client_addr]. [HTTP::header], pool
```
when HTTP_REQUEST {
  if { [HTTP::header User-Agent] contains "curl" } {
    log "Client info. : [IP::client_addr] [HTTP::header User-Agent]"
    pool sg-http-tcp80
  }
}

```

#### 粘贴以下命令到 客户端
  + 并检查 客户端 相应的输出
```
curl --interface 10.240.70.242 -k https://10.240.70.31

curl --interface 10.240.70.243 -k https://10.240.70.31

###curl --interface 10.240.70.101 -k https://10.240.70.31

###curl --interface 10.240.70.102 -k https://10.240.70.31

```

#### 将以下配置粘贴到 vADC521_01
  + 并检查 
    + Event
    + execute, failures and aborts
```
!
show aflex log_curl_agent
!
show log

```


## aFlex example 2
#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
  + 创建 aFlex
    + 点击 Create
      + Name: reject_ip21_ip23
      + Definition: 复制并粘贴以下 aFlex example 2
  + 绑定 reject_ip21_ip23 到 vip:443
    + 点击 ADC > SLB > Virtual Servers
      + 修改 vip, port 443
      + 点击 Advanced Fields
        + aFlex Scripts: reject_ip21_ip23 打上钩
          + 保持 log_curl_agent 打上钩
        + 点击 Update
      + 点击 Update
  + 保存配置
    + 点击 "Save"  

#### aFlex example 2
  + Event used: CLIENT_ACCEPTED
    + Operator used: equals, or
    + Commands used: [IP::addr], [IP::client_addr], log, reject
```
when CLIENT_ACCEPTED {
  if { ([IP::addr [IP::client_addr] equals 10.240.70.242]) or ([IP::addr [IP::client_addr] equals 10.240.70.101]) } {
    log "Reject connection from [IP::client_addr]"
    reject
  }
}

```

#### 粘贴以下命令到 客户端
  + 并检查 客户端 相应的输出
```
curl --interface 10.240.70.242 -k https://10.240.70.31

curl --interface 10.240.70.243 -k https://10.240.70.31

###curl --interface 10.240.70.101 -k https://10.240.70.31

###curl --interface 10.240.70.102 -k https://10.240.70.31

```

#### 将以下配置粘贴到 vADC521_01
  + 并检查 
```
!
show aflex reject_ip21_ip23
!
show log

```

## aFlex example 3
#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
  + 创建 aFlex
    + 点击 Create
      + Name: log_tcp
      + Definition: 复制并粘贴以下 aFlex example 3
  + 绑定 log_tcp 到 vip:443
    + 点击 ADC > SLB > Virtual Servers
      + 修改 vip, port 443
      + 点击 Advanced Fields
        + aFlex Scripts: log_tcp 打上钩
          + 保持 log_curl_agent 打上钩
          + 保持 reject_ip21_ip23 打上钩
        + 点击 Update
      + 点击 Update
  + 保存配置
    + 点击 "Save"  

#### aFlex example 3
  + Event used: CLIENT_ACCEPTED
    + Commands used: [IP::client_addr], [TCP::client_port], [IP::local_addr], [TCP::local_port], [TIME::clock]
  + Event used: SERVER_CONNECTED
    + Commands used: [IP::server_addr], [TCP::server_port], [IP::local_addr], [TCP::local_port], log
```
when CLIENT_ACCEPTED {
  set timestamp [TIME::clock seconds]
  set cip [IP::client_addr]
  set cport [TCP::client_port]
  set vip [IP::local_addr]
  set vport [TCP::local_port]
}

when SERVER_CONNECTED {
  set sip [IP::server_addr]
  set sport [TCP::server_port]
  set snat_ip [IP::local_addr]
  set snat_port [TCP::local_port]

  log "\[$timestamp\] $cip:$cport <-> $vip:$vport + $snat_ip:$snat_port <-> $sip:$sport"
}

```

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
    + show log
```
curl --interface 10.240.70.242 -k https://10.240.70.31

curl --interface 10.240.70.243 -k https://10.240.70.31

###curl --interface 10.240.70.101 -k https://10.240.70.31

###curl --interface 10.240.70.102 -k https://10.240.70.31

```

#### 将以下配置粘贴到 vADC521_01
  + aFlex 配置的顺序是什么?
```
!
show aflex log_tcp

```

```
!
show log

```

```
!
show run slb virtual-server

```


## 保存配置并休息一下
#### 粘贴以下命令到 vADC521_01
```
!
write memory lab07
y
!
show startup-config all

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)