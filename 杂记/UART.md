![image-20220917015220327](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/image-20220917015220327.png)

![image-20220917015409107](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/image-20220917015409107.png)

![image-20220917015535482](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/image-20220917015535482.png)

CH340用于UART转USB， 电脑端安装CH340驱动

两个MCU的UART串口通信接线

![image-20220918170713159](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209181707197.png)

UART数据发送`'0x65'`，从StartBit开始，小端发送。以停止位结束

![image-20220921201147070](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209212011209.png)



receive poll模式， 每次接收数据从头开始放置。 等待设定的延时时间后才进行接下来的动作。阻塞式。影响程序执行，一般不使用这种模式。

![image-20220921185632830](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209211856895.png)

TX引脚在发送空闲状态时是高电平；

![image-20220922224526555](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209222245595.png)

![image-20220922224550572](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209222245618.png)

![image-20220922231028003](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209222310048.png)

![image-20220922231103980](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209222311036.png)

![image-20220922231338737](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209222313797.png)

![image-20220922232137355](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209222321410.png)

USART 配置流程

![image-20220922232648291](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209222326346.png)

![image-20220922232815419](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209222328466.png)

![image-20220922233417984](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209222334041.png)

![image-20220922233456920](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209222334956.png)

发送过程

![image-20220922234852343](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209222348383.png)

UART接收配置流程

![image-20220923000032076](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209230000132.png)

波特率计算

![image-20220923000851817](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209230008861.png)

![image-20220923000922621](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209230009664.png)

奇偶校验

![image-20220923001351486](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202209230013555.png)
