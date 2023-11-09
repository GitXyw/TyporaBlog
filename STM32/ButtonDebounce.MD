## 一、按键抖动的现象
按键按下和松开的时候，按键金属片之间的贴合、分离有一个过程。给STM32输入的信号并不是理想的0和1切换的过程。而是如下图所示的，按下和松开的一小段时间内按键信号出现抖动（**jitter**），这种现象称为按键抖动（**Button Bouncing**）。为了避免程序上出现误动作，需要从硬件或软件上消除按键抖动（**Button Debouncing**）。  
![button bouncing](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220904084239.png)
***
## 二、 硬件电路消抖
可以从电路设计上消除抖动，常见的有RC滤波电路消抖。但是仅通过RC电路，消抖过程慢，实际效果也并不好，一般会加上施密特触发器。硬件消抖的缺点是要增加额外的元器件，如果有多个需要消抖的输入信号，则会增加较大的成本。
* RC电路  
![](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220904102451.png)
![RC debounce circuit](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220904084508.png)
* RC电路加施密特触发器  
![](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220904084600.png)
***
## 三、 软件消抖
### 3.1 按键状态分析
按键状态变化后，短时间内的状态是抖动的、不可采用的。软件上可延迟一段时间再判断按键的状态。按键的状态机变化如下图所示。
![button state](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220904104901.png)
### 3.2 程序实现
下面通过程序来实现按键的消抖。下例中的开发板MCU为stm32f103RCT6， 按键接在PB12、PB13引脚，LED接在PC0、PC1引脚。程序基于HAL库编写，外设的初始化程序由Stm32CubeMx软件生成，此处不再赘述。
* 循环阻塞判断
``` C
int main(void)
{
    while (1)
    {
        if (HAL_GPIO_ReadPin(Button1_GPIO_Port, Button1_Pin) == GPIO_PIN_RESET)
        {
            HAL_Delay(20);
            if(HAL_GPIO_ReadPin(Button1_GPIO_Port, Button1_Pin) == GPIO_PIN_RESET)
            {
                printf("Key 1 pressed.\n");
                HAL_GPIO_TogglePin(Led1_GPIO_Port, Led1_Pin);
                while(HAL_GPIO_ReadPin(Button1_GPIO_Port, Button1_Pin) == GPIO_PIN_RESET);      // 等待按键松开
            }
        }
    }
}

```
上面的方式，按键松开之前程序一直卡在while循环里，按键松开之后才能处理其他的程序。
***
* 增加标志位、非阻塞
``` C
int main(void)
{
    uint8_t Button1PressedFlag = 0;
	uint8_t Button2PressedFlag = 0;
    while (1)
    {
	    if (Button1PressedFlag == 0 && HAL_GPIO_ReadPin(Button1_GPIO_Port == Button1_Pin) == GPIO_PIN_RESET)
	    {
		    HAL_Delay(20);
		    if(HAL_GPIO_ReadPin(Button1_GPIO_Port, Button1_Pin) == GPIO_PIN_RESET)
		    {
			    printf("Key 1 pressed.\n");
			    HAL_GPIO_TogglePin(Led1_GPIO_Port, Led1_Pin);
			    Button1PressedFlag = 1;
		    }
	    }
	    if(Button1PressedFlag == 1 && HAL_GPIO_ReadPin(Button1_GPIO_Port, Button1_Pin) == GPIO_PIN_SET)
	    {
		    HAL_Delay(20);
		    if(HAL_GPIO_ReadPin(Button1_GPIO_Port, Button1_Pin) == GPIO_PIN_SET)
		    {
			    printf("Key 1 released.\n");
			  Button1PressedFlag = 0;
		    }
	    }

	    if (Button2PressedFlag == 0 && HAL_GPIO_ReadPin(Button2_GPIO_Port, Button2_Pin) == GPIO_PIN_RESET)
	  	{
            HAL_Delay(20);
            if(HAL_GPIO_ReadPin(Button2_GPIO_Port, Button2_Pin) == GPIO_PIN_RESET)
            {
                printf("Key 2 pressed.\n");
                HAL_GPIO_TogglePin(Led2_GPIO_Port, Led2_Pin);
                Button2PressedFlag = 1;
            }
	  	}
	    if(Button2PressedFlag == 1 && HAL_GPIO_ReadPin(Button2_GPIO_Port, Button2_Pin) == GPIO_PIN_SET)
	    {
		    HAL_Delay(20);
		    if(HAL_GPIO_ReadPin(Button2_GPIO_Port, Button2_Pin) == GPIO_PIN_SET)
		    {
			    printf("Key 2 released.\n");
			    Button2PressedFlag = 0;
		    }
	    }
    }
}
```
上面实现的是两个按键消抖的处理。非阻塞方式可实现两个LED灯的同时点亮和熄灭，阻塞方式只能一个一个地操作。  
***
* 外部中断方式  
①. 将按键GPIO设置为外部中断输入方式，中断捕获类型可根据实际电路设置为上升沿或下降沿，这里我们配置为内部上拉、下降沿中断方式。  
![](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220904163931.png)  
②. 设置中断优先级，打开中断  
![](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220904164044.png)  
③. 在stm32f1xx_it.c文件中编写中断回调函数
``` C
void EXTI15_10_IRQHandler(void)
{
  HAL_GPIO_EXTI_IRQHandler(GPIO_PIN_12);
  HAL_GPIO_EXTI_IRQHandler(GPIO_PIN_13);
}       // EXTI15_10_IRQHandler 中断ISR 有CubeMx生成

void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin)
{
	if(GPIO_Pin == GPIO_PIN_12)
	{
		printf("Button triggered!\n");
		HAL_Delay(20);
		if(HAL_GPIO_ReadPin(GPIOB, GPIO_PIN_12) == GPIO_PIN_RESET)
		{
			HAL_GPIO_TogglePin(Led1_GPIO_Port, Led1_Pin);
			printf("Led toggled!\n");
		}
	}
	if(GPIO_Pin == GPIO_PIN_13)
	{
		HAL_Delay(20);
		if(HAL_GPIO_ReadPin(GPIOB, GPIO_PIN_13) == GPIO_PIN_RESET)
		{
			HAL_GPIO_TogglePin(Led2_GPIO_Port, Led2_Pin);
		}
	}
}       // 中断回调函数 按键按下之后执行的动作由自己编写
```  
&emsp;&emsp;&emsp;⑤. 最后，还需修改一下HAL库中的外部GPIO中断服务函数
``` C
void HAL_GPIO_EXTI_IRQHandler(uint16_t GPIO_Pin)
{
  /* EXTI line interrupt detected */
  if (__HAL_GPIO_EXTI_GET_IT(GPIO_Pin) != 0x00u)
  {
//    __HAL_GPIO_EXTI_CLEAR_IT(GPIO_Pin);    注释此行
    HAL_GPIO_EXTI_Callback(GPIO_Pin);
    __HAL_GPIO_EXTI_CLEAR_IT(GPIO_Pin);     // 添加此行
  }
}
```
**<span style="color:red;font-size:20px;">关于中断消抖的方式，有几个需要注意的点。以上只是实现过程的描述，具体细节下一篇更新</span>**  



[//]: # (这是一条注释)
[//]: # (https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220904093241.png)
