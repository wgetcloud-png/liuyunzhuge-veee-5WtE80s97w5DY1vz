
### 时序逻辑电路概述


* **时序逻辑电路分类：**
	+ 按照触发器的动作特点：
		- **同步时序逻辑电路：** 所有触发器的状态变化都是在同一个时钟信号作用下同时发生的
		- **异步时序逻辑电路：** 没有统一的时钟脉冲信号，各触发器状态的变化不在同一时间，而是有先后顺序
	+ 按照输出信号的特点：
		- **Mealy型：** 输出状态不仅与存储电路的状态有关，而且与外部的输入有关
		- **Moore型：** 输出状态仅与存储电路的状态有关，而且与外部输入无关


### 时序逻辑电路设计方法


* **时序逻辑电路设计要点**
	+ 只有时钟信号和复位信号可以放在敏感列表里，如果敏感变量列表中，有一个信号是边沿触发，那么所有信号都得使用边沿触发；
		- posedge：时钟上升沿触发
		- negedge：时钟下降沿触发
	+ 使用非阻塞赋值，即使用“\<\="进行赋值
	+ 不需要对所有分支进行描述，对于未描述的分支，变量将保持原值


### 时序逻辑电路设计实例


        不同结构功能和不同用途的触发器和锁存器，是基本的时序电路元件，是时序逻辑电路设计的基础


* **实例1：基本锁存器**



> **要求描述：**
>         基本锁存器电路是一个**电平触发型**的电路，当时钟clk为高电平时，其输出q的值才会随输入d的数据变化而更新；当时钟clk为低电平时，锁存器将保持原来高电平时锁存的值。
> 
> 
> ![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103120926575-1588971695.png)



```


|  | //实现代码 |
| --- | --- |
|  | module latch_1( |
|  | input clk, d, |
|  | output reg q); |
|  |  |
|  | always@(clk,d) |
|  | if(clk) |
|  | q<=d; |
|  |  |
|  | endmodule |
|  |  |
|  | //仿真代码 |
|  | `timescale 1ns/1ns |
|  | module latch_1_tb(); |
|  | reg clk, d; |
|  | wire q; |
|  |  |
|  | latch_1 U1(clk, d, q); |
|  | always #5 clk = ~clk; |
|  | initial begin |
|  | clk=0; d=1; |
|  | #20 d=0; |
|  | #20 d=1; |
|  | #20 $finish; |
|  | end |
|  | endmodule |


```

        仿真得到的波形图如下：


![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103124725437-1201443592.png)
        综合出的电路结构图如下：
![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103124320353-486064384.png)
* **实例2：含复位控制的锁存器**



> **要求描述：**
>         当rstn为低电平时，输出q的值复位为0；当rstn为高电平时，q的输出行为与基本锁存器一致。
> 
> 
> ![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103125934319-536397722.png)



```


|  | //实现代码 |
| --- | --- |
|  | module latch_2( |
|  | input clk, d, rstn, |
|  | output reg q); |
|  |  |
|  | always@(clk, d, rstn)	begin |
|  | if(~rstn)	q<=1'b0; |
|  | else if(clk)	q<=d; |
|  | end |
|  | endmodule |
|  |  |
|  | //仿真代码 |
|  | `timescale 1ns/1ns |
|  | module latch_2_tb(); |
|  | reg clk, d, rstn; |
|  | wire q; |
|  |  |
|  | latch_2 U1(clk, d, rstn, q); |
|  | always #5 clk = ~clk; |
|  | initial begin |
|  | rstn=0;d=0; |
|  | #10 rstn=1; d=0; |
|  | #10 rstn=1; d=1; |
|  | #10 rstn=1; d=0; |
|  | #10 rstn=1; d=1; |
|  | #10 rstn=0; |
|  | #10 $finish; |
|  | end |
|  | endmodule |


```

        仿真得到的波形图如下：


![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103130612244-1220770491.png)
        综合出的电路结构图如下：
![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103130838809-452661830.png)
* **实例3：基本D触发器**



