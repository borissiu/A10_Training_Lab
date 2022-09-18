![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 6 - Load Balancing Acceleration
  + Connection Reuse
  + HTTP Ram Cache
  + HTTP Compression

## Connection Reuse
#### 粘贴以下命令到 客户端
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

  + 修改 nginx 配置之后
```
sudo systemctl restart nginx

sudo apt -y install net-tools

watch 'netstat -n | egrep -i "23:|24:"'

```

#### 将以下配置粘贴到 vADC521_01
```
!
clear slb all
!
repeat 2 show session | inc 23:\|24:

```

#### 打开另一个 SSH 客户端连接
#### 粘贴以下命令到 客户端
  + 并检查
    + 客户端 相应的输出
    + vADC521_01 相应的输出
  + web服务器忙于建立新连接?
```
for i in {1..10000}; do curl -k https://192.168.226.80; done
```

#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
  + 创建 Template Connection Reuse
    + 点击 ADC > Templates > Application
      + 点击 Create "Connection Re-use"
        + Name: connection_resue_test
  + 绑定 Connection Reuse Template 到 vs80:443
    + 点击 ADC > Virtual Servers
      + 修改 vs80, port 443
      + 点击 Templates
        + Template Connection Reuse: 选择 connection_reuse_test
  + 保存配置
    + 点击 "Save"  

#### 粘贴以下命令到 客户端
  + 并检查 客户端 相应的输出
  + 并检查 vADC521_01 相应的输出
  + web服务器忙于建立新连接?
```
for i in {1..10000}; do curl -k https://192.168.226.80; done
```


## HTTP RAM Caching
#### 将以下配置粘贴到 vADC521_01
```
!
clear slb cache entries vs80 443
!
show slb cache entries vs80 443
!
repeat 2 show cache stats vs80 443

```

#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
  + 创建 Template RAM Caching
    + 点击 ADC > Templates > Application
      + 点击 Create "RAM Caching"
        + Name: cache_test
        + Min Content Size: 0
  + 绑定 RAM Caching Template 到 vs80:443
    + 点击 ADC > Virtual Servers
      + 修改 vs80, port 443
      + Template Cache
        + 选择 cache_test
  + 保存配置
    + 点击 "Save"  

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
```
for i in {1..4}; do curl -k https://192.168.226.80; done

for i in {1..4}; do curl -k https://192.168.226.80/ip; done

```

#### 将以下配置粘贴到 vADC521_01
```
!
show slb cache entries vs80 443
!
show cache stats vs80 443

```


## HTTP Compression
#### 将以下配置粘贴到 vADC521_01
```
!
clear slb compression vs80 443
!
repeat 2 show slb compression vs80 443

```

#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
  + 修改 Template http_test
    + 点击 ADC > Templates > L7 Protocols
      + 修改 http_test
        + 点击: Compression
          + Content Type:
            + 添加 text/html
            + Action 保存
          + Enable: 0
          + Level: 5
  + 保存配置
    + 点击 "Save"  

#### 粘贴以下命令到 客户端
  + 并检查 vADC521_01 相应的输出
```
curl -sH 'Accept-encoding: gzip' -k https://192.168.226.80 | gunzip

```

#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
  + 点击 ADC > Statistics > Applications
    + 检查 Connection Re-use
    + 检查 RAM Caching
    + 检查 Compression


#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
write memory
!
show run slb

```