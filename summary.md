# 软复位和硬复位

软复位：CPU发出复位指令（几行代码，写寄存器），随后系统产生

硬复位：拉低PORST引脚



# 复位

## stm32f1&stm32f4

复位系统分为上电复位、系统复位、备份域复位。最重要的是系统复位，如下图：

![image-20231220013730478](summary.assets/image-20231220013730478.png)

上电复位范围：除了备份区以外的所有寄存器。电源复位最终作用的也是RESET引脚。

系统复位范围：除了时钟控制器的RCC_CSR寄存器中的复位标志位和备份区域中的寄存器以外，将复位所有寄存器到复位状态。

==上电和系统复位之后，CPU执行的情况是什么样的呢？==

CPU的PC指针0x0000 0004处取出PC的初始值，也就是复位向量。然后从复位向量指向的地方取址执行。

![image-20231220023128691](summary.assets/image-20231220023128691.png)

## stm32H7x3

复位范围取决于复位信号源。共有三种复位类型：

+ 上电/掉电复位
+ 系统复位
+ 本地复位

复位信号源有以下几种：

+ 外部器件（通过 NRST 引脚）
+ VDD 电源电压故障
+ 看门狗超时
+ 软件命令

### 系统复位

![image-20231220020514891](summary.assets/image-20231220020514891.png)

系统复位 (nreset) 可将所有寄存器均复位为其默认值，但 RCC_RSR（或 RCC_C1_RSR） 寄存器中的复位状态标志、调试功能、Flash 和备份域寄存器除外。

### 本地复位

+ CPU复位。
+ 域复位。

### 复位源及其范围

![image-20231220023723504](summary.assets/image-20231220023723504.png)

![image-20231220023735196](summary.assets/image-20231220023735196.png)

## TC397

### 概述

#### 框图

![image-20231221014013230](summary.assets/image-20231221014013230.png)

#### 热复位和冷复位

热复位：当系统供电稳定并且可操作时触发的复位。

冷复位：上电期间或者响应临时电源故障时首次触发的复位。

### 复位源触发的复位类型

![image-20231221014828399](summary.assets/image-20231221014828399.png)



### 复位类型产生的影响

![reset_type](summary.assets/reset_type.png)