> **要求描述：**
>         触发器是指边沿触发的存储单元，常见的有D型、JK型、T型，通常在时钟上升沿存储（更新）数据。其中D触发器是最常用的触发器，几乎所有的逻辑电路都可以描述成D触发器与组合逻辑电路
> 
> 
> ![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103133429823-87971298.png)



```


|  | //实现代码 |
| --- | --- |
|  | module dff_1( |
|  | input clk, d, |
|  | output reg q); |
|  |  |
|  | always@(posedge clk) |
|  | q<=d; |
|  |  |
|  | endmodule |
|  |  |
|  | //仿真代码 |
|  | `timescale 1ns/1ns |
|  | module dff_1_tb(); |
|  | reg clk, d; |
|  | wire q; |
|  |  |
|  | dff_1 U1(clk, d, q); |
|  | always #10 clk = ~clk; |
|  | initial begin |
|  | clk=0;	d=0; |
|  | #20 d=1; |
|  | #30 d=0; |
|  | #10 d=1; |
|  | #20 d=0; |
|  | #10 $finish; |
|  | end |
|  | endmodule |


```

        仿真得到的波形图如下：


![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103134038646-202974137.png)
        综合出的电路结构图如下：
![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103134246368-1584032136.png)
* **实例4：含异步复位信号的D触发器**



> **要求描述：**
>         异步复位信号reset的高电平能够在任意时刻复位D触发器，而不受时钟信号控制
> 
> 
> ![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103134429094-140174367.png)



```


|  | //实现代码 |
| --- | --- |
|  | module dff_2( |
|  | input clk, d, reset, |
|  | output reg q); |
|  |  |
|  | always@(posedge clk, posedge reset)	begin//敏感信号列表中只能同时出现脉冲边沿信号或者是电平信号，不能混合使用 |
|  | if(reset)	q<=1'b0; |
|  | else	q<=d; |
|  | end |
|  | endmodule |
|  |  |
|  | //仿真代码 |
|  | `timescale 1ns/100ps |
|  | module dff_2_tb(); |
|  | reg clk, d, reset; |
|  | wire q; |
|  |  |
|  | dff_2 U1(clk, d, reset, q); |
|  | always #5 clk=~clk; |
|  | initial begin |
|  | clk=0; reset=1; d=0; |
|  | #6 reset=0; d=1; |
|  | #6 reset=0; d=0; |
|  | #6 reset=0; d=1; |
|  | #6 reset=0; d=0; |
|  | #6 reset=0; d=1; |
|  | #6 reset=1; |
|  | #10 $finish; |
|  | end |
|  | endmodule |


```

        仿真得到的波形图如下：


![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103135339816-1047942361.png)
        综合出的电路结构图如下：
![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103135536348-1881376866.png)
* **实例5：含同步复位信号的D触发器**



> **要求描述：**
>         与时钟同步，同步复位信号reset的高电平只有当时钟信号有效时才起作用，而当时钟信号没有到来时，该控制信号不起作用
> 
> 
> ![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103140039985-1613543348.png)



```


|  | //实现代码 |
| --- | --- |
|  | module dff_3( |
|  | input clk, d, reset, |
|  | output reg q); |
|  |  |
|  | always@(posedge clk)	begin |
|  | if(reset)	q<=1'b0; |
|  | else	q<=d; |
|  | end |
|  | endmodule |
|  |  |
|  | //仿真代码 |
|  | `timescale 1ns/100ps |
|  | module dff_3_tb(); |
|  | reg clk, d, reset; |
|  | wire q; |
|  |  |
|  | dff_3 U1(clk, d, reset, q); |
|  | always #5 clk=~clk; |
|  | initial begin |
|  | clk=0; reset=1; d=0; |
|  | #6 reset=0; d=1; |
|  | #6 reset=0; d=0; |
|  | #6 reset=0; d=1; |
|  | #6 reset=0; d=0; |
|  | #6 reset=0; d=1; |
|  | #6 reset=1; |
|  | #10 $finish; |
|  | end |
|  | endmodule |


