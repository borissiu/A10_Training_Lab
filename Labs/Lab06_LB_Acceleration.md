![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 6 - 应用加速 (Acceleration)
  + Connection Reuse
  + HTTP Compression
  + HTTP Ram Cache

#### 粘贴以下命令到 vADC521_01
```
write memory lab06
y
!
show startup-config all

```

## Connection Reuse
#### 粘贴以下命令到 2台 web 服务器
  + 修改 nginx 配置
    + keepalive timeout from 75 to 2400
    + keepalive requests from 100 to 1000
  + 修改 /etc/nginx/nginx.conf
    + 在 http { } 中间添加
      + keepalive_timeout 2400;
      + keepalive_requests 10000;

  + 修改 /etc/nginx/nginx.conf
```
cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak

sudo vi /etc/nginx/nginx.conf

```

  + 修改 nginx 配置后
```
sudo systemctl restart nginx

```

```
sudo apt -y install net-tools

watch -n 1 'netstat -n | egrep -i "101:|102:"'

```

#### 将以下配置粘贴到 vADC521_01
```
!
clear slb all
!
repeat 2 show session | inc 101:\|102:

```

#### 打开另一个 SSH 客户端连接
#### 粘贴以下命令到 客户端
  + 并检查
    + 客户端 相应的输出
    + vADC521_01 相应的输出
  + web 服务器忙于建立新连接?
```
for i in {1..10000}; do curl -k https://10.240.70.31; done
```

#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
  + 创建 Template Connection Reuse
    + 点击 ADC > Templates > Application
      + 点击 Create "Connection Re-use"
        + Name: connection_reuse_test
  + 绑定 Connection Reuse Template 到 vip:443
    + 点击 ADC > SLB > Virtual Servers
      + 修改 vip, port 443
      + 点击 Templates
        + Template Connection Reuse: 选择 connection_reuse_test
        + 点击 "Update" 
      + 点击 "Update" 
  + 保存配置
    + 点击 "Save"  

#### 粘贴以下命令到 客户端
  + 并检查
    + 客户端 相应的输出
    + vADC521_01 相应的输出
  + web 服务器忙于建立新连接?
```
for i in {1..10000}; do curl -k https://10.240.70.31; done
```

## HTTP Compression
#### 将以下配置粘贴到 vADC521_01
```
!
clear slb compression vip 443
!
repeat 2 show slb compression vip 443

```

#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
  + 修改 Template http_test
    + 点击 ADC > Templates > L7 Protocols
      + 修改 http_template_test
        + 点击: Compression
          + Content Type:
            + 添加 text/html
            + Action 保存
          + Enable: 打上钩
          + Level: 5
          + 点击 "Update"  
  + 保存配置
    + 点击 "Save"  

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
```
curl -sH 'Accept-encoding: gzip' -k https://10.240.70.31 | gunzip

```

#### 默认情况下，以下对象将被压缩
+ 记一下
  + Text, e.g. html/css/js
  + App, e.g. doc/xls/ppt/pdf

## HTTP RAM Caching
#### 将以下配置粘贴到 vADC521_01
```
!
clear slb cache entries vip 443
!
show slb cache entries vip 443

```

```
show slb cache stats vip 443

```

#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
  + 创建 Template RAM Caching
    + 点击 ADC > Templates > Application
      + 点击 Create "RAM Caching"
        + Name: cache_test
        + Min Content Size: 0
  + 绑定 RAM Caching Template 到 vip:443
    + 点击 ADC > SLB > Virtual Servers
      + 修改 vip, port 443
      + Template Cache
        + 选择 cache_test
        + 点击 "Update"
      + 点击 "Update"
  + 保存配置
    + 点击 "Save"  

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
```
for i in {1..4}; do curl -k https://10.240.70.31; done

for i in {1..4}; do curl -k https://10.240.70.31/ip; done

```

#### 将以下配置粘贴到 vADC521_01
```
!
show slb cache entries vip 443

```

```
!
show slb cache stats vip 443

```

#### 了解清楚, 再记三下
+ (1) 默认缓存, 除非 web 服务器响应拒绝缓存
+ (2) 以下 HTTP Response Status code 将被缓存
  + 200 OK
  + 203 Non-Authoritative response
  + 300 Multiple Choices
  + 301 Moved Permanently
  + 302 Found (only if Expires header is also present)
  + 410 Gone
+ (3) 以下 HTTP Response Header 将不被缓存
  + Does not support client HTTP range requests (they are sent to the servers)
  + Does not cache server responses with "Vary" header (except "Vary: Accept-Encoding")
  + Does not cache server responses with "Warning" header
  + Does not cache server responses if requests had an "Authorization" header (even if the server specifies "Cache-Control: public”)
  + Does not cache incomplete (partial) responses

#### 连接到 vADC521_01 GUI 界面 (https://10.240.70.21)
  + 点击 ADC > Statistics > Applications
    + 检查 Connection Re-use
    + 检查 RAM Caching
    + 检查 Compression
  + 记得 Disable Compression before moving to next lab.
  + 记得 Disable Cache before moving to next lab.

#### 将以下配置粘贴到 vADC521_01
```
!
show slb virtual-server bind
!
show slb virtual-server config

```

## 保存配置并休息一下
#### 粘贴以下命令到 vADC521_01
```
!
write memory lab06
y
!
show startup-config all

```

[返回 A10 Training Lab. 主页](https://github.com/borissiu/A10_Training_Lab)
