

CAN总线接口电路   TJA1050 是CAN总线协议控制器与物理总线之间的接口，该器件为CAN控制器提供差分发送和差分接收的能力。 CANH(6)、CANL(7)引脚是差分信号发送和接收引脚，连接到外部总线。连出去的线就是常说的CAN总线。TXD(1)、RXD(4)是连接到STM32的CAN引脚的，这里连接的是STM32F103的PA12和PA11引脚。

![image-20221016214301317](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210162143360.png)

连接多个设备的情形。（根据[datasheet](https://max.book118.com/html/2019/1010/8142045045002054.shtm)，上面这款CAN收发器支持连接110+个站点）。各个站点可以独立的收发数据帧，根据仲裁机制来处理冲突情况。

![image-20221016223714761](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210162237799.png)

物理接口CANH、CANL信号的形态

![image-20221016223051138](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210162230174.png)

CANH和CANL是一对差分信号，提升了抗干扰能力。noisy immunity。

CAN协议控制器CAN Tx、 CAN Rx信号的形态。（收发器的功能就是把下面这种信号转换成上面的两根线输出的差分信号。 CAN控制器没有收发器也可以正常使用，不过不能长距离通讯，且易受干扰）

![image-20221016224628430](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210162246459.png)



![image-20221016214412758](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210162144788.png)

STM32F103系列CAN总线发送数据的流程

![image-20221016234409755](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210162344799.png)









remote frame

![image-20221018000046794](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210180000850.png)

![image-20221021233410113](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210212334289.png)



![image-20221021233633142](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210212336204.png)

远程帧可用来查询节点的状态

![image-20221024000225101](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210240002129.png)