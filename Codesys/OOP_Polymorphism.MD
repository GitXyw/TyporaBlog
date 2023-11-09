## 多态-Polymorphism
Polymorphism 由前缀**polu**（many,表示'多'）和**morphe**（form,“形态”）构成，  多态是面向对象编程中的一个重要概念。多态即指为不同数据类型的实体提供统一的接口。 
> 计算机程序运行时，相同的消息可能会送给多个不同的类别之对象，而系统可依据对象所属类别，引发对应类别的方法，而有不同的行为。简单来说，所谓多态意指相同的消息给予不同的对象会引发不同的动作。  

根据处理时间的不同，多态还可分为**动态多态**和**静态多态**  
- 动态多态 dynamic polymorphism : 多态生效于运行期；
- 静态多态 static polymorphism : 多态生效于编译器， codesys程序不支持此种多态；
***  

## Codesys实现
### 一、 Interface方式

将几个不同的实体共有的属性新建为一个接口（Interface），每个实体都实现（implement）该接口。  
例如，矩形和椭圆都有面积属性，则矩形和椭圆类都实现计算面积的接口。接口中只定义共有的方法和属性，方法具体的实现放在实现接口的实体中。  
1. 首先，我们添加一个Interface, 用于体现两个实体的共同特性;  
![add an interface](https://img2022.cnblogs.com/blog/2945242/202208/2945242-20220828173535423-204106040.gif)
在Interface中添加计算面积的方法mArea和属性pParameter1和pParameter2;  
![add method and property](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220828174341.png)  
mArea只有申明，无任何实现内容。Property通过**get**和**set**方法，使外界可与封装数据进行交互。Property特性是IEC标准的扩展，用于面向对象编程。  

2. 创建“Rectangle”和“Ellipse” FB。类似面向对象编程中的对象，创建时实现ITF_Area接口。 
![](https://img2022.cnblogs.com/blog/2945242/202208/2945242-20220828180559795-766869272.gif)
创建完成后，可以看到这两个FB中都有了接口中定义的方法和属性。  
3. 在FB_Ellipse类型的变量表中增加两个内部变量  _rParameter1和_rParameter2。

```
----------------------------------------------------------
FUNCTION_BLOCK FB_Ellipse IMPLEMENTS ITF_Area
VAR_INPUT
END_VAR
VAR_OUTPUT
END_VAR
VAR
	_rParameter1		:REAL;
	_rParameter2		:REAL;
END_VAR
----------------------------------------------------------
```  
4. 编写属性的get和set方法, 外部将通过这两个方法来读取和写入上述两个变量。  
```
GET:
pParameter1 := _rParameter1;
```
```
SET:
_rParameter1 := pParameter1;
```
5. 实现mArea方法。  
```
_rTempArea 		:= pParameter1 * pParameter2 * 3.14;
mArea 			:= CONCAT('Area of this ellipse is ', TO_STRING(_rTempArea));

```
6. 重复以上步骤，实现FB_Rectangle的方法，不过需更改计算面积的实现方式。
```
_rTempArea 		:= pParameter1 * pParameter2;
mArea 			:= CONCAT('Area of this rectangle is ', TO_STRING(_rTempArea));
```
7. 添加面积计算函数，输入ITF_Area和两个参数，返回String 面积信息。  
```
----------------------------------------------------------
FUNCTION F_Area : String
VAR_INPUT
	IN_ITF_Shape		:ITF_Area;
	IN_rParameter1		:REAL;
	In_rParameter2		:REAL;
END_VAR
VAR
END_VAR
----------------------------------------------------------


IN_ITF_Shape.pParameter1 := IN_rParameter1;
IN_ITF_Shape.pParameter2 := In_rParameter2;
F_Area := IN_ITF_Shape.mArea();
```
8. 添加程序，监控执行情况
```
----------------------------------------------------------
PROGRAM POU_TestPolymorphism
VAR
	_strRes							:STRING;
	_xCalculateRectangleArea		:BOOL;
	_xCalculateEllipseArea			:BOOL;
	_fbEllipse1						:FB_Ellipse;
	_fbRectangle1					:FB_Rectangle;
END_VAR

----------------------------------------------------------

IF _xCalculateEllipseArea THEN
	_xCalculateEllipseArea := FALSE;
	_strRes := F_Area(_fbEllipse1, 5.0, 6.0);	
END_IF


IF _xCalculateRectangleArea THEN
	_xCalculateRectangleArea := FALSE;
	_strRes := F_Area(_fbRectangle1, 3.0, 5.0);	
END_IF

```
![](https://img2022.cnblogs.com/blog/2945242/202208/2945242-20220828184342961-1678399517.gif)
可以看到，通过同一个函数，对多种数据类型的对象的面积进行了计算。函数调用时会自动调用到对应的功能块。FB_Ellipse和FB_Rectangle都实现了ITF_Area接口，所以在向F_Area函数传参时，可以将这两种数据类型传入。

### 二、 Abstract类——抽象类实现  
