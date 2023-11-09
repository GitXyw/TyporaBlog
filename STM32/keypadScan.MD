# 【STM32扫描4x4矩阵键盘模块】 4x4 matrix keypad interface
***
## 4x4矩阵键盘模块
矩阵键盘是将多个按键排布成类似矩阵形式的键盘组。为了减少IO资源的占用，将键盘组的每一行和每一列接入到GPIO。如下图的薄膜型键盘模块，第一个键盘组是3行x4列，所以共使用了3+4=7个GPIO口，即用7个GPIO表现了12个按键的状态；第二个键盘组是4行x4列，共使用了4+4=8个GPIO，即用8个GPIO表现了16个按键的状态。  
![key pad](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220906231044.png)
怎样用较少的IO口来表示更多的状态呢？ 下面分析一下矩阵键盘的原理。  

## 矩阵键盘的原理  
下面以4x4矩阵键盘为例：
![](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220907014221.png)  
如上图所示4x4矩阵，同一行的按键开关一侧连接在行线上，从上到下分别记作R1、R2、R3、R4。同一列的按键开关一侧连接在列线上，从左到右分别记作C1、C2、C3、C4。 R1、R2、R3、R4连接到STM32的GPIO，并设置为输出模式。C1、C2、C3、C4接入到STM32的GPIO，设置为输入上拉模式。
## 行列扫描法
1. 第1行输出为0，第2、3、4行输出为1；
2. 分别判断四根列线  
    * C1为低电平， 则按键“1”被按下；
    * C2为低电平， 则按键“2”被按下；
    * C3为低电平， 则按键“3”被按下；
    * C4为低电平， 则按键“A”被按下；
***
3. 第2行输出为0，第1、3、4行输出为1；
4. 分别判断四根列线  
    * C1为低电平， 则按键“4”被按下；
    * C2为低电平， 则按键“5”被按下；
    * C3为低电平， 则按键“6”被按下；
    * C4为低电平， 则按键“B”被按下；
***
5. 第3行输出为0，第1、2、4行输出为1；
6. 分别判断四根列线  
    * C1为低电平， 则按键“7”被按下；
    * C2为低电平， 则按键“8”被按下；
    * C3为低电平， 则按键“9”被按下；
    * C4为低电平， 则按键“C”被按下；
***
7. 第4行输出为0，第1、2、3行输出为1；
8. 分别判断四根列线  
    * C1为低电平， 则按键“*”被按下；
    * C2为低电平， 则按键“0”被按下；
    * C3为低电平， 则按键“#”被按下；
    * C4为低电平， 则按键“D”被按下；