```

        仿真得到的波形图如下：


![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103135934873-659937140.png)
        综合出的电路结构图如下：
![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103140951480-2057017044.png)
* **实例6：含异步复位和同步使能的D触发器**



> **要求描述：**
>         更加实用的D触发器包含一个额外的控制信号en，能够控制触发器进行输入值采样。使能信号en只有在时钟上升沿来临时才会生效，所以他是同步信号，如果en没有置1，触发器将保持先前的值。
> 
> 
> ![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103141552271-136838870.png)



```


|  | //实现代码 |
| --- | --- |
|  | module dff_4( |
|  | input clk, d, reset, en, |
|  | output reg q |
|  | ); |
|  |  |
|  | always@(posedge clk, posedge reset)	begin |
|  | if(reset)	q<=1'b0; |
|  | else if(~en)	q<=q; |
|  | else	q<=d; |
|  | end |
|  | endmodule |
|  |  |
|  | //仿真代码 |
|  | module dff_4_tb(); |
|  | reg clk, d, reset, en; |
|  | wire q; |
|  |  |
|  | dff_4 U1(clk, d, reset, en, q); |
|  | always #5 clk=~clk; |
|  |  |
|  | initial begin |
|  | clk=0; reset=1; en=0; d=0; |
|  | #8 reset=0; |
|  | #4 en=1; |
|  | #5 d=1; |
|  | #10 d=0; |
|  | #10 d=1; |
|  | #10 d=0; |
|  | #10 en=0; d=1; |
|  | #3 en=1; reset=1; |
|  | #10 $finish; |
|  | end |
|  | endmodule |


```

        仿真得到的波形图如下：


![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103142320650-1641624675.png)
        综合出的电路结构图如下：
![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103142513772-1230562735.png)

> D触发器的使能常用于**实现同步快子系统和慢子系统**：
>         假设快子系统和慢子系统的时钟频率分别为50MHz和1MHz。我们可以生成一个周期性的使能信号，每50个时钟周期使能一个时钟周期，而不是另外派生出一个1MHz的时钟信号来驱动慢子系统，慢子系统在其余49个时钟周期中是保持原来状态的。



> **锁存器和触发器的区别：**
> 
> 
> * **锁存器：** 没有时钟输入端，对脉冲电平敏感的存储电路，在特定输入脉冲电平作用下改变状态；
> * **触发器：** 每一个触发器有一个时钟输入端，对脉冲边沿敏感的存储电路，在时钟脉冲的上升沿或下降沿的变化瞬间改变状态。
> **①**锁存器消耗的门资源比DFF要少；
> **②**ASIC中会使用一定数量的锁存器；而FPGA中几乎没有标准的锁存器单元，更多的是触发器；
> **③**锁存器会让时序变得极为复杂，静态时序分析非常困难。故在绝大多数设计中应尽量避免产生锁存器，能使用触发器则不使用锁存器。


* **实例7：1位寄存器**



> **要求描述：**
>         一个触发器构成一个一位寄存器；N个触发器级联，构成一个N位寄存器。为了设计一个一位寄存器，可以在需要时从输入线in\_data加载一个值，我们给D触发器增加一根使能信号load，当想要从in\_data加载一个值时，就把load设置为1，那么在下一个时钟上升沿到来时，in\_data的值将被存储在q中。
>         本质上，一位寄存器就是一个异步复位和同步使能的触发器



```


|  | //实现代码 |
| --- | --- |
|  | module reg_1( |
|  | input clk, in_data, reset, load, |
|  | output reg q); |
|  |  |
|  | always@(posedge clk, posedge reset)	begin |
|  | if(reset)	q<=1'b0; |
|  | else if(load)	q<=in_data; |
|  | end |
|  | endmodule |
|  |  |
|  | //仿真代码 |
|  | `timescale 1ns/1ns //默认值是1ns/1ps |
|  | module reg_1_tb(); |
|  | reg clk, in_data, reset, load; |
|  | wire q; |
|  |  |
|  | reg_1 U1(clk, in_data, reset, load, q); |
|  | always #10 clk=~clk; |
|  |  |
|  | initial begin |
|  | clk=0; reset=1; load=0; in_data=0; |
|  | #5 reset=0; |
|  | #5 load=1; |
|  | #5 in_data=1; |
|  | #5 in_data=0; |
|  | #10 in_data=1; |
|  | #5 load=0; |
|  | #5 load=1;reset=1; |
|  | #10 $finish; |
|  | end |
|  | endmodule |


```

        仿真得到的波形图如下：


