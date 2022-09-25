![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 8 - 健康检查
 - 三层健康检查
 - 四层健康检查
 - 七层健康检查
 - 脚本健康检查

#### 粘贴以下命令到 vADC521_01
```
write memory lab08
y
!
show startup-config all

```

#### 默认设置
+ 背一下

| 健康检查| 三层| 四层| 七层| 脚本|
| --- | --- | --- | --- | --- |
| Server | ON| ON| OFF| OFF|
| Service Group| OFF| OFF| OFF| OFF|

+ 默认设置
  + Method: Ping (icmp)
  + 背一下
    + Inteval: 5 seconds, Timeout: 5
    + Retry: 3, Up-Retry: 1
  + 健康检查失败时间
    + 5 + 5 x3 (Retry) = 20 秒
  + 健康检查成功时间
    + 5 x1 (Up-Retry) = 5 秒
    
#### 三层健康检查 (Ping)
+ 将以下配置粘贴到 vADC521_01
  + 为什么需要 health-check-disable?
  + httpbin 服务器有启用其他健康检查?
```
!
show run slb server httpbin

```


#### 四层健康检查 (Tcp port / Udp port)
+ 将以下配置粘贴到 vADC521_01
  + 哪些端口启用了第4层健康检查?
```
!
show run slb server web102
!
axdebug
!
filter 1
ip 10.240.70.102 /32
exit
!
capture brief

```

```
!
ctrl+c
!
exit

```


#### 七层健康检查
#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
+ 创建 Health Monitors
  + 点击 ADC > Health Monitors > Create
    + Name: hm-http-nginx
    + Method Type: HTTP
      + 背一下其他健康检查
      + Compound 是什么?
    + HTTP Expect: HTTP Text
    + HTTP Text: NgInX
    + 点击 Create
+ 绑定 hm-http-nginx 到 sg-http-tcp80
  + 点击 ADC > SLB > Service Gorups
    + 修改 sg-http-tcp80
      + Health Monitor : hm-http-nginx
      + 点击 Update
+ 保存配置
  + 点击 "Save"

#### 将以下配置粘贴到 vADC521_01
+ web101, web102 server 健康?
+ service-group sg-http-tcp80 健康? 
+ 背一下
```
!
show slb server
!
show slb service-group
!
show health monitor
!
show health stat

```

#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
#### 修改 hm-http-nginx Health Monitors
+ HTTP Text: nginx
  + 点击 Update
+ 保存配置
  + 点击 "Save"


#### 脚本健康检查
+ 脚本 (背一下)
  + Shell Script
  + Tcl Script
  + Perl Script
  + Python Script

Shell Script Sample
+ ping_src.sh
```bash
#!/bin/sh

src_ip=$1
dest_ip=$2
echo -n "ping $ip ...."

#test server IP, if fail return 1
ping $dest_ip -I $src_ip  -c 1  > /dev/null
ret=$?

if test $ret == 0 ; then
  echo "OK"
  exit 0
else
  echo "Fail"
  exit 1
fi
```

+ Passing 2 arguments to Shell Script
```
health monitor hm-llb-gw
  method external program ping_src arguments "ve2983 120.238.38.97"
```

## 保存配置并休息一下
#### 粘贴以下命令到 vADC521_01
```
!
write memory lab08
y
!
show startup-config all

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)