## 代码实现（基于HAL库）
**`keypad.h`**
``` C
#ifndef __KEYPAD_H__
#define __KEYPAD_H__


#include "gpio.h"
#include "main.h"

#define KEYPAD_DEBOUNCE_DELAYTIME			20u

#define KEYPAD_COLUMN_4_Pin 				GPIO_PIN_15
#define KEYPAD_COLUMN_4_GPIO_Port 			GPIOB
#define KEYPAD_COLUMN_3_Pin 				GPIO_PIN_14
#define KEYPAD_COLUMN_3_GPIO_Port 			GPIOB
#define KEYPAD_COLUMN_2_Pin 				GPIO_PIN_13
#define KEYPAD_COLUMN_2_GPIO_Port 			GPIOB
#define KEYPAD_COLUMN_1_Pin 				GPIO_PIN_12
#define KEYPAD_COLUMN_1_GPIO_Port 			GPIOB

#define KEYPAD_ROW_4_Pin 					GPIO_PIN_4
#define KEYPAD_ROW_4_GPIO_Port 				GPIOA
#define KEYPAD_ROW_3_Pin 					GPIO_PIN_5
#define KEYPAD_ROW_3_GPIO_Port 				GPIOA
#define KEYPAD_ROW_2_Pin 					GPIO_PIN_6
#define KEYPAD_ROW_2_GPIO_Port 				GPIOA
#define KEYPAD_ROW_1_Pin 					GPIO_PIN_7
#define KEYPAD_ROW_1_GPIO_Port 				GPIOA

#define KEYPAD_ROW_1_LOW					XY_GPIO_SetPinLow(KEYPAD_ROW_1_GPIO_Port, KEYPAD_ROW_1_Pin)
#define KEYPAD_ROW_1_HIGH					XY_GPIO_SetPinHigh(KEYPAD_ROW_1_GPIO_Port, KEYPAD_ROW_1_Pin)

#define KEYPAD_ROW_2_LOW					XY_GPIO_SetPinLow(KEYPAD_ROW_2_GPIO_Port, KEYPAD_ROW_2_Pin)
#define KEYPAD_ROW_2_HIGH					XY_GPIO_SetPinHigh(KEYPAD_ROW_2_GPIO_Port, KEYPAD_ROW_2_Pin)

#define KEYPAD_ROW_3_LOW					XY_GPIO_SetPinLow(KEYPAD_ROW_3_GPIO_Port, KEYPAD_ROW_3_Pin)
#define KEYPAD_ROW_3_HIGH					XY_GPIO_SetPinHigh(KEYPAD_ROW_3_GPIO_Port, KEYPAD_ROW_3_Pin)

#define KEYPAD_ROW_4_LOW					XY_GPIO_SetPinLow(KEYPAD_ROW_4_GPIO_Port, KEYPAD_ROW_4_Pin)
#define KEYPAD_ROW_4_HIGH					XY_GPIO_SetPinHigh(KEYPAD_ROW_4_GPIO_Port, KEYPAD_ROW_4_Pin)


#define KEYPAD_ROW_HIGH_BUT_1 \
{\
	KEYPAD_ROW_1_LOW;\
	KEYPAD_ROW_2_HIGH;\
	KEYPAD_ROW_3_HIGH;\
	KEYPAD_ROW_4_HIGH;\
}

#define KEYPAD_ROW_HIGH_BUT_2 \
{\
	KEYPAD_ROW_1_HIGH;\
	KEYPAD_ROW_2_LOW;\
	KEYPAD_ROW_3_HIGH;\
	KEYPAD_ROW_4_HIGH;\
}

#define KEYPAD_ROW_HIGH_BUT_3 \
{\
	KEYPAD_ROW_1_HIGH;\
	KEYPAD_ROW_2_HIGH;\
	KEYPAD_ROW_3_LOW;\
	KEYPAD_ROW_4_HIGH;\
}

#define KEYPAD_ROW_HIGH_BUT_4 \
{\
	KEYPAD_ROW_1_HIGH;\
	KEYPAD_ROW_2_HIGH;\
	KEYPAD_ROW_3_HIGH;\
	KEYPAD_ROW_4_LOW;\
}


#define KEYPAD_COLUMN_1_CHECK			!XY_GPIO_GetInputPinValue(KEYPAD_COLUMN_1_GPIO_Port, KEYPAD_COLUMN_1_Pin)
#define KEYPAD_COLUMN_2_CHECK			!XY_GPIO_GetInputPinValue(KEYPAD_COLUMN_2_GPIO_Port, KEYPAD_COLUMN_2_Pin)
#define KEYPAD_COLUMN_3_CHECK			!XY_GPIO_GetInputPinValue(KEYPAD_COLUMN_3_GPIO_Port, KEYPAD_COLUMN_3_Pin)
#define KEYPAD_COLUMN_4_CHECK			!XY_GPIO_GetInputPinValue(KEYPAD_COLUMN_4_GPIO_Port, KEYPAD_COLUMN_4_Pin)

#define KEYPAD_NO_PRESSED					(uint8_t)0xFF

typedef enum {
	KEYPAD_Button_0 = 0x00,                     /*!< Button 0 code */
	KEYPAD_Button_1 = 0x01,                     /*!< Button 1 code */
	KEYPAD_Button_2 = 0x02,                     /*!< Button 2 code */
	KEYPAD_Button_3 = 0x03,                     /*!< Button 3 code */
	KEYPAD_Button_4 = 0x04,                     /*!< Button 4 code */
	KEYPAD_Button_5 = 0x05,                     /*!< Button 5 code */
	KEYPAD_Button_6 = 0x06,                     /*!< Button 6 code */
	KEYPAD_Button_7 = 0x07,                     /*!< Button 7 code */
	KEYPAD_Button_8 = 0x08,                     /*!< Button 8 code */
	KEYPAD_Button_9 = 0x09,                     /*!< Button 9 code */
	KEYPAD_Button_STAR = 0x0A,                  /*!< Button START code */
	KEYPAD_Button_HASH = 0x0B,                  /*!< Button HASH code */
	KEYPAD_Button_A = 0x0C,	                   /*!< Button A code. Only on large size */
	KEYPAD_Button_B = 0x0D,	                   /*!< Button B code. Only on large size */
	KEYPAD_Button_C = 0x0E,	                   /*!< Button C code. Only on large size */
	KEYPAD_Button_D = 0x0F,	                   /*!< Button D code. Only on large size */
	KEYPAD_Button_NOPRESS = KEYPAD_NO_PRESSED
} KEYPAD_Button_t;


static inline char *stringFromKeypadButton(KEYPAD_Button_t key)
{
    static char *strings[] = { 	"		KEYPAD_Button_0", "KEYPAD_Button_1", "KEYPAD_Button_2", "KEYPAD_Button_3",
										"KEYPAD_Button_4", "KEYPAD_Button_5", "KEYPAD_Button_6", "KEYPAD_Button_7",
										"KEYPAD_Button_8", "KEYPAD_Button_9", "KEYPAD_Button_STAR", "KEYPAD_Button_HASH",
										"KEYPAD_Button_A", "KEYPAD_Button_B", "KEYPAD_Button_C", "KEYPAD_Button_D"
    						};

    return strings[key];
}

uint8_t KEYPAD_READ(void);

void KEYPAD_Init(void);

void KEYPAD_Debounce_Delay(uint32_t debounceDelay);

#endif /*__KEYPAD_H__ */

```
---
**`keypad.c`**
``` C
#include "keypad.h"


uint8_t KEYPAD_INT_Buttons[4][4] = {
	{0x01, 0x02, 0x03, 0x0C},
	{0x04, 0x05, 0x06, 0x0D},
	{0x07, 0x08, 0x09, 0x0E},
	{0x0A, 0x00, 0x0B, 0x0F},
};

// initialize keypad
void KEYPAD_Init(void)
{

  GPIO_InitTypeDef GPIO_InitStruct = {0};

  /* GPIO Ports Clock Enable */
  __HAL_RCC_GPIOD_CLK_ENABLE();
  __HAL_RCC_GPIOA_CLK_ENABLE();
  __HAL_RCC_GPIOB_CLK_ENABLE();

  /*Configure GPIO pin Output Level */
  HAL_GPIO_WritePin(GPIOA, KEYPAD_ROW_4_Pin|KEYPAD_ROW_3_Pin|KEYPAD_ROW_2_Pin|KEYPAD_ROW_1_Pin, GPIO_PIN_SET);

  /*Configure GPIO pins : PAPin PAPin PAPin PAPin */
  GPIO_InitStruct.Pin = KEYPAD_COLUMN_4_Pin|KEYPAD_COLUMN_3_Pin|KEYPAD_COLUMN_2_Pin|KEYPAD_COLUMN_1_Pin;
  GPIO_InitStruct.Mode = GPIO_MODE_INPUT;
  GPIO_InitStruct.Pull = GPIO_PULLUP;
  HAL_GPIO_Init(GPIOB, &GPIO_InitStruct);

  /*Configure GPIO pins : PAPin PAPin PAPin PAPin */
  GPIO_InitStruct.Pin = KEYPAD_ROW_4_Pin|KEYPAD_ROW_3_Pin|KEYPAD_ROW_2_Pin|KEYPAD_ROW_1_Pin;
  GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
  GPIO_InitStruct.Pull = GPIO_NOPULL;
  GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
  HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);
}

// private function for "PULL DOWN SPECIFIC ROW"
void KEYPAD_INT_PullDownRow(uint8_t row)
{
	switch(row)
	{
	case 1:
		KEYPAD_ROW_HIGH_BUT_1;					// pull down row 1
		break;
	case 2:
		KEYPAD_ROW_HIGH_BUT_2;					// pull down row 2
		break;
	case 3:
		KEYPAD_ROW_HIGH_BUT_3;					// pull down row 3
		break;
	case 4:
		KEYPAD_ROW_HIGH_BUT_4;					// pull down row 4
		break;
	default:
		break;
	}
}

// private function for "check each column after pulling down row"
uint8_t KEYPAD_INT_CheckColumn(uint8_t row)
{
	if(KEYPAD_COLUMN_1_CHECK)
	{
		KEYPAD_Debounce_Delay(KEYPAD_DEBOUNCE_DELAYTIME);
		if(KEYPAD_COLUMN_1_CHECK)
		{
			return KEYPAD_INT_Buttons[row - 1][0];
		}
	}
	if(KEYPAD_COLUMN_2_CHECK)
	{
		KEYPAD_Debounce_Delay(KEYPAD_DEBOUNCE_DELAYTIME);
		if(KEYPAD_COLUMN_2_CHECK)
		{
			return KEYPAD_INT_Buttons[row - 1][1];
		}
	}
	if(KEYPAD_COLUMN_3_CHECK)
	{
		KEYPAD_Debounce_Delay(KEYPAD_DEBOUNCE_DELAYTIME);
		if(KEYPAD_COLUMN_3_CHECK)
		{
			return KEYPAD_INT_Buttons[row - 1][2];
		}
	}
	if(KEYPAD_COLUMN_4_CHECK)
	{
		KEYPAD_Debounce_Delay(KEYPAD_DEBOUNCE_DELAYTIME);
		if(KEYPAD_COLUMN_4_CHECK)
		{
			return KEYPAD_INT_Buttons[row - 1][3];
		}
	}
	return KEYPAD_NO_PRESSED;
}


uint8_t KEYPAD_READ(void)
{
	uint8_t row, check;
	for(uint8_t i = 0; i < 4; i++)
	{
		row = i + 1;
		KEYPAD_INT_PullDownRow(row);
		check = KEYPAD_INT_CheckColumn(row);
		if(check != KEYPAD_NO_PRESSED)
		{
			return check;
		}
	}
	return KEYPAD_NO_PRESSED;
}

__attribute__((weak)) void KEYPAD_Debounce_Delay(uint32_t debounceDelay)
{
	HAL_Delay(debounceDelay);
}
//

```
---
**`main.c`**
``` C
int main(void)
{
 
  HAL_Init();

 
  SystemClock_Config();

  
  KEYPAD_Init();
 
  while (1)
  {
    uint8_t key = KEYPAD_READ();
    if(key != KEYPAD_NO_PRESSED)
    {
        printf("key %s pressed\n", stringFromKeypadButton(key));
        while(KEYPAD_READ() != KEYPAD_NO_PRESSED);
    }
 
  }
}

```