---
title: Verilog Demo_15 SDRAM
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
layout: 
summary: 本文整理 SDRAM 的相关知识
categories: ☸ FPGA
tags:
  - Verilog
  - sdram
abbrlink: 3505248845
date: 2020-03-29 15:46:09
password:
---

## SDRAM 原理

---

注 : 本文整理摘抄复制 SDRAM 相关内容， [SDRAM原理参考文章](http://dengkanwen.com/105.html)、[参考文2](https://www.cnblogs.com/JissXbon/p/6947279.html)、[参考文3](https://blog.csdn.net/eydwyz/article/details/52945642)、[参考文4](https://blog.csdn.net/lusics/article/details/53646233)、[参考文5](https://www.cnblogs.com/chengqi521/p/7909437.html)、[参考文6](http://www.chinastor.com/baike/storage/020I62322017.html)

### 存储器介绍

SRAM、DRAM、SDRAM、DDR3、DDR4介绍及区别

**SRAM**：静态随机访问存储器

一个SRAM存储单元由4个晶体管和2个电阻器组成，利用晶体管的状态切换来存储数据，而不是电容器，因此读数据时不存在漏电问题，不需要刷新操作。但是由于SRAM需要的晶体管数多，因此成本高。

**DRAM**：动态随机访问存储器

一个DRAM存储单元由1个晶体管和1个电容器组成，利用电容量存储电量的多少来存储数据，由于电容器存在漏电问题，因此需要定期刷新。读数据时，电容量的电量会消失，因此每次访问之后，也需要刷新，以防止数据丢失。

**SDRAM**：同步动态随机访问存储器

同步是指 Memory工作需要同步时钟，内部的命令的发送与数据的传输都以它为基准。传统的DRAM在两个读周期之间需要等待一段时间，用于充电操作。而SDRAM一个模组有两个bank，在对一个bank充电时，可以操作另一个bank，实现流水线。

SDRAM的发展已经经历了五代：分别是SDR SDRAM、 DDR SDRAM、 DDR2 SDRAM、 DDR3 SDRAM、 DDR4 SDRAM。

DDR3：2005年制造出DDR3的原型产品。2007年，市场开始使用DDR3内存芯片。

与DDR2相比，DDR3采用8bit预取，因此提供更高的传输速率(2133MT/s)；更低的工作电压（1.5V，DDR2工作电压为1.8V），另外采用了不同的封装工艺，因此能耗更低。

延迟周期同样比DDR2增长，引脚数增加。

DDR4：2011年，samsung宣布生产出第一个DDR4内存模块。2012年，samsung、Micron等开始量产DDR4 SDRAM芯片。

与DDR3相比，DDR4提供更高的时钟频率和传输速率（4166MT/s）；更低的工作电压（1.05~1.2V），因此能耗更低。
转自中国存储网，原文链接：http://www.chinastor.com/baike/storage/020I62322017.html

### SDRAM介绍

SDRAM（Synchronous Dynamic Random Access Memory），同步动态随机存储器。同步是指 Memory工作需要同步时钟，内部的命令的发送与数据的传输都以它为基准；动态是指存储阵列需要不断的刷新来保证存储的数据不丢失，因为SDRAM中存储数据是通过电容来工作的，大家知道电容在自然放置状态是会有放电的，如果电放完了，也就意味着SDRAM中的数据丢失了，所以SDRAM需要在电容的电量放完之前进行刷新；随机是指数据不是线性依次存储，而是自由指定地址进行数据的读写。

### SDRAM的内部结构

SDRAM内部其实是一个存储阵列，阵列就如同一张表格一样，将数据填进去。和表格的检索原理一样，先确定一个行（Row），再确定一个列（Col），我们就可以准确的找到所需要的单元格，这就是内存芯片寻址的基本原理。对于内存，这个单元格可以成为存储单元，那么这个表格（存储阵列）就是逻辑Bank (Logic Bank,下文简称Bank).

<img src="/images/post_images/verilog_demo_15_sdram/sdram_01.png">

对SDRAM进行寻址的方式为：先确定Bank的地址，再确定行和列的地址。

### SDRAM内存容量计算方式

**128Mb Synchronous DRAM based on 2M x 4Bank x16 I/O**

128Mb表示SDRAM的总容量，2M：表示1个Bank共有2M个地址，也就是说一个bank有2M个存储单元；4Bank表示有4个Bank；16 I/O表示位宽为16位，数据总线用的是16位的，1个存储单元能存储的最大的数为2^16-1=65535。然后再根据各个参数算下容量：2M x 4 x 16b = 128Mb .

### SDRAM芯片主要引脚介绍

| 引脚         | 介绍                                                         |
| ------------ | ------------------------------------------------------------ |
| CLK          | SDRAM工作的时钟，并且所有的输入信号都是在CLK的上升沿进行检测的，也就是说我们给SDRAM给的任何命令，一定要在CLK的上升沿保持稳定，以免SDRAM获取我们给出的命令时出现错误。 |
| CKE          | 时钟使能信号，是用来控制SDRAM内部时钟是否工作的一个信号（在SDRAM内部也是有时钟的哦） |
| CS           | 片选信号，这里需要注意的是，如果要对SDRAM进行操作，必须要将片选信号拉低 |
| BA0，BA1     | Bank地址线，用来给bank的地址，可以控制SDRAM的4个bank         |
| A0～A11      | 地址线，当我们选择SDRAM某个Bank的Row地址的时候，需要到12根地址线（A0～A11）；当选择Col地址的时候，只用A0～A8这9根线；A10这个信号可以用来控制Auto-precharge。 |
| RAS、CAS、WE | 这三根线就是用来给SDRAM发命令的，包括初始化、读、写、自动充电等命令。 |
| UDQM、LDQM： | 数据输入/输出掩码。                                          |
| DQ0～DQ15    | SDRAM的数据线，为双向的，向SDRAM写数据或者从SDRAM中读出来的数据都是在DQ上进行传输的 |

大家看到上面的A0～A11这根地址总线既可以来控制row也可以控制col，而且控制col地址与控制row地址用到的线的数量又不一样，可能会有些疑问，下面解释一下：

SDRAM的厂商一般为了节约成本，采用同一总线来对SDRAM进行寻址是无可厚非的，对于Row地址用到了12根线，也就是总共有2^12=4096个Row地址，而col地址使用9根线，也就是有2^9=512个col地址，而总共加起来的话，一个Bank就有4096×512=2097152 (2x1024x1024)  ,也就是有2M个地址，这样的话，就与我们前面计算SDRAM的容量想吻合了。

**掩码（UDQM、LDQM）的作用**

就拿咱们使用的这块SDRAM芯片来讲，数据线有16根，也就是说明我们数据的位数可以达到16位，但是呢，请注意，也许在我们使用SDRAM的时候，也许我们在向SDRAM写数据的时候，我们生成的数据只有8位，但FPGA是与SDRAM的16根数据线连在一起的，这个时候，存到SDRAM中的数据还是16位的，所以为了避免这个问题，我们就可以使用掩码来屏蔽掉高8位了。当然掩码在读数据的时候起到的作用也是类似的。

## SDRAM驱动

### SDRAM相关操作时序

#### SDRAM初始化

先找到SDRAM最开始的状态“POWER ON”，这是刚上电的状态。在“POWER ON”状态给‘Precharge’命令之后就会跳转到“Precharge”状态，然后自动跳转至“IDLE”状态。在“IDLE”状态下，我们需要给SDRAM两次Auto-refresh命令，然后接着需要进行模式寄存器设置，对模式寄存器设计完毕之后，我们的初始化过程也就结束了。

<img src="/images/post_images/verilog_demo_15_sdram/sdram_02.png">

#### SDRAM写操作

在状态转移图中，我们可以发现在初始化完成之后，对SDRAM进行读或者写操作之前，我们还需要有一个命令”ACT”（这个命令在我们的初始化时序图中也出现了，只是我们是拿到了这里来讲），这个命令的意思说成大白话就是”行有效”命令，就是让SDRAM中的某一行活动起来，以便我们进行读或写。

在正式讲写操作之前，还有一点希望大家能注意一下，可能细心的朋友已经发现了，在我们的状态转移图中有”WRITE”和”WRITEA”这两个状态，处于这两个状态时，我们都可以对SDRAM进行写操作，只是说在”WRITEA”状态，我们每写完一个突发长度的数据之后，SDRAM会自动跳出这个状态进行刷新，而在”WRITE”状态，是需要给相应的指令之后才会跳出”WRITE”状态的，所以为了提高SDRAM的运行速度，我们一般采用不让SDRAM进入”WRITEA”状态来提高速度。当然”READ”和”READA”这两个状态的区别也是这样的。

<img src="/images/post_images/verilog_demo_15_sdram/sdram_03.png">

#### SDRAM读操作

下面来说说我们的读操作，其实读操作和写操作是类似的，

<img src="/images/post_images/verilog_demo_15_sdram/sdram_04.png">

#### SDRAM的自动刷新操作

前面多次提到了SDRAM的自动刷新，SDRAM为什么要自动刷新，相信大家通过前面的介绍已经知道原因了，可以说如果SDRAM到了该刷新的时间没有进行刷新，那不管我们给SDRAM写进去了多少数据都是白搭，所以大家一定要高度重视SDRAM的刷新操作，到了该刷新的时候就一定要给刷新的命令。SDRAM内部电容保存数据的最长时间是64ms，而我们一个BANK有4096行，64ms/4096～=15us，也就是说为了保证SDRAM内部的数据不被丢失，两次刷新之间的最大时间间隔为15us，所以为了能让SDRAM有更多的时间进行读或者写，我们就设定SDRAM刷新的周期为15us.

在这里补充一个小知识，SDRAM每进行一次刷新，是对每一行进行操作的，并不是单独针对每一个电容进行充电，所以每进行一次刷新，该行中的电容进行充电我们可以理解为是同步发生的。

<img src="/images/post_images/verilog_demo_15_sdram/sdram_05.png">

### SDRAM驱动原理

SDRAM是每隔15us进行刷新一次，但是如果当SDRAM需要进行刷新时，而SDRAM正在写数据，这两个操作之间怎么进行协调呢？因为我们是肯定需要保证写的数据不能丢失，所以，我们可以考虑这样来做：如果刷新的时间到了，先让写操作把正在写的4个数据（突发长度为4）写完，然后再去进行刷新操作。而如果在执行读操作也遇到需要刷新的情况，我们也可以这样来做，先让数据读完，再去执行刷新操作。

大家看完可能会想，说是这么说，那代码怎么来写呢？似乎还是没什么思路。大家可以想象一下，我们写的SDRAM控制器是肯定包括初始化、读操作、写操作及自动刷新这些操作的，既然这样，我们就可以给每一个操作写上一个模块独立开来，这样也便于我们每个模块的调试，显然这种思路是正确的。那怎么让我们的各个模块工作起来呢，虽然都是独立的模块，但很显然这几个模块之间又是相互关联的。就拿上面刚才说的那个情况来讲，如果SDRAM需要刷新了，而SDRAM却正在执行写操作，那我们刷新模块与写模块之间怎么进行控制呢？这个问题解决了，读模块与刷新模块之间的这个问题也可以很轻松的解决。

<img src="/images/post_images/verilog_demo_15_sdram/sdram_06.png">

<img src="/images/post_images/verilog_demo_15_sdram/sdram_07.png">

在仲裁模块中，初始化操作完成之后便进入到了“ARBIT”仲裁状态，只有处于仲裁状态的时候，仲裁机才能向其他模块发送命令。

当状态机处于“WRITE”写状态时，如果SDRAM刷新的时间到了，刷新模块同时向写模块和仲裁模块发送刷新请求ref_req信号，当写模块接受到ref_req之后，写模块在写完当前4个数据（突发长度为4）之后，写模块的写结束标志flag_wr_end拉高，然后状态机进入“ARBIT”仲裁状态；

处于仲裁状态之后，此时有刷新请求ref_req，然后状态机跳转到“AREF”状态并且仲裁模块发送ref_en刷新使能，然后刷新模块将刷新请求信号ref_req拉低并给sdram发送刷新的命令。

等刷新完毕之后，刷新模块给仲裁模块发送flag_ref_end刷新结束标志，状态机跳转到“ARBIT”仲裁状态。

当刷新完跳转到“ARBIT”仲裁状态之后，如果之前全部数据仍然没有写完（指的是全部数据，并不是一个突发长度的4个数据），那么此时仍然要给仲裁模块写请求“wr_req”，然后仲裁模块经过一系列判断之后，如果符合写操作的时机，那就给写模块一个写使能信号“wr_en”，然后跳转到“WRITE”写状态并且写模块开始工作。

### 仲裁模块代码

```verilog
//state
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            state    <=    IDLE;
        else case(state)
            IDLE:
                if(key[0] == 1'b1)
                    state    <=    INIT;
                else
                    state    <=    IDLE;
            INIT:
                if(flag_init_end == 1'b1)    //初始化结束标志
                    state    <=    ARBIT;
                else
                    state    <=    INIT;
            ARBIT:
                if(ref_req == 1'b1)    //刷新请求到来且已经写完
                    state    <=    AREF;
                else if(ref_req == 1'b0 && rd_en == 1'b1)    //默认读操作优先于写操作
                    state    <=    READ;            
                else if(ref_req == 1'b0 && wr_en == 1'b1)    //无刷新请求且写请求到来
                    state    <=    WRITE;
                else
                    state    <=    ARBIT;
            AREF:
                if(flag_ref_end == 1'b1)
                    state    <=    ARBIT;
                else
                    state    <=    AREF;
            WRITE:
                if(flag_wr_end == 1'b1)
                    state    <=    ARBIT;
                else
                    state    <=    WRITE;
            READ:
                if(flag_rd_end == 1'b1)
                    state    <=    ARBIT;
                else
                    state    <=    READ;
            default:
                state    <=    IDLE;            
        endcase
```



### 初始化模块代码

首先，我们需要有200us的稳定期，所以我们便有了一个200us的计数器cnt_200us，而这个计数器是根据flag_200us的低电平来工作的。

falg_200us在200us计时之后一直拉高。在200us计满，即flag_200us拉高之后，我们就需要先给一个“NOP”命令，然后给两次“Precharge”命令，同时选中ALL Banks。

```verilog
module    sdram_init(
        input    wire        sclk,        //系统时钟为50M，即T=20ns
        input    wire        s_rst_n,
        
        output    reg    [3:0]    cmd_reg,    //sdram命令寄存器
        output    reg    [11:0]    sdram_addr,    //地址线
        output    reg    [1:0]    sdram_bank,    //bank地址
        output    reg        flag_init_end    //sdram初始化结束标志    
        );
        
    parameter    CMD_END        =    4'd11,        //初始化结束时的命令计数器的值
            CNT_200US    =    14'd1_0000,    
            NOP        =    4'b0111,    //空操作命令
            PRECHARGE    =    4'b0010,    //预充电命令
            AUTO_REF    =    4'b0001,    //自刷新命令
            MRSET        =    4'b0000;    //模式寄存器设置命令
 
    reg    [13:0]    cnt_200us;        //200us计数器
    reg        flag_200us;        //200us结束标志（200us结束后，一直拉高）
    reg    [3:0]    cnt_cmd;        //命令计数器，便于控制在某个时候发送特定指令
    reg        flag_init;        //初始化标志：初始化结束后，该标志拉低
//flag_init 
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            flag_init    <=    1'b1;
        else if(cnt_cmd == CMD_END)
            flag_init    <=    1'b0;    
//cnt_200us    
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            cnt_200us    <=    14'd0;
        else if(cnt_200us == CNT_200US)
            cnt_200us    <=    14'd0;
        else if(flag_200us == 1'b0)
            cnt_200us    <=    cnt_200us + 1'b1;
//flag_200us
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            flag_200us    <=    1'b0;
        else if(cnt_200us == CNT_200US)
            flag_200us    <=    1'b1;
//cnt_cmd
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            cnt_cmd    <=    4'd0;
        else if(flag_200us == 1'b1 && flag_init == 1'b1)
            cnt_cmd    <=    cnt_cmd + 1'b1;
//flag_init_end
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            flag_init_end    <=    1'b0;
        else if(cnt_cmd == CMD_END)
            flag_init_end    <=    1'b1;
        else
            flag_init_end    <=    1'b0;
//cmd_reg
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            cmd_reg    <=    NOP;
        else if(cnt_200us == CNT_200US)
            cmd_reg    <=    PRECHARGE;
        else if(flag_200us)
            case(cnt_cmd)
                4'd0:
                    cmd_reg    <=    AUTO_REF;    //预充电命令
                4'd6:
                    cmd_reg    <=    AUTO_REF;
                4'd10:
                    cmd_reg    <=    MRSET;        //模式寄存器设置
                default:
                    cmd_reg    <=    NOP;
            endcase
//sdram_addr
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            sdram_addr    <=    12'd0;
        else case(cnt_cmd)
            4'd0:
                sdram_addr    <=    12'b0100_0000_0000;    //预充电时，A10拉高，对所有Bank操作
            4'd10:
                sdram_addr    <=    12'b0000_0011_0010;    //模式寄存器设置时的指令:CAS=2,Burst Length=4;
            default:
                sdram_addr    <=    12'd0;
        endcase
//sdram_bank
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            sdram_bank    <=    2'd0;    //这里仅仅只是初始化，在模式寄存器设置时才会用到且其值为全零，故不赋值
 
//sdram_clk
    assign    sdram_clk    =    ~sclk;
            
endmodule
```

### 写操作模块代码

在模块端口列表中，用key_wr来接收写请求信号，这个写请求信号，是在没有写完之前一直拉高的，在写完了全部数据之后才拉低的。

在写模块中，让SDRAM循环着写16’h0012，16’h1203，16’h562f，16’hfe12这四个数据。

另外一点，在这个写模块中，每写完4个数据，也就是突发结束后，有一个写完标志，从而使状态机跳转到仲裁状态，然后如果数据没写完，由于写请求是拉高的，所以如果此时没有刷新请求，那状态机还是会跳转到写状态继续写的。

```verilog
module    sdram_write(
        input    wire        sclk,
        input    wire        s_rst_n,
        input    wire        key_wr,
        input    wire        wr_en,        //来自仲裁模块的写使能
        input    wire        ref_req,    //来自刷新模块的刷新请求
        input    wire    [5:0]    state,        //顶层模块的状态
        
        output    reg    [15:0]    sdram_dq,    //sdram输入/输出端口
        //output    reg    [3:0]    sdram_dqm,    //输入/输出掩码
        output    reg    [11:0]    sdram_addr,    //sdram地址线
        output    reg    [1:0]    sdram_bank,    //sdram的bank地址线
        output    reg    [3:0]    sdram_cmd,    //sdram的命令寄存器
        output    reg        wr_req,        //写请求（不在写状态时向仲裁进行写请求）
        output    reg        flag_wr_end    //写结束标志（有刷新请求来时，向仲裁输出写结束）
        );
        
    parameter    NOP    =    4'b0111,    //NOP命令
            ACT    =    4'b0011,    //ACT命令
            WR    =    4'b0100,    //写命令（需要将A10拉高）
            PRE    =    4'b0010,    //precharge命令
            CMD_END    =    4'd8,
            COL_END    =    9'd508,        //最后四个列地址的第一个地址
            ROW_END    =    12'd4095,    //行地址结束
            AREF    =    6'b10_0000,    //自动刷新状态
            WRITE    =    6'b00_1000;    //状态机的写状态
            
    reg        flag_act;            //需要发送ACT的标志            
    reg    [3:0]    cmd_cnt;            //命令计数器
    reg    [11:0]    row_addr;            //行地址
    reg    [11:0]    row_addr_reg;            //行地址寄存器
    reg    [8:0]    col_addr;            //列地址
    reg        flag_pre;            //在sdram内部为写状态时需要给precharge命令的标志
 
//flag_pre
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            flag_pre    <=    1'b0;
        else if(col_addr == 9'd0 && flag_wr_end == 1'b1)
            flag_pre    <=    1'b1;
        else if(flag_wr_end == 1'b1)
            flag_pre    <=    1'b0;
    
//flag_act
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            flag_act    <=    1'b0;
        else if(flag_wr_end)
            flag_act    <=    1'b0;
        else if(ref_req == 1'b1 && state == AREF)
            flag_act    <=    1'b1;
//wr_req
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            wr_req    <=    1'b0;
        else if(wr_en == 1'b1)
            wr_req    <=    1'b0;
        else if(state != WRITE && key_wr == 1'b1)
            wr_req    <=    1'b1;
//flag_wr_end
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            flag_wr_end    <=    1'b0;
        else if(cmd_cnt == CMD_END)
            flag_wr_end    <=    1'b1;
        else
            flag_wr_end    <=    1'b0;
//cmd_cnt
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            cmd_cnt    <=    4'd0;
        else if(state == WRITE)
            cmd_cnt    <=    cmd_cnt + 1'b1;
        else 
            cmd_cnt    <=    4'd0;
        
//sdram_cmd
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            sdram_cmd    <=    4'd0;
        else case(cmd_cnt)
            3'd1:
                if(flag_pre == 1'b1)
                    sdram_cmd    <=    PRE;
                else
                    sdram_cmd    <=    NOP;
            3'd2:
                if(flag_act == 1'b1 || col_addr == 9'd0)
                    sdram_cmd    <=    ACT;
                else
                    sdram_cmd    <=    NOP;
            3'd3:         
                sdram_cmd    <=    WR;
 
            default:
                sdram_cmd    <=    NOP;        
        endcase
//sdram_dq
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            sdram_dq    <=    16'd0;
        else case(cmd_cnt)
            3'd3:
                sdram_dq    <=    16'h0012;
            3'd4:
                sdram_dq    <=    16'h1203;
            3'd5:
                sdram_dq    <=    16'h562f;
            3'd6:
                sdram_dq    <=    16'hfe12;
            default:
                sdram_dq    <=    16'd0;
        endcase
/* //sdram_dq_m
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            sdram_dqm    <=    4'd0; */
//row_addr_reg
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            row_addr_reg    <=    12'd0;
        else if(row_addr_reg == ROW_END && col_addr == COL_END && cmd_cnt == CMD_END)
            row_addr_reg    <=    12'd0;
        else if(col_addr == COL_END && flag_wr_end == 1'b1)
            row_addr_reg    <=    row_addr_reg + 1'b1;
        
//row_addr
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            row_addr    <=    12'd0;
        else case(cmd_cnt)
        //因为下边的命令是通过行、列地址分开再给addr赋值，所以需要提前一个周期赋值，以保证在命令到来时能读到正确的地址
            3'd2:
                row_addr    <=    12'b0000_0000_0000;    //在写命令时，不允许auto-precharge    
            default:
                row_addr    <=    row_addr_reg;
        endcase
//col_addr
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            col_addr    <=    9'd0;
        else if(col_addr == COL_END && cmd_cnt == CMD_END)
            col_addr    <=    9'd0;
        else if(cmd_cnt == CMD_END)
            col_addr    <=    col_addr + 3'd4;
//sdram_addr
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            sdram_addr    <=    12'd0;
        else case(cmd_cnt)
            3'd2:
                sdram_addr    <=    row_addr;
            3'd3:
                sdram_addr    <=    col_addr;
            
            default:
                sdram_addr    <=    row_addr;
        endcase
//sdram_bank
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            sdram_bank    <=    2'b00;
endmodule
```

### 读操作模块代码

```verilog
module    sdram_read(
        input    wire        sclk,
        input    wire        s_rst_n,
        input    wire        rd_en,
        input    wire    [5:0]    state,
        input    wire        ref_req,        //自动刷新请求
        input    wire        key_rd,            //来自外部的读请求信号
        input    wire    [15:0]    rd_dq,        //sdram的数据端口
        
        output    reg    [3:0]    sdram_cmd,
        output    reg    [11:0]    sdram_addr,
        output    reg    [1:0]    sdram_bank,
        output    reg        rd_req,            //读请求
        output    reg        flag_rd_end        //突发读结束标志
        );
 
    parameter    NOP    =    4'b0111,
            PRE    =    4'b0010,
            ACT    =    4'b0011,
            RD    =    4'b0101,        //SDRAM的读命令（给读命令时需要给A10拉低）
            CMD_END    =    4'd12,            //
            COL_END    =    9'd508,            //最后四个列地址的第一个地址
            ROW_END    =    12'd4095,        //行地址结束
            AREF    =    6'b10_0000,        //自动刷新状态
            READ    =    6'b01_0000;        //状态机的读状态
        
    reg    [11:0]    row_addr;
    reg    [8:0]    col_addr;
    reg    [3:0]    cmd_cnt;
    reg        flag_act;                //发送ACT命令标志（单独设立标志，便于跑高速）
 
//flag_act
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            flag_act    <=    1'b0;
        else if(flag_rd_end == 1'b1 && ref_req == 1'b1)
            flag_act    <=    1'b1;
        else if(flag_rd_end == 1'b1)
            flag_act    <=    1'b0;
//rd_req
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            rd_req    <=    1'b0;
        else if(rd_en == 1'b1)
            rd_req    <=    1'b0;
        else if(key_rd == 1'b1 && state != READ)
            rd_req    <=    1'b1;
//cmd_cnt
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            cmd_cnt    <=    4'd0;
        else if(state == READ)
            cmd_cnt    <=    cmd_cnt + 1'b1;
        else
            cmd_cnt    <=    4'd0;
//flag_rd_end
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            flag_rd_end    <=    1'b0;
        else if(cmd_cnt == CMD_END)
            flag_rd_end    <=    1'b1;
        else
            flag_rd_end    <=    1'b0;
//row_addr
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            row_addr    <=    12'd0;
        else if(row_addr == ROW_END && col_addr == COL_END && flag_rd_end == 1'b1)
            row_addr    <=    12'd0;
        else if(col_addr == COL_END && flag_rd_end == 1'b1)
            row_addr    <=    row_addr + 1'b1;
//col_addr
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            col_addr    <=    9'd0;
        else if(col_addr == COL_END && flag_rd_end == 1'b1)
            col_addr    <=    9'd0;
        else if(flag_rd_end == 1'b1)
            col_addr    <=    col_addr + 3'd4;
//cmd_cnt
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            cmd_cnt    <=    4'd0;
        else if(state == READ)
            cmd_cnt    <=    cmd_cnt + 1'b1;
        else
            cmd_cnt    <=    4'd0;
//sdram_cmd
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            sdram_cmd    <=    NOP;
        else case(cmd_cnt)
            4'd2:
                if(col_addr == 9'd0)
                    sdram_cmd    <=    PRE;
                else
                    sdram_cmd    <=    NOP;
            4'd3:    
                if(flag_act == 1'b1 || col_addr == 9'd0)
                    sdram_cmd    <=    ACT;
                else
                    sdram_cmd    <=    NOP;
            4'd4:
                sdram_cmd    <=    RD;
            default:
                sdram_cmd    <=    NOP;
        endcase
//sdram_addr
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            sdram_addr    <=    12'd0;
        else case(cmd_cnt)
            4'd4:
                sdram_addr    <=    {3'd0, col_addr};
            default:
                sdram_addr    <=    row_addr;
        endcase
//sdram_bank
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            sdram_bank    <=    2'd0;
endmodule
```

### 自动刷新模块代码

```verilog
module    auto_refresh(
        input    wire        sclk,
        input    wire        s_rst_n,
        input    wire        ref_en,
        input    wire        flag_init_end,    //初始化结束标志（初始化结束后，启动自刷新标志）
        
        output    reg    [11:0]    sdram_addr,
        output    reg    [1:0]    sdram_bank,
        output    reg        ref_req,
        output    reg    [3:0]    cmd_reg,
        output    reg        flag_ref_end
        );
 
    parameter    BANK    =    12'd0100_0000_0000,    //自动刷新是对所有bank刷新
            CMD_END    =    4'd10,
            CNT_END    =    10'd749,    //15us计时结束
            NOP    =    4'b0111,    //
            PRE    =    4'b0010,    //precharge命令
            AREF    =    4'b0001;    //auto-refresh命令
            
            
    reg    [9:0]    cnt_15ms;    //15ms计数器
    reg        flag_ref;    //处于自刷新阶段标志
    reg        flag_start;    //自动刷新启动标志
    reg    [3:0]    cnt_cmd;    //指令计数器
//flag_start
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            flag_start    <=    1'b0;
        else if(flag_init_end == 1'b1)
            flag_start    <=    1'b1;
//cnt_15ms
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            cnt_15ms    <=    10'd0;
        else if(cnt_15ms == CNT_END)
            cnt_15ms    <=    10'd0;
        else if(flag_start == 1'b1)
            cnt_15ms    <=    cnt_15ms + 1'b1;
//flag_ref
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            flag_ref    <=    1'b0;
        else if(cnt_cmd == CMD_END)
            flag_ref    <=    1'b0;
        else if(ref_en == 1'b1)
            flag_ref    <=    1'b1;
//cnt_cmd
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            cnt_cmd    <=    4'd0;
        else if(flag_ref == 1'b1)
            cnt_cmd    <=    cnt_cmd + 1'b1;
        else
            cnt_cmd    <=    4'd0;
//flag_ref_end
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            flag_ref_end    <=    1'b0;
        else if(cnt_cmd == CMD_END)
            flag_ref_end    <=    1'b1;
        else
            flag_ref_end    <=    1'b0;
//cmd_reg
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            cmd_reg    <=    NOP;
        else case(cnt_cmd)
            3'd0:
                if(flag_ref == 1'b1)
                    cmd_reg    <=    PRE;
                else
                    cmd_reg    <=    NOP;
            3'd1:
                cmd_reg    <=    AREF;
            3'd5:
                cmd_reg    <=    AREF;
            default:
                cmd_reg    <=    NOP;
        endcase
//sdram_addr
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            sdram_addr    <=    12'd0;
        else case(cnt_cmd)
            4'd0:
                sdram_addr    <=    BANK;    //bank进行刷新时指定allbank or signle bank
            default:
                sdram_addr    <=    12'd0;
        endcase
//sdram_bank
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            sdram_bank    <=    2'd0;        //刷新指定的bank
//ref_req
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            ref_req    <=    1'b0;
        else if(ref_en == 1'b1)
            ref_req    <=    1'b0;
        else if(cnt_15ms == CNT_END)
            ref_req    <=    1'b1;
//flag_ref_end
    always    @(posedge sclk or negedge s_rst_n)
        if(s_rst_n == 1'b0)
            flag_ref_end    <=    1'b0;
        else if(cnt_cmd == CMD_END)
            flag_ref_end    <=    1'b1;
        else
            flag_ref_end    <=    1'b0;
 
endmodule
```

​       

​        

[  完  ]