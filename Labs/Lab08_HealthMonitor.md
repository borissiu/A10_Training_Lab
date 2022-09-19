![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 8 - 健康检查
 - 三层健康检查
 - 四层健康检查
 - 七层健康检查
 - 脚本健康检查

#### 三层健康检查
+ 默认设置
  + Service Group Level "OFF"  
  + Server Level "ON"
    + Method: Ping (icmp)
    + Inteval: 5 seconds, Timeout: 5
    + Retry: 3, Up-Retry: 1

#### 四层健康检查
+ 默认设置
  + Service Group Level "OFF"
  + Server Level "ON"
    + Method: tcp syn/udp xxx
    + Inteval: 5 seconds, Timeout: 5
    + Retry: 3, Up-Retry: 1

#### 七层健康检查
+ 默认设置
  + Service Group Level "OFF"
  + Server Level "OFF"

#### 脚本健康检查
+ 默认设置
  + Service Group Level "OFF"
  + Server Level "OFF"










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
#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
  + 创建 aFlex
    + 点击 Create
      + Name: log_curl_agent
      + Definition: 复制并粘贴以下 aflex
  + 绑定 reject_curl_agent 到 vs80:443
    + 点击 ADC > Virtual Servers
      + 修改 vs80, port 443
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
curl --interface 192.168.226.21 -k https://192.168.226.80

curl --interface 192.168.226.22 -k https://192.168.226.80

curl --interface 192.168.226.23 -k https://192.168.226.80

curl --interface 192.168.226.24 -k https://192.168.226.80

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
#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
  + 创建 aFlex
    + 点击 Create
      + Name: reject_ip21_ip23
      + Definition: 复制并粘贴以下 aflex
  + 绑定 reject_ip21_ip23 到 vs80:443
    + 点击 ADC > Virtual Servers
      + 修改 vs80, port 443
      + 点击 Advanced Fields
        + aFlex Scripts: reject_ip21_ip23 打上钩
          + 保持 log_curl_agent 打上钩
        + 点击 Update
      + 点击 Update
  + 保存配置
    + 点击 "Save"  

#### aFlex example 1
  + Event used: CLIENT_ACCEPTED
  + Operator used: equals, or
  + Commands used: [IP::addr], [IP::client_addr], log, reject
```
when CLIENT_ACCEPTED {
  if { ([IP::addr [IP::client_addr] equals 192.168.226.21]) or ([IP::addr [IP::client_addr] equals 192.168.226.23]) } {
    log "Reject connection from [IP::client_addr]"
    reject
  }
}

```

#### 粘贴以下命令到 客户端
  + 并检查 客户端 相应的输出
```
curl --interface 192.168.226.21 -k https://192.168.226.80

curl --interface 192.168.226.22 -k https://192.168.226.80

curl --interface 192.168.226.23 -k https://192.168.226.80

curl --interface 192.168.226.24 -k https://192.168.226.80

```

#### 将以下配置粘贴到 vADC521_01
  + aFlex 配置的顺序是什么?
```
!
show aflex log_curl_agent
!
show aflex reject_ip21_ip23
!
show log
!
show run slb virtual-server

```


#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
write memory
!
show run slb

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)