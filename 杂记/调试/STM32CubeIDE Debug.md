# stm32cubeIDE  STLINK连接SWD接口调试，启动GDB server失败的处理

## 报警提示

> **Failed to bind to port 61234, error code -1: No error**
> **Failure starting GDB server: TCP port 61234 not available.**

![image-20221012003837650](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210120038688.png)

## 处理

1. 打开任务管理器；

![image-20221007231607158](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210072316222.png)

2. 结束STLINK GDB Server进程；

![image-20221012003820052](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210120038135.png)

3. 再连接Stlink调试就OK了；