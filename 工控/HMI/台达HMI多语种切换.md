# 台达HMI多语种切换

最近在做一个出口产品的项目，HMI需要中英文切换。HMI选的是台达的DOP-110WS. 

* 首先打开`模块参数`设置， 找到`控制命令`->`命令区`， 勾选上**`系统控制旗标`**， 可以看到设定的寄存器的低8位就是控制显示语言的值；

![image-20221106214839944](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202211062148047.png)

* 创建一个交替型的语言切换按钮，切换`系统控制旗标`寄存器的状态；

  ![image-20221106215527642](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202211062155703.png)

* 每个需要英文显示的地方都填写上英文内容；

  ![image-20221106215642605](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202211062156692.png)

* 按下语言切换按钮，就可以切换控件显示的文字语言了；

![image-20221106215846341](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202211062158455.png)

![image-20221106215854650](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202211062158698.png)
