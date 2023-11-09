SPI is normally full duplex (transmit and receive at the same time)

`MOSI`  :  master out slave in		/ 		COPI : controller out peripheral in

`MISO` : master in slave out		/			CIPO : controller in peripheral out 

![image-20220923124149897](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209231241968.png)

半双工模式 SDO和SIO共用一条线，不能同时接收和发送

![image-20220923124340020](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209231243096.png)

---

## SPI is a BUS

SPI 是一条总线，可以连接多个从站；每个从站有独立的chip select线（CS1、CS2）。CS Pin工作时处于低电平，初始状态时处于高电平状态 。STM32和那个从站通讯就把相应从站的CS线拉低。

![image-20220923124500535](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209231245638.png)

## SPI 参数 

![image-20220923130150003](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209231301093.png)

把上面几个参数总结如下。 最常用的是Mode 0;

![image-20220923130233538](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209231302604.png)

## SPI 通讯数据解析示例

* 开始通讯 发送命令；

![image-20220923130503565](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209231305651.png)

* 接收数据，通讯结束；

![image-20220923130649727](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209231306782.png)



## STM32连接 W25Q64 Flash 

![image-20220924095454274](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209240954327.png)

WP引脚和HOLD引脚

![image-20220924095542795](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209240955860.png)

![image-20220924102942060](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209241029132.png)

SPI 不能连续读不同的指令？

![image-20220924182438782](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209241824837.png)

连续读取DEVICE ID

![image-20220924182642984](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209241826042.png)