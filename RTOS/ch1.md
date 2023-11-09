## RTOS

Real Time Operating System

![image-20221005122320331](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210051223424.png)

![image-20221005122342267](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210051223328.png)

![image-20221005145210406](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210051452448.png)

![image-20221005145426374](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210051454439.png)

![image-20221024193616211](C:\Users\David\AppData\Roaming\Typora\typora-user-images\image-20221024193616211.png)

![image-20221024193744590](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210241937672.png)

![image-20221024195129289](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210241951360.png)





程序运行起来之后，可以看到比我们创建的线程多了两个线程，这两个线程是"IDLE"和"Tmr Svc"，这两个线程是freertos自动创建的;

![image-20221027232141158](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210272321224.png)

IDLE线程的创建使得至少有一个线程在运行

![image-20221027231403057](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210272314109.png)

IDLE线程中可定义一个背景任务vApplicationIdleHook

![image-20221027231517490](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210272315532.png)

创建"Tmr Svc"线程是因为使用了“software timer”，在freertos配置中打开了`configUSE_TIMERS`

```c
#define configUSE_TIMERS                         1
```

Freertos scheduling : 

* time slicing scheduling、 `#define configUSE_PREEMPTION                     1`

* priority based preemptive scheduling、`#define configUSE_PREEMPTION                     1`

* cooperative scheduling.    `#define configUSE_PREEMPTION                     0`

![image-20221027234905010](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210272349102.png)

![image-20221027235314268](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210272353342.png)

![image-20221027235610404](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210272356509.png)

![image-20221028194825242](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210281948347.png)

![image-20221028202139348](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210282021395.png)

![image-20221028204248340](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210282042488.png)![image-20221028204628741](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210282046772.png)

![image-20221028204610884](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202210282046934.png)
