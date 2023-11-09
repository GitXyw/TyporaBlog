W25Q64  2.7到3.6V供电

## W25Q64 FLASH Block Diagram

![image-20220925105113246](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209251051458.png)

## 块、扇区和页的大小关系

W25Q64总容量64M-bit，有128个块（Block）, 2048个扇区(Sector)， 和32768个可编程页（Page）。

![image-20220925114406775](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209251144835.png)

Flash芯片的最小擦除单位为扇区（Sector）4KB。 一次擦除大小可为4KB，32KB， 64KB或全部擦除（chip erase）.

## SPI接口

SPI最大时钟频率104MHz， 50MB/S continuous data transfer rate  。

![image-20220925115742502](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209251157546.png)

接口定义

![image-20220925115829760](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209251158798.png)

## 写入前的操作

![image-20220925153045998](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209251530053.png)