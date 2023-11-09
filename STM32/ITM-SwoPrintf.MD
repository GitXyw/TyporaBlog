# STM32CubeIDE + ITM SWV实现printf调试信息的打印
STM32开发时，使用printf函数来打印信息是一种方便的调试方法。不需要像使用断点来中断程序运行即可方便地查看想要的信息。一般，可以通过串口输出调试信息，或将printf重定向到串口输出上，但是这样就占用了串口的硬件资源，而且接线上比较麻烦。而ITM就是一种特别方便用于调试诊断的工具，不需要占用额外的硬件资源即可方便地实现跟踪监控。  
***
## 什么是ITM  
ITM是cortex中的一种硬件设计，Cortex M3、M4、M7系列内核的芯片都支持该接口（M0系列内核不支持）。ITM仅支持SWD调试接口，不支持JTAG接口。相较于传统SWD接口的SWCLK和SWDIO两根数据线，使用ITM功能时还需要增加一根额外的线连接到SWO接口。  
![ITM 硬件](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220901132907.png)
![](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220901133928.png)
## 硬件连接  
1. 使用ITM调试功能时，使用的线比SWD接口多一条SWO线，某宝上的非官方版本的ST-link V2调试器大多不支持SWO接口。我最后选购了<a href="https://item.taobao.com/item.htm?spm=a1z09.2.0.0.335b2e8dnQSHiu&id=592119156479&_u=11nj0b4f826c">J-OB V2</a>和[ST-link V2.1](https://item.taobao.com/item.htm?spm=a1z09.2.0.0.335b2e8dnQSHiu&id=652657776917&_u=11nj0b4f0342)这两款调试工具。  以下以ST-link V2工具进行说明。
![J-OB V2](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220901233321.png)
![ST-link V2.1](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220901233416.png)
2. 连接到STM32f103芯片开发板  
* 正常连接SWD接口；
* 将调试器端的SWO pin针脚连接到开发板的PB3口上，因为查看datasheet可知，PB3接口可复用为SWO输出功能；
![connection](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220901235722.png)
![PB3-SWO](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220902000359.png)
## 软件配置
1. 使用STM32CubeIDE新建STM32工程，选择对应的芯片型号；
![](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220902000814.png)
2. 配置HSE时钟。将调试模式设置为<strong>`Trace Asynchronous Sw`</strong>模式；
![](https://img2022.cnblogs.com/blog/2945242/202209/2945242-20220902001528737-1183959868.gif)
3. 根据需要配置时钟树。这里将系统时钟配置为36MHz；
![](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220902001901.png)
4. 保存配置，生产代码；
5. 在`main.c`文件中添加_write函数来重定向printf，并添加`stdio.h`头文件.接下来就可以使用`printf`函数来打印信息了;
``` C
#include <stdio.h>

int _write(int file, char *ptr, int len)
{
	int DataIdx;

	for (DataIdx = 0; DataIdx < len; DataIdx++)
	{
		ITM_SendChar(*ptr++);       // 把printf函数重定向到ITM_SendChar
	}
	return len;
}
```
6. 在main函数中使用`printf`，测试一下效果；
``` C
int main(void)
{
 
    uint32_t count = 0;
  
    HAL_Init();
    SystemClock_Config();
    MX_GPIO_Init();
 
    while (1)
    {
	    printf("Hello world! ");
	    printf("I'm in Cycle %ld\n", count);
	    count++;
	    HAL_Delay(500);								// delay for 500 ms
    }

}
```
7. 项目Debug设置，打开**SWV**。SWV时钟需与之前时钟树配置中的FClk一致；
![](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220902003453.png)
8. 点击“Debug”，开始调试；
9. 开始调试后， 打开下图这两个窗口；
![](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220902004440.png)
10. 设置SWV， 打开0号端口
![](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220902004732.png)
11. 运行程序，可以看到SWV监视窗口正常地输出打印信息和数据跟踪信息。
![](https://img2022.cnblogs.com/blog/2945242/202209/2945242-20220902005338623-1749348808.gif)