![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103144653643-652148896.png)
        综合出的电路结构图如下：
![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103144932340-1628661149.png)
* **实例8：N位寄存器**



> **要求描述：**
>         把N个1位寄存器模块组合起来，就可以构成一个N位寄存器
> 
> 
> ![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103145814025-967453979.png)



```


|  | //实现代码 |
| --- | --- |
|  | module reg_2#(parameter N=4)( |
|  | input clk, reset, load, |
|  | input [N-1:0] in_data, |
|  | output reg [N-1:0] q); |
|  |  |
|  | always@(posedge clk, posedge reset)	begin |
|  | if(reset)	q<=1'b0; |
|  | else if(load)	q<=in_data; |
|  | end |
|  | endmodule |
|  |  |
|  | //仿真代码 |
|  | `timescale 1ns/100ps |
|  | module reg_2_tb#(parameter N=4)(); |
|  | reg clk, reset, load; |
|  | reg [N-1:0] in_data; |
|  | wire [N-1:0] q; |
|  |  |
|  | reg_2 U1(clk, reset, load, in_data, q); |
|  | always #1 clk=~clk; |
|  |  |
|  | initial begin |
|  | clk=0; reset=1; load=0; in_data=4'b0000; |
|  | #5 reset=1; |
|  | #5 load=1; |
|  | #5 in_data = 4'b0001; |
|  | #5 in_data = 4'b0010; |
|  | #5 in_data = 4'b0100; |
|  | #5 in_data = 4'b1000; |
|  | #5 load = 0; |
|  | #5 reset=1; |
|  | #10 $finish; |
|  | end |
|  | endmodule |


```

        仿真得到的波形图如下：


![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103150713255-301634625.png)
        综合出的电路结构图如下：
![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103150927561-1821092981.png)
* **实例9：具有同步预置功能的8位移位寄存器**



> 将若干个D触发器串接级联在一起构成的具有移位功能的寄存器
> 移位寄存器的分类：
> 
> 
> * 按照**移动方式**分类：
> 	+ 单向移位寄存器：左移位寄存器/右移位寄存器
> 	+ 双向移位寄存器
> * 按照**输出方式**分类：
> 	+ 串入串出
> 	+ 串入并出
> 
> 
> ![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103152439937-449522293.png)
> **要求描述：**
>         当时钟上升沿到来时，过程被启动，如果此时预置使能端口load为高电平，则输入端口din的8位二进制数被同步并行移入寄存器，用作串行右移的初始值；
>         如果此时预置使能端口load为低电平，则执行赋值语句：reg8\[6:0]\<\=reg8\[7:1]，这样完成一个时钟周期后，将把上一时钟周期的高七位值更新至此寄存器的低七位，实现右移一位的操作，连续赋值语句把移位寄存器最低为通过qb端口输出。



```


|  | //实现代码 |
| --- | --- |
|  | module reg_3( |
|  | input clk, load, |
|  | input [7:0] din, |
|  | output qb |
|  | ); |
|  |  |
|  | reg [7:0] reg8; |
|  | always@(posedge clk)	begin |
|  | if(load)	reg8<=din; |
|  | else	reg8[6:0]<=reg8[7:1]; |
|  | end |
|  |  |
|  | assign qb=reg8[0]; |
|  | endmodule |
|  |  |
|  | //仿真代码 |
|  | `timescale 1ns/100ps |
|  | module reg_3_tb(); |
|  | reg clk, load; |
|  | reg [7:0] in; |
|  | wire qb; |
|  |  |
|  | reg_3 U1(clk, load, in, qb); |
|  | always #1 clk=~clk; |
|  |  |
|  | initial begin |
|  | clk=0; load=0; in=8'b11011010; |
|  | #5 load=1; |
|  | #1 load=0; |
|  | #10 load=1; in=8'b10101010; |
|  | #5 load=0; |
|  | #10 $finish; |
|  | end |
|  | endmodule |
|  |  |


```

        仿真得到的波形图如下：


