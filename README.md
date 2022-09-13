![](/Images/A10-NewLogos-Blue-NoReg-RGB-50.png)
---

# A10_Training_Lab
```
安装前准备
设备初始化
网络层配置
Active/Standby配置

健康检查
会话保持（可选）
SSL卸载
HTTP 源地址插入

常见问题及处理
```

#### For details, please refer to A10_4.1.4-GR1-P5_DMG.pdf


[link:cheat-sheet](https://www.interviewbit.com/markdown-cheat-sheet/#text-styles)
[link:cheat-sheet](https://www.markdownguide.org/cheat-sheet/)

| Model | FPGA | Xeon CPU | Memory | L4CPS | L7CPS | Conn Session | Remarks |
|---|---|---|---|---|---|---|---|
| TH3430s | FTA-4 | **6-core** | **32GB** | 2.5M | 620k | 128 M | |
| TH3350s | CPU simulate | 14-core | 64GB | 2M | 750k | 128M | <<< **3 CPUs used to simulate FPGA** >>>, Running 5.2.1 but NOT 4.1.4-GR1 |
| TH4440s | 2x FTA-4 | 6-core | 32GB | 2M | 750k | 128M | CPU & Memory same as Th3430s |
| **TH5440s** | 2x FTA-4 | **12-core** | **64GB** | 3.2M | 950k | 256M | <<< **Recommended Model** >>> |
| TH5840s | 2x FTA-4 | 18-core | 64GB | 4.5M | 1.5M | 256M | For long term planning |

