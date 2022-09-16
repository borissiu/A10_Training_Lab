![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)

## Lab. 4 - Load Balancing Algorithm
+ Round Robin (Round Robin on SERVER level)
+ Lease Connection (Lease Connection on SERVER level)
+ Service Least Connection (Lease Connection SERVICE PORT level)
+ Weighted Round Robin
+ Weighted Lease Connection
+ Service Weighted Round Robin
+ Service Weighted Least Connection

 - Priority

## Load Balancing Algorithm
#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
+ 由于没有 License，GUI 速度会有点慢
+ 启用dns1、dns5、dns6

#### 将以下配置粘贴到 vADC521_01
```
!
clear slb all
!
repeat 1 show slb server | include udp

```

#### 粘贴以下命令到 客户端，并检查相应的输出
+ 
```
for i in {1..30}; do dig @192.168.2.53 www.a10networks.com; done

```

#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
+ 由于没有 License，GUI 速度会有点慢
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
    + Certificate List: cert-www.a10.com
    + Version: TLSv1.3
    + Downgradable Version: TLSv1.2
    + Reject Client Requests for SSLv3: 打上钩
+ 绑定 Client SSL Template 到 vs80:443
  + 点击 ADC > Virtual Servers
    + 修改 vs80, port 443
    + 添加 Template Client SSL
      + 选择 www.a10.com
+ 保存配置
  + 点击 "Save"  
    
#### 粘贴以下命令到 客户端，并检查相应的输出
+ HTTPs 响应？
```
for i in {1..100000}; do curl --connect-timeout 1 -k https://192.168.226.80; done

```

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
show slb ssl-counters
!
show sessions

```

## HTTP Header 源地址插入
#### 连接到 vADC521_01 GUI 界面 (https://192.168.247.11)
+ 由于没有 License，GUI 速度会有点慢
+ 创建 HTTP Template
  + 点击 ADC > Templates > L7 Protocols > 
  + 点击 Create HTTP
    + Client IP Header Insert: 打上钩
    + Header Name: X-Forwarded-For
+ 绑定 HTTP Template 到 vs80:80
  + 点击 ADC > Virtual Servers
    + 修改 vs80, port 80
    + 添加 Template HTTP
      + 选择 http
+ 保存配置
  + 点击 "Save"  
    
#### 粘贴以下命令到 客户端，并检查相应的输出
```
sudo tcpdump -i $(ip addr | egrep -i "192.168.226.21" | awk '{print $NF}') -nn -A port 80 | egrep -i "X-Forwarded-For"

```

#### 打开另一个 SSH 连接
#### 粘贴以下命令到 客户端，并检查相应的输出
```
for i in {1..3}; do curl --interface 192.168.226.21 http://192.168.226.80; done

for i in {1..3}; do curl --interface 192.168.226.22 http://192.168.226.80; done
```

#### 粘贴以下命令到 vADC521_01，并检查相应的输出
```
!
write memory
!
show run slb

```
