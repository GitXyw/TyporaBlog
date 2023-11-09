之前看到有博文写Codesys程序编写标准中有一条，多个判断条件的`if-else-`语句，可能性最大的条件应放到最前面，这样可减少PLC处理的事件。但是根据测试，情况并非如此。下面的例子进行详细说明。
***
## AND、OR条件并列if-else语句
![Test if short circuit](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220828232818.png)

1. 第一个`if`语句中，第一个条件`1 > 2`显然等于FALSE, 但是PLC仍执行了后面的条件；
``` C
IF 1 > 2 AND (_xTestCondition := TRUE) THEN
	_iResult := 100;
END_IF
```
2. 第二个`if`语句中，第一个条件`2 > 1`已经等于TRUE， 但是PLC仍执行了后面的条件；
``` C
IF 2 > 1 OR (_xTestCondition1 := TRUE) THEN
	_iResult1 := 99;
END_IF
```
3. 第二个`if`语句中，第一个条件为TRUE， 但是PLC仍执行了后面的条件， 导致程序出现异常；
``` C
IF _iDenominator <> 0 AND _iNumerator / _iDenominator = 2 THEN
	;
END_IF
```
## AND_THEN、OR_THEN条件并列if-else语句
从以上例子可判断，像上面这样编写的if-else语句并不会像其他高级语言如C一样有short circuit的特性。
后来查阅资料，发现使用**AND_THEN**和**OR_ELSE**运算符可实现short circuit（短路）功能。  
> Operator ‘AND_THEN’ : This operator is an extension of the IEC 61131-3 standard.
The AND_THEN operator is permitted only for programming in structured text with the AND operation of BOOL and BIT operands with short-circuit evaluation.  
<br/>
Operate ‘OR_ELSE’ : This operator is an extension of the IEC 61131-3 standard.
The OR_ELSE operator is permitted only for programming in structured text: OR operation of BOOL and BIT operands; with short-circuit evaluation. 
***
下面用**AND_THEN**和**OR_ELSE**运算符实现上面几个语句。  
![](https://cdn.jsdelivr.net/gh/GitXyw/ImgHosting/MyBlog2022/20220828234642.png)
如上，当并列条件中的第一个条件满足时，后面的条件不再执行。这样既减少了程序处理的时间，也使代码更见简洁。  
## AND_THEN、OR_THEN语句的运用
1. 减少运行时间 —— 多条件并列时，按照判断条件的概率排列。
``` C
if Condition1 and_then Condition2 or_else Condition3 and_then ... then
    Execute();
end_if
```
2. 使代码更简洁
``` C
// before
IF _iDenominator <> 0 THEN
	IF _iNumerator / _iDenominator = 2 THEN
		Execute();
	END_IF
END_IF

// after
IF _iDenominator <> 0 AND_THEN _iNumerator / _iDenominator = 2 THEN
	Execute();
END_IF

------------------------------------------------------------------------

// before
IF (ptr <> 0) THEN
	IF (ptr^ = 99) THEN
		Execute();
	END_IF
END_IF

// after
IF (ptr <> 0) AND_THEN (ptr^ = 99) then
    Execute();
END_IF

```
## 结论
在Codesys st语言编程中使用`and`、`or`并列条件时，所有条件判断都会被执行。而使用`and_then`、`or_else`运算符，当条件确定满足时，后面的条件就不会被执行，即short-circuit。 使用[`and_then`](https://help.codesys.com/api-content/2/codesys/3.5.13.0/en/_cds_operator_and_then/)、[`or_else`](https://help.codesys.com/api-content/2/codesys/3.5.12.0/en/_cds_operator_or_else/)运算符的好处是减少CPU的处理时间，使代码风格更加干净简洁。