![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103154909051-2111168524.png)
        综合出的电路结构图如下：
![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103155212090-1444294673.png)
* **实例10：4位双向移位寄存器**



> **要求描述：**
>         功能如下图，试用功能描述风格对其建模
> 
> 
> ![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103155356208-1443581938.png)



```


|  | //实现代码 |
| --- | --- |
|  | module reg_4( |
|  | input clk, clr, dsl, dsr, s1, s0, |
|  | input [3:0] din, |
|  | output reg [3:0] q |
|  | ); |
|  |  |
|  | always@(posedge clk, negedge clr)	begin |
|  | if(~clr)	q<=4'b0000; |
|  | else	begin |
|  | case({s1, s0}) |
|  | 2'b00: q<=q; |
|  | 2'b01: q<={dsr,q[3:1]}; |
|  | 2'b10: q<={q[2:0],dsl}; |
|  | 2'b11: q<=din; |
|  | endcase |
|  | end |
|  | end |
|  | endmodule |
|  |  |
|  | //仿真代码 |
|  | `timescale 1ns/100ps |
|  | module reg_4_tb(); |
|  | reg clk, clr, dsl, dsr, s1, s0; |
|  | reg [3:0] din; |
|  | wire [3:0] q; |
|  |  |
|  | reg_4 U1(clk, clr, dsl, dsr, s1, s0, din, q); |
|  | always #1 clk=~clk; |
|  |  |
|  | initial begin |
|  | clk=0; clr=0; dsl=0; dsr=0; s1=0; s0=0; din=4'b1111; |
|  | #5 clr=1; s1=1; s0=1; |
|  | #5 s1=0; s0=1; |
|  | #5 s1=1; s0=0; dsl=1; |
|  | #10 clr=0; |
|  | #5 $finish; |
|  | end |
|  | endmodule |


```

        仿真得到的波形图如下：


![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103160750893-708208423.png)
        综合出的电路结构图如下：
![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103161048011-1951860272.png)
* **实例11：模M计数器**



> 计数器的基本功能是对输入时钟脉冲进行计数，它也可以用于分频、定时、产生节拍脉冲和脉冲序列以及进行数字运算等。
>         计数器的分类如下：
> 
> 
> ![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103161403978-1979001660.png)
> **要求描述：**
>         模M计数器的计数值从0增加到M\-1，然后循环。其中参数M指定了计数模值，参数N指定了计数器所需的位数，采用高电平同步复位。



```


|  | //实现代码 |
| --- | --- |
|  | module counter_mod_m #( |
|  | parameter M=10, |
|  | parameter N=4 |
|  | )( |
|  | input clk, reset, |
|  | output reg [N-1:0] qd, |
|  | output cout //进位信号 |
|  | ); |
|  |  |
|  |  |
|  |  |
|  | always@(posedge clk)	begin |
|  | if(reset) |
|  | qd<=0; |
|  | else if(qd1) |
|  | qd<=qd+1; |
|  | else |
|  | qd<=0; |
|  | end |
|  |  |
|  | assign cout = (qd==(M-1))? 1'b1:1'b0; |
|  | endmodule |
|  |  |
|  | //仿真代码 |
|  | `timescale 1ns/100ps |
|  | module counter_mod_m_tb(); |
|  | reg clk, reset; |
|  | wire cout; |
|  | wire [3:0] qd; |
|  |  |
|  | counter_mod_m U1(clk, reset, qd, cout); |
|  |  |
|  | always #1 clk=~clk; |
|  | initial begin |
|  | clk=0; reset=1; |
|  | #5 reset=0; |
|  | #20 reset=1; |
|  | #5 $finish; |
|  | end |
|  | endmodule |


```

        仿真得到的波形图如下：


![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103163116700-35463523.png)
        综合出的电路结构图如下：
![](https://img2024.cnblogs.com/blog/2326690/202501/2326690-20250103163409809-630989448.png)
 本博客参考[veee加速器](https://blog.liuyunzhuge.com)。转载请注明出处！
