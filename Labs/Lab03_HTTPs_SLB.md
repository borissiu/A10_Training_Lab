![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 3 - HTTPs SLB
 + HTTPs Load Balancing (SSL卸载)
  + 创建或导入ssl证书
  + URL Switching
  + HTTP Header 源地址插入

## HTTPs Load Balancing
#### 将以下配置粘贴到 vADC521_01
```
configure terminal
!
slb virtual-server vip 192.168.2.31
  port 443 https
    source-nat pool snat
    service-group sg-http-tcp80
!
end
!
clear slb all

```

#### 粘贴以下命令到 客户端，并检查相应的输出
+ HTTPs 响应？
```
for i in {1..2}; do curl --connect-timeout 1 -k https://192.168.2.31; done

```

#### 连接到 vADC521_01 GUI 界面 (https://192.168.2.21)
+ 创建或导入ssl证书
  + 点击 ADC > SSL Management
  + 点击 Create
    + File Name: cert-www.a10.com
    + Common Name: www.a10.com
    + Country: China
    + Key Size: 2048
+ 创建 Client SSL Template
  + 点击 ADC > Template > SSL
  + 点击 Create Client SSL
    + Name: www.a10.com
    + Certificate List
      + Cert: cert-www.a10.com
      + Key: cert-www.a10.com
      + Chain Cert: can be skipped if using self sign cert.
    + Version: TLSv1.3
    + Downgradable Version: TLSv1.2
    + Reject Client Requests for SSLv3: 打上钩
    + 点击 "Ok"
+ 绑定 Client SSL Template 到 vip:443
  + 点击 ADC > Virtual Servers
    + 修改 vip, port 443
    + 添加 Template Client SSL
      + 选择 www.a10.com
    + 点击 "Update"
    + 点击 "Update"
+ 保存配置
  + 点击 "Save"  
    
#### 粘贴以下命令到 客户端，并检查相应的输出
+ HTTPs 响应？
```
for i in {1..100000}; do curl --connect-timeout 1 -k https://192.168.2.31; done

```

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
show slb ssl-counters

```

```
!
show session

```

## URL Switching
#### 将以下配置粘贴到 vADC521_01
```
configure terminal
!
slb server httpbin httpbin.org
  health-check-disable
  port 80 tcp
!
slb service-group sg-httpbin-tcp80 tcp
  member httpbin 80
!
end
!
clear slb all
!
repeat 2 show slb service-group | include 80

```

#### 连接到 vADC521_01 GUI 界面 (https://192.168.2.21)
+ 创建 HTTP Template
  + 点击 ADC > Templates > L7 Protocols > 
  + 点击 Create HTTP
    + Name: http_test
    + URL Switching: 打上钩
    + 添加:
      + Switching Type: starts-with
      + Match String: /ip
      + Matching Group: sg-httpbin-tcp80
      + Action: 点击 "Save"
+ 绑定 HTTP Template "http_test" 到 vip:443
  + 点击 ADC > Virtual Servers
    + 修改 vip80
    + 修改 port 443
      + 添加 Template HTTP
        + 选择 http_test
+ 保存配置
  + 点击 "Save"  

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
```
for i in {1..10}; do curl -k https://192.168.2.31; sleep 1; done

```

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
```
for i in {1..10}; do curl -k https://192.168.2.31/ip; sleep 1; done

```


## HTTP Header 源地址插入
#### 连接到 vADC521_01 GUI 界面 (https://192.168.2.21)
+ 修改 HTTP Template
  + 点击 ADC > Templates > L7 Protocols > 
  + 添加 Template HTTP "http_test"
    + Client IP Header Insert: 打上钩
    + Header Name: X-Forwarded-For
+ 保存配置
  + 点击 "Save"  

#### 粘贴以下命令到 客户端，并检查相应的输出
```
for i in {1..2}; do curl --interface 192.168.2.99 -k https://192.168.2.31/ip; done

for i in {1..2}; do curl --interface 192.168.2.100 -k https://192.168.2.31/ip; done
```


#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
write memory lab03
y
!
show startup-config all

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)