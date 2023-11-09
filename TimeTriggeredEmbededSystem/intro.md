## RT Systems

### essential features

* rapid respond  -- process inputs and generates outputs very rapidly.
* time scale measured in milliseconds
* deterministic processing   --  guarantee that a particular activity will be completed within a precise interval.
* reliability

### interrupt

![image-20230613135103848](https://blog-pic-1313935212.cos.ap-guangzhou.myqcloud.com/imgs/202306131351896.png)

*The interrupt service routine (ISR) invoked by a low-priority interrupt can be interrupted by a high-priority interrupt. In this case, the low-priority ISR will be paused, to allow the high-priority ISR to be executed, after which the operation of the low-priority ISR will be completed.* 

**interrupt responses can be missed**