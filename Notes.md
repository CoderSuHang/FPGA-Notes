# 20230914_FPGA学习笔记

## 1 环境搭建

​	略。

## 2 Verilog学习

​	在笔记本子上。

## 3 实验笔记

### 3.1 点亮LED灯

#### 3.1.1 设计流程

​	设计规划-->波形绘制-->代码编写-->代码编译-->逻辑仿真-->波形对比-->绑定管脚-->分析综合布局布线-->上板验证

#### 3.1.2 文件管理

* doc				//文档资料，包括：波形图、项目日志等
* quartus_prj     //主要放置工程文件
* rtl                 //可综合的代码，放置最后可生成硬件电路的代码
* sim               //主要放置对可综合代码的仿真文件

#### 3.1.3 设计规划

* (1)KEY-->LED：用按键点亮LED灯，简单不需要模块划分。
* (2)确定原理图：KEY1按下让D6亮起
  * ![](https://pic.imgdb.cn/item/64db7de8661c6c8e5491b43e.jpg)
  * ![](https://pic.imgdb.cn/item/64db7e1d661c6c8e549241cc.jpg)
  * KEY1引脚为：CLK2_M2
  * ![](https://pic.imgdb.cn/item/64db7e92661c6c8e54936bc0.jpg)
  * D6引脚为：IO_B3_L7
  * ![](https://pic.imgdb.cn/item/64db7edd661c6c8e549439cd.jpg)

#### 3.1.4 波形绘制

* (1)打开文件夹“doc”，新建“visio”绘图：
* (2)绘制波形图：
  * ![](https://pic.imgdb.cn/item/64db7fb6661c6c8e549665bd.jpg)

#### 3.1.5 代码编写

* (1)打开文件夹“rtl”，新建“.v”文件：

  * 这里需要新建文本文档“.txt”，再修改后缀成为“.v”文件。

* (2)双击打开文件，开始编写代码：

  * ``` verilog
    module	led
        (
            input	wire		key_in,		//输入信号是key_in
            output	wire		led_out		//输出信号是led_out
        );
        assign	led_out = key_in;			//赋值语句，把key_in的输入信号赋给led_out输出信号
    endmodule
    ```

#### 3.1.6 代码编译

* （1）打开开发软件“Quartus II”，新建工程：
  * 左上角“File”选项，选择“New Project Wizard”
    * ![](https://pic.imgdb.cn/item/64db840f661c6c8e54a1d31e.jpg)
  * 进入向导界面，点击next：
    * ![](https://pic.imgdb.cn/item/64db84b8661c6c8e54a397a4.jpg)
  * 选择工程文件保存路径，第三个选项保持默认：
    * ![](https://pic.imgdb.cn/item/64db8557661c6c8e54a58d94.jpg)
  * 跳过：
    * ![](https://pic.imgdb.cn/item/64db86a2661c6c8e54a9074a.jpg)
  * 这里需要找到项目芯片使用型号：
    * 1.器件选择：Cyclone IV E
    * 2.封装方式：FBGA
    * 3.引脚数量：256
    * 4.速度等级：8
    * 5.芯片型号：EP4CE10F17C8
    * ![](https://pic.imgdb.cn/item/64db8719661c6c8e54aa4fae.jpg)
  * EDA工具设置直接跳过：
    * ![](https://pic.imgdb.cn/item/64db890e661c6c8e54affc62.jpg)
  * Finish
    * ![image-20230815221829231](C:\Users\10482\AppData\Roaming\Typora\typora-user-images\image-20230815221829231.png)
* (2)将写好的“.v”文件加载到工程当中：
  * ![](https://pic.imgdb.cn/item/64db89c4661c6c8e54b1f8c5.jpg)
  * ![image-20230815222351808](C:\Users\10482\AppData\Roaming\Typora\typora-user-images\image-20230815222351808.png)
* (3)编译代码：
  * ![image-20230815222538895](C:\Users\10482\AppData\Roaming\Typora\typora-user-images\image-20230815222538895.png)
  * ![image-20230815222748338](C:\Users\10482\AppData\Roaming\Typora\typora-user-images\image-20230815222748338.png)
  * 编译完成

#### 3.1.7 逻辑仿真

* (1)使用仿真文件产生模拟的激励信号输入到被测试模块当中，观察输出响应是否达到要求：

  * ![](https://pic.imgdb.cn/item/64db8bcc661c6c8e54b74a40.jpg)

* (2)仿真需要编写仿真文件，使用野火编写好的仿真程序即可,测试文件需要放在“sim”文件夹中：

  * ``` verilog
    `timescale  1ns/1ns
    ///////////////////////////////////////////////////////////////////////////
    // Author        : EmbedFire
    // Create Date   : 2019/03/12
    // Module Name   : tb_led
    // Project Name  : led
    // Target Devices: Altera EP4CE10F17C8N
    // Tool Versions : Quartus 13.0
    // Description   : 按键控制LED灯仿真文件
    //
    // Revision      : V1.0
    // Additional Comments:
    // 
    // 实验平台: 野火_征途Pro_FPGA开发板
    // 公司    : http://www.embedfire.com
    // 论坛    : http://www.firebbs.cn
    // 淘宝    : https://fire-stm32.taobao.com
    //////////////////////////////////////////////////////////////////////////
    
    module  tb_led();
    
    //********************************************************************//
    //****************** Parameter and Internal Signal *******************//
    //********************************************************************//
    //wire  define
    wire    led_out ;
    
    //reg   define
    reg     key_in  ;
    
    //********************************************************************//
    //***************************** Main Code ****************************//
    //********************************************************************//
    //初始化输入信号
    initial key_in <= 1'b0;
    
    //key_in:产生输入随机数，模拟按键的输入情况
    always #10 key_in <= {$random} % 2; /*取模求余数，产生非负随机数0、1
                                          每隔10ns产生一次随机数*/
    
    //********************************************************************//
    //**************************** Instantiate ***************************//
    //********************************************************************//
    //------------- led_inst -------------
    led led_inst
    (
        .key_in (key_in ),  //input     key_in
    
        .led_out(led_out)   //output    led_out
    );
    
    endmodule
    
    ```

* (3)添加仿真测试文件：

  * ![](https://pic.imgdb.cn/item/64db89c4661c6c8e54b1f8c5.jpg)
  * ![](https://pic.imgdb.cn/item/64db8daf661c6c8e54bc538b.jpg)

* (4)仿真设置：

  * ![](https://pic.imgdb.cn/item/64db8e21661c6c8e54bd8e0e.jpg)
  * 在“simulation”选项中进行设置：
  * ![](https://pic.imgdb.cn/item/64db8f67661c6c8e54c0fe77.jpg)
  * ![](https://pic.imgdb.cn/item/64db8f45661c6c8e54c09be3.jpg)
  * ![](https://pic.imgdb.cn/item/64db900e661c6c8e54c2ce27.jpg)
  * OK-->Apply下去即可

#### 3.1.8 波形对比

* 运行仿真,这里介绍了ModelSim仿真软件使用方法，详情可看视频25:55的内容：
  * ![](https://pic.imgdb.cn/item/64db90fd661c6c8e54c53a13.jpg)
  * ![](https://pic.imgdb.cn/item/64db934d661c6c8e54cb7276.jpg)

#### 3.1.9 绑定管脚

* ![](https://pic.imgdb.cn/item/64db9396661c6c8e54cc30b3.jpg)
* ![](https://pic.imgdb.cn/item/64db93f8661c6c8e54cd3ba4.jpg)

* 返回工程进行重新编译；

#### 3.1.10 上板验证

* 点击“programmer”，选择添加文件：
  * ![](https://pic.imgdb.cn/item/64db9521661c6c8e54d1163f.jpg)
* ![](https://pic.imgdb.cn/item/64db96b5661c6c8e54d761e5.jpg)
* 如果是第一次下载程序，会出现电脑无法识别JTAG端口问题，在开发板使用手册“第4章Quartus软件和USB-Blaster驱动安装”有解决办法介绍

#### 3.1.11 程序固化

* （1）生成“.jic”文件
  * ![](https://pic.imgdb.cn/item/64db9721661c6c8e54d9242b.jpg)
  * ![](https://pic.imgdb.cn/item/64db97b8661c6c8e54db895b.jpg)
  * ![](https://pic.imgdb.cn/item/64db980f661c6c8e54dcda36.jpg)
  * ![](https://pic.imgdb.cn/item/64db985d661c6c8e54de00f9.jpg)
  * ![](https://pic.imgdb.cn/item/64db9873661c6c8e54de5703.jpg)
* (2)删除原来的“.sof”文件：
  * ![](https://pic.imgdb.cn/item/64db98a9661c6c8e54df2235.jpg)
* (3)添加生成后的“.jic”文件：
  * ![image-20230815232620937](C:\Users\10482\AppData\Roaming\Typora\typora-user-images\image-20230815232620937.png)
* (4)固化程序：
  * ![image-20230815232705590](C:\Users\10482\AppData\Roaming\Typora\typora-user-images\image-20230815232705590.png)
* (5)重新上电即可验证固化成功。

## 4 简单组合逻辑

### 4.1 多路选择器

#### 4.1.1 理论学习

* (1)数字电路根据电路的不同特点可以分为：`组合逻辑`和`时序逻辑`。
* (2)组合逻辑电路的特点：输出信号只是当前时刻输入信号的函数，与其他时刻的输入状态无关，无储存电路，也没有反馈电路。
* (3)多路选择器(数据选择器)：在多路数据传送过程中，能够根据需要将其中任意一路选出来的电路，叫做数据选择器，也称多路选择器或多路开关。
  * ![](https://pic.imgdb.cn/item/64dc9134661c6c8e54103e76.jpg)

#### 4.1.2 实战演练

* (1)实战项目：

  * 选择信号sel为高电平时，输入信号in_1的输出；
    选择信号sel为低电平时，输入信号in_2的输出。

* (2)绘制整体波形图：

  * 在“doc”文件夹中新建“visio”文件绘制波形图；
  * ![](https://pic.imgdb.cn/item/64dc9916661c6c8e542b031d.jpg)

* (3)编写代码

  * a.在“rtl”文件夹新建“.v”文件；

    * ``` verilog
      /****************模块开始****************/
      module  mux2_1      //module后面跟着模块名，并且模块名要与文件名尽量保持一致
      
      /****************定义变量****************/
      (
          input   wire    [0:0]   in_1,//wire后面是信号位宽，如果是1位款可以不用编写，或者用[0:0]表示
          input   wire            in_2,
          input   wire            sel ,
      
          output  reg             out  //输出信号将用于always语句中，变量要求必须为reg型变量
      );
      
      /******用组合逻辑对输出信号进行赋值******/
      always@(*)//*号相当于任何信号变化都会执行always语句，此时的*相当于in_1、in_2、out
      //always语句使用方法在视频03:00中
          if(sel == 1'b1)              //当sel为高电平时
              out = in_1;              //如果只有一条语句，begin和end可以不写
      
          else
              out = in_2;
      
      endmodule                        //每个模块只有一组module-endmodule
      ```

  * b.打开Quarts II进行代码编译

  * c.在“sim”文件夹新建“.v”测试代码文件：

    * ``` verilog
      `timescale 1ns/1ns//仿真文件必不可少：时间尺度预编译指令 时间单位/时间精度
      
      module  tb_mux2_1();//仿真测试这里的括号不用添加输入输出信号
      //由于仿真测试成语用于项目的always语句中，所以在这里的变量全部是reg型
      reg     in_1;
      reg     in_2;
      reg     sel ;
      
      wire    out ;//输出信号可以用wire方便观察
      
      initial//该语句上电只执行一次
          begin
              in_1    <=  1'b0;
              in_2    <=  1'b0;
              sel     <=  1'b0;
          end
          
      always #10 in_1 <=  {$random} % 2;//{$random} % 2随机数除2取余
                                        //要么是1要么是0，这样可以模拟高低电平，
                                        //#10表示每隔10个时间单位执行一次
      always #10 in_2 <=  {$random} % 2;
      always #10 sel  <=  {$random} % 2;
      
      initial
          begin
              $timeformat(-9,0,"ns",6);//系统函数设置显示的时间格式
                                       //此处表示的是(打印时间单位为纳秒,
                                       //小数点后打印的小数位为 0 位,
                                       //时间值后打印的字符串为“ns”,
                                       //打印的最小数量字符为 6 个)
                                       
              //只要监测的变量（时间、in1, in2, sel, out）发生变化，就会打印出相应的信息
              $monitor("@time %t:in_1=%b in_2=%b sel=%b out=%b",$time,in_1,in_2,sel,out);
          end
      /*实例化，在仿真文件中调用被仿真文件*/
      mux2_1  mux2_1_inst      //实例化1次
      
      /****************定义变量****************/
      (
          .in_1(in_1),//.表示连接,.b(a):将（被仿真变量a）与仿真变量b连接
          .in_2(in_2),
          .sel (sel),
      
          .out (out) //输出信号将用于always语句中，变量要求必须为reg型变量
      );
      endmodule
      ```

  * d.在Quartus II软件中加入仿真测试文件，并设置相关仿真参数

  * c.点击Modelsim联仿按钮进行联仿（具体操作见3.1.7）；

  * d.查看波形图进行验证

  * e.管脚绑定

    * in_1-->key1-->M2
    * in_2-->key2-->M1
    * sel  -->key3-->E15
    * out  -->led0-->L7

  * f.回到实验工程进行全编译

  * g.点击program按钮导入“.sof”文件；

  * h.连接板卡，点击“start”按钮下载程序；

  * I.上板验证；

  * g.程序固化。

#### 4.1.3 其他方法

* (1)always 中 case 实现方法

  * ``` verilog
    endmodule
    
    (
    input wire in1, //输入端 1
    input wire in2, //输入端 2
    input wire sel, //选择端
    
    output reg out //结果输出
    );
    
    //out:组合逻辑输出选择结果
    always@(*)
    case(sel)
    1'b1 : out = in1;
    1'b0 : out = in2;
    //如果 sel 不能列举出所有的情况一定要加 default
    //此处 sel 只有两种情况，并且完全列举了，所以 default 可以省略
    default : out = in1;
    endcase
    
    endmodule
    ```

  * ![](https://pic.imgdb.cn/item/64dde2c6661c6c8e54980fee.jpg)

* (2)assign中条件运算符（三元运算符）实现方法

  * ``` verilog
    module mux2_1
    (
    input wire in1, //输入端 1
    input wire in2, //输入端 2
    input wire sel, //选择端
    
    output wire out //结果输出
    );
    
    //out:组合逻辑输出选择结果
    //此处使用的是条件运算符（三元运算符），当括号里面的条件成立时
    //执行"?”后面的结果；如果括号里面的条件不成立时，执行“：”后面的结果
    assign out = (sel == 1'b1) ? in1 : in2;
    
    endmodule
    ```

  * ![image-20230817170622565](C:\Users\10482\AppData\Roaming\Typora\typora-user-images\image-20230817170622565.png)

### 4.2 译码器

#### 4.2.1 理论学习

* 译码器原理：译码是编码的逆过程，在编码时，每一种二进制代码，都赋予了特定的含义，即都表示了一个确定的信号或者对象。把代码状态的特定含义翻译出来的过程叫做译码，实现译码操作的电称为译码器。或者说，译码器是可以将输入二进制代码的状态翻译成输出信号，以表示其原来含义的电路。
* 译码器（decoder）是一类多输入多输出组合逻辑电路器件，其可以分为：
  * 变量译码和显示译码两类。

#### 4.2.2 实战演练

* (1)实验目标

  * 设计并仿真验证 3-8 译码器。

* (2)绘制模块框图

  * <img src="https://pic.imgdb.cn/item/64df351a661c6c8e547709b5.jpg" style="zoom: 50%;" />

* (3)绘制真值表

  * <img src="https://pic.imgdb.cn/item/64df3560661c6c8e54782111.jpg" style="zoom:50%;" />

* (4)绘制波形图

  * ![](https://pic.imgdb.cn/item/64df3605661c6c8e547a8fd4.jpg)

* (5)代码编写

  * 打开“rtl”文件夹新建“.v”文件；

    * if-else语句写法(存在优先级顺序执行)：

    * ``` verilog
      module  decoder
      (
          input   wire            in_1,
          input   wire            in_2,
          input   wire            in_3,
          
          output  reg     [7:0]   out//这里用reg型
                                     //因为后面需要用到always语句
                                     //设置位宽为8bit
      );
      
      always@(*)
          if({in_1,in_2,in_3} == 3'b000)//这里用{}来拼接符号
              out = 8'b0000_0001;
          else if({in_1,in_2,in_3} == 3'b001)
              out = 8'b0000_0010;
          else if({in_1,in_2,in_3} == 3'b010)
              out = 8'b0000_0100;
          else if({in_1,in_2,in_3} == 3'b011)
              out = 8'b0000_1000;
          else if({in_1,in_2,in_3} == 3'b100)
              out = 8'b0001_0000;
          else if({in_1,in_2,in_3} == 3'b101)
              out = 8'b0010_0000;
          else if({in_1,in_2,in_3} == 3'b110)
              out = 8'b0100_0000;
          else if({in_1,in_2,in_3} == 3'b111)
              out = 8'b1000_0000;
          else
              out = 8'b0000_0001;
      endmodule
      ```

    * case语句写法(不存在优先级顺序执行方式，通过判断变量执行对应指令)：

    * ``` verilog
      module  decoder
      (
          input   wire            in_1,
          input   wire            in_2,
          input   wire            in_3,
          
          output  reg     [7:0]   out//这里用reg型
                                     //因为后面需要用到always语句
                                     //设置位宽为8bit
      );
      
      always@()
          case({in_1,in_2,in_3})
              3'b000:out = 8'b0000_0001;
              3'b001:out = 8'b0000_0010;
              3'b010:out = 8'b0000_0100;
              3'b011:out = 8'b0000_1000;
              3'b100:out = 8'b0001_0000;
              3'b101:out = 8'b0010_0000;
              3'b110:out = 8'b0100_0000;
              3'b111:out = 8'b1000_0000;
              default:out = 8'b0000_0001;
          endcase
      endmodule
      ```

    * 

  * 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

  * 导入“rtl”文件中编写完成的“.v”文件，进行编译。

    * 这里出现的BUG：
      * a.“top_level”文件名前缀不能有数字；
        * ![image-20230818174622016](C:\Users\10482\AppData\Roaming\Typora\typora-user-images\image-20230818174622016.png)
      * b.“.v”文件前缀不能有数字；
      * c.“module”指令后面文件名前缀不能有数字。
    * 可以使用“RTL Viewer”查看代码综合出的视图。
      * ![](https://pic.imgdb.cn/item/64e2ca88661c6c8e549224c2.jpg)

* (6)测试代码编写

  * 打开“sim”文件夹新建“.v”文件；

  * ``` verilog
    `timescale 1ns/1ns
    
    module  tb_decoder();
    
    reg     in_1;
    reg     in_2;
    reg     in_3;
    
    wire    [7:0]   out;
    
    initial
        begin
            in_1 <= 1'b0;
            in_2 <= 1'b0;
            in_3 <= 1'b0;
        end
    
    always #10  in_1 <= {$random} % 2;
    always #10  in_2 <= {$random} % 2;
    always #10  in_3 <= {$random} % 2;
    
    initial
        begin
            $timeformat(-9,0,"ns",6);
            $monitor("@time %t:in_1=%b,in_2=%b,in_3=%b,out=%b",$time,in_1,in_2,in_3,out);
        end
    
    decoder decoder_inst//实例化
    (
        .in_1(in_1),
        .in_2(in_2),
        .in_3(in_3),
    
        .out (out)
    );
    
    endmodule
    ```

    * “decoder_decoder_inst”例化示意图:
      * ![](https://pic.imgdb.cn/item/64df629f661c6c8e54fdf0d9.jpg)

  * 导入测试文件，进行Modelsim联仿，查看波形图和打印信息：

    * ![](https://pic.imgdb.cn/item/64df679b661c6c8e540d5ef9.jpg)
    * ![](https://pic.imgdb.cn/item/64df67bf661c6c8e540ded85.jpg)

  * 与真值表对比即可。

### 4.3 半加器

#### 4.3.1 理论学习

* 主要用于两个数或者多个数的加和， 加法器又分为半加器（half adder）和全加器（full adder）。
  * 半加器：指对两个输入数据位相加，输出一个结果位和进位，没有进位输入的加法器电路。是实现两个一位二进制数的加法运算电路。
  * 全加器：而全加器是在半加器的基础上的升级版，除了加数和被加数加和外还要加上上一级传进来的进位信号。

#### 4.3.2 实战演练

* (1)实验目标：

  * 设计并实现一个半加器，使用开发板上的按键 KEY1、KEY2 作为被加数输入，选择开发板上的 LED 灯 D6 表示相加和的输出，LED 灯 D7 表示进位输出。

* (2)绘制模块框图

  * <img src="https://pic.imgdb.cn/item/64e5f2d1661c6c8e5429b45f.jpg" style="zoom: 50%;" />

* (3)绘制真值表

  * <img src="https://pic.imgdb.cn/item/64e5f2f6661c6c8e5429c7a6.jpg" style="zoom:50%;" />

* (4)绘制波形图

  * <img src="https://pic.imgdb.cn/item/64e5f315661c6c8e5429d061.jpg" style="zoom:50%;" />

* (5)代码编写

  * 打开“rtl”文件夹新建“.v”文件；

  * ``` verilog
    module  half_adder
    (
        input   wire        in_1,
        input   wire        in_2,
        
        output  wire        sum,
        output  wire        count
    );
    
    assign  {count,sum} = in_1 + in_2;  //相加之和的高位赋值给count
                                        //相加之和的低位赋值给sum
    
    endmodule
    ```

  * 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

  * 导入“rtl”文件中编写完成的“.v”文件，进行编译.

    * 可以使用“RTL Viewer”查看代码综合出的视图。
      * ![](https://pic.imgdb.cn/item/64e5f8fb661c6c8e542bf72e.jpg)

  * (6)测试代码编写

    * 打开“sim”文件夹新建“.v”文件；

    * ``` verilog
      `timescale  1ns/1ns
      module  tb_half_adder();
      
      reg     in_1;
      reg     in_2;
      
      wire    sum;
      wire    count;
      
      initial
          begin
              in_1 <= 1'b0;
              in_2 <= 1'b0;
          end
          
      always #10  in_1 <= {$random} % 2;
      always #10  in_2 <= {$random} % 2;
      
      initial
          begin
              $timeformat(-9,0,"ns",6);
              $monitor("@time %t:in_1=%b,in_2=%b,sum=%b,count=%b",$time,in_1,in_2,sum,count);
          end
          
      half_adder half_adder_inst
      (
          .in_1(in_1),
          .in_2(in_2),
      
          .sum(sum),
          .count(count)
      );
      
      endmodule
      ```

    * 导入测试文件，进行Modelsim仿真设置；

    * 开始联仿，查看波形图和打印信息（这节课讲述了Wire窗口的新设置操作）：

      * ![](https://pic.imgdb.cn/item/64e5fa36661c6c8e542c3f9e.jpg)
      * ![](https://pic.imgdb.cn/item/64e5fa5c661c6c8e542c469d.jpg)

    * 与真值表对比即可。

  * (7)管脚绑定

    * in_1-->key1-->M2
    * in_2-->key2-->M1
    * sum-->led1-->M6
    * count -->led0-->L7

  * (8)上板子验证

    * 点击program按钮导入“.sof”文件；
    * 连接板卡，点击“start”按钮下载程序；
    * 上板验证；

  * (9)程序固化。

## 5 层次化设计

### 5.1 理论学习

#### 5.1.1 设计方法

* 数字电路中根据模块层次不同有两种基本的结构设计方法：`自底向上（Bottom-Up）`和`和自顶向下（Top-Down）`的设计方法。
  * （1）自底向上（Bottom-Up）的设计是一种传统的设计方法，对设计进行逐次划分的过程是从存在的基本单元出发的，设计树最末枝上的单元要么是已经构造出的单元，要么是其他项目开发好的单元或者是可外购得到的单元。在自底向上建模方法中，我们首先对现有的功能块进行分析，然后使用这些模块来搭建规模大一些的功能块，如此继续直至顶层模块。
    * ![](https://pic.imgdb.cn/item/64e70ae6661c6c8e54a412b4.jpg)
  * （2）自上而下（Top-Down）的设计是从系统级开始，把系统分为基本单元，然后再把每个单元划分为下 一层次的基本单元，一直这样做下去，直到直接可以用EDA 元件库中的原件来实现为止。 在自顶向下设计方法中，我们首先定义顶层功能块，进而分析需要哪些构成顶层模块的必 要子模块；然后进一步对各个子模块进行分解，直到到达无法进一步分解的底层功能块。
    * ![](https://pic.imgdb.cn/item/64e70e21661c6c8e54a6c9fc.jpg)

### 5.2 实战演练

#### 5.2.1 实验目标

* 使用上一章节实现的半加器，结合层次化设计思想，设计并实现一个全加器。

#### 5.2.2 程序设计

* （1）模块框图绘制：

  * <img src="https://pic.imgdb.cn/item/64e717b4661c6c8e54aa84f2.jpg" style="zoom: 33%;" />

* （2）层次化设计：

  * ![](https://pic.imgdb.cn/item/64e7180a661c6c8e54aa9853.jpg)

* （3）绘制真值表：

  * <img src="https://pic.imgdb.cn/item/64e719cf661c6c8e54ab1cab.jpg" style="zoom: 80%;" />

* （4）绘制波形图

  * ![](https://pic.imgdb.cn/item/64e71c16661c6c8e54ac129b.jpg)

* （5）代码编写

  * 打开“rtl”文件夹新建“.v”文件；

  * ``` verilog
    module  full_adder
    (
        input   wire    in_1,
        input   wire    in_2,
        input   wire    cin ,
    
        output  wire     sum,
        output  wire    count
    );
    
    wire    h0_sum;
    wire    h0_count;
    wire    h1_count;
    
    half_adder half_adder_inst0           //实例化
    (
        .in_1(in_1),
        .in_2(in_2),
    
        .sum(h0_sum),
        .count(h0_count)
    );
    
    half_adder half_adder_inst1           //实例化
    (
        .in_1(h0_sum),
        .in_2(cin),
    
        .sum(sum),
        .count(h1_count)
    );
    
    assign  count = (h0_count | h1_count);//两个进位进行或运算
    
    endmodule
    ```

  * 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

  * 导入“rtl”文件中编写完成的“.v”文件，进行编译.

    * 可以使用“RTL Viewer”查看代码综合出的视图。
      * ![](https://pic.imgdb.cn/item/64e7217a661c6c8e54ae322e.jpg)

#### 5.2.3 仿真验证

* (1)测试代码编写

  * 打开“sim”文件夹新建“.v”文件；

  * ``` verilog
    `timescale  1ns/1ns
    module  tb_full_adder();
    
    reg     in_1;
    reg     in_2;
    reg     cin;
    
    wire    sum;
    wire    count;
    
    initial
        begin
            in_1 <= 1'b0;
            in_2 <= 1'b0;
            cin  <= 1'b0;
        end
    
    always #10  in_1 <= {$random} % 2;
    always #10  in_2 <= {$random} % 2;
    always #10  cin  <= {$random} % 2;
    
    initial
        begin
            $timeformat(-9,0,"ns",6);
            $monitor("@time %t:in_1=%b,in_2=%b,cin=%b,sum=%b,count=%b",$time,in_1,in_2,cin,sum,count);
        end
        
    full_adder full_adder_inst
    (
        .in_1(in_1),
        .in_2(in_2),
        .cin(cin),
    
        .sum(sum),
        .count(count)
    );
    
    endmodule
    ```

  * 导入测试文件，进行Modelsim仿真设置；

  * 开始联仿，查看波形图和打印信息（这节课讲述了Wire窗口的新设置操作）：

    * ![](https://pic.imgdb.cn/item/64e74850661c6c8e54b92bc5.jpg)
    * ![](https://pic.imgdb.cn/item/64e744ea661c6c8e54b8485e.jpg)

  * 与真值表对比即可。


#### 5.2.4 管脚绑定

* in_1-->key1-->M2
* in_2-->key2-->M1
* cin---->key3-->E15
* sum-->led1-->L7
* count -->led0-->M6

#### 5.2.5 上板子验证

* 点击program按钮导入“.sof”文件；
* 连接板卡，点击“start”按钮下载程序；
* 上板验证；

#### 5.2.6 程序固化

* 详情参见3.1.11

## 6 避免Latch的产生（锁存器为例）

### 6.1 Latch简介

* Latch 其实就是锁存器，是一种在异步电路系统中，对输入信号电平敏感的单元，用来存储信息。
* 锁存器在数据未锁存时，输出端的信号随输入信号变化，就像信号通过一个缓冲器，一旦锁存信号有效，则数据被锁存，输入信号不起作用。因此，锁存器也被称为透明锁存器，指的是不锁存时输出对于输入是透明的。

#### 6.1.2 异步电路与同步电路

* 异步电路:异步电路主要是组合逻辑电路，用于产生FIFO或RAM的读写控制信号脉冲，但它同时也用在时序电路中，此时它没有统一的时钟，状态变化的时刻是不稳定的，通常输入信号只在电路处于稳定状态时才发生变化。
* 同步电路:同步电路是由时序电路(寄存器和各种触发器)和组合逻辑电路构成的电路，其所有操作都是在严格的时钟控制下完成的。这些时序电路共享同一个时钟CLK，而所有的状态变化都是在时钟的上升沿(或下降沿)完成的。

#### 6.1.3 Latch的危害

* 对毛刺敏感
* 不能异步复位
* 复杂的静态时序分析
* 占用更多逻辑资源
* 额外的延时

### 6.2 几种产生Latch的情况

#### 6.2.1 组合逻辑中if-else条件分支语句缺少else语句

* 未产生Latch版：

  * ``` verilog
    module  decoder
    (
        input   wire            in_1,
        input   wire            in_2,
        input   wire            in_3,
        
        output  reg     [7:0]   out//这里用reg型
                                   //因为后面需要用到always语句
                                   //设置位宽为8bit
    );
    
    always@(*)
        if({in_1,in_2,in_3} == 3'b000)//这里用{}来拼接符号
            out = 8'b0000_0001;
        else if({in_1,in_2,in_3} == 3'b001)
            out = 8'b0000_0010;
        else if({in_1,in_2,in_3} == 3'b010)
            out = 8'b0000_0100;
        else if({in_1,in_2,in_3} == 3'b011)
            out = 8'b0000_1000;
        else if({in_1,in_2,in_3} == 3'b100)
            out = 8'b0001_0000;
        else if({in_1,in_2,in_3} == 3'b101)
            out = 8'b0010_0000;
        else if({in_1,in_2,in_3} == 3'b110)
            out = 8'b0100_0000;
        else if({in_1,in_2,in_3} == 3'b111)
            out = 8'b1000_0000;
        else
            out = 8'b0000_0001;
    endmodule
    ```

  * ![](https://pic.imgdb.cn/item/64e860ca661c6c8e54f48a82.jpg)

* 产生Latch版：

  * ``` verilog
    module  decoder
    (
        input   wire            in_1,
        input   wire            in_2,
        input   wire            in_3,
        
        output  reg     [7:0]   out//这里用reg型
                                   //因为后面需要用到always语句
                                   //设置位宽为8bit
    );
    
    always@(*)
        if({in_1,in_2,in_3} == 3'b000)//这里用{}来拼接符号
            out = 8'b0000_0001;
        else if({in_1,in_2,in_3} == 3'b001)
            out = 8'b0000_0010;
        else if({in_1,in_2,in_3} == 3'b010)
            out = 8'b0000_0100;
        else if({in_1,in_2,in_3} == 3'b011)
            out = 8'b0000_1000;
        else if({in_1,in_2,in_3} == 3'b100)
            out = 8'b0001_0000;
        else if({in_1,in_2,in_3} == 3'b101)
            out = 8'b0010_0000;
        else if({in_1,in_2,in_3} == 3'b110)
            out = 8'b0100_0000;
        else if({in_1,in_2,in_3} == 3'b111)
            out = 8'b1000_0000;
        // else
            // out = 8'b0000_0001;
    endmodule
    ```

  * ![](https://pic.imgdb.cn/item/64e86189661c6c8e54f4cc03.jpg)

  * ![](https://pic.imgdb.cn/item/64e861af661c6c8e54f4f06c.jpg)

#### 6.2.2 组合逻辑中case条件分支语句条件未完全列举，且缺少default语句

* 未产生Latch版：

  * ``` verilog
    module  decoder
    (
        input   wire            in_1,
        input   wire            in_2,
        input   wire            in_3,
        
        output  reg     [7:0]   out//这里用reg型
                                   //因为后面需要用到always语句
                                   //设置位宽为8bit
    );
    
    always@(*)
        case({in_1,in_2,in_3})
            3'b000:out = 8'b0000_0001;
            3'b001:out = 8'b0000_0010;
            3'b010:out = 8'b0000_0100;
            3'b011:out = 8'b0000_1000;
            3'b100:out = 8'b0001_0000;
            3'b101:out = 8'b0010_0000;
            3'b110:out = 8'b0100_0000;
            3'b111:out = 8'b1000_0000;
            default:out = 8'b0000_0001;
        endcase
    endmodule
    ```

  * ![](https://pic.imgdb.cn/item/64e8623b661c6c8e54f58e82.jpg)

* 产生Latch版：

  * ``` verilog
    module  decoder
    (
        input   wire            in_1,
        input   wire            in_2,
        input   wire            in_3,
        
        output  reg     [7:0]   out//这里用reg型
                                   //因为后面需要用到always语句
                                   //设置位宽为8bit
    );
    
    always@(*)
        case({in_1,in_2,in_3})
            3'b000:out = 8'b0000_0001;
            3'b001:out = 8'b0000_0010;
            3'b010:out = 8'b0000_0100;
            3'b011:out = 8'b0000_1000;
            3'b100:out = 8'b0001_0000;
            3'b101:out = 8'b0010_0000;
            3'b110:out = 8'b0100_0000;
            //3'b111:out = 8'b1000_0000;
            //default:out = 8'b0000_0001;
        endcase
    endmodule
    ```

  * ![](https://pic.imgdb.cn/item/64e862dd661c6c8e54f5c2e9.jpg)

  * ![](https://pic.imgdb.cn/item/64e862ff661c6c8e54f5cff0.jpg)

#### 6.2.3 组合逻辑中输出变量赋值给自己

* 未产生Latch版：

  * ``` verilog
    module  decoder
    (
        input   wire            in_1,
        input   wire            in_2,
        input   wire            in_3,
        
        output  reg     [7:0]   out//这里用reg型
                                   //因为后面需要用到always语句
                                   //设置位宽为8bit
    );
    
    always@(*)
        case({in_1,in_2,in_3})
            3'b000:out = 8'b0000_0001;
            3'b001:out = 8'b0000_0010;
            3'b010:out = 8'b0000_0100;
            3'b011:out = 8'b0000_1000;
            3'b100:out = 8'b0001_0000;
            3'b101:out = 8'b0010_0000;
            3'b110:out = 8'b0100_0000;
            3'b111:out = 8'b1000_0000;
            default:out = 8'b0000_0001;
        endcase
    endmodule
    ```

  * ![](https://pic.imgdb.cn/item/64e8623b661c6c8e54f58e82.jpg)

* 产生Latch版：

  * case语句：

  * ``` verilog
    module  decoder
    (
        input   wire            in_1,
        input   wire            in_2,
        input   wire            in_3,
        
        output  reg     [7:0]   out//这里用reg型
                                   //因为后面需要用到always语句
                                   //设置位宽为8bit
    );
    
    always@(*)
        case({in_1,in_2,in_3})
            3'b000:out = 8'b0000_0001;
            3'b001:out = 8'b0000_0010;
            3'b010:out = 8'b0000_0100;
            3'b011:out = 8'b0000_1000;
            3'b100:out = 8'b0001_0000;
            3'b101:out = 8'b0010_0000;
            3'b110:out = 8'b0100_0000;
            3'b111:out = out;
            default:out = 8'b0000_0001;
        endcase
    endmodule
    ```

  * ![](https://pic.imgdb.cn/item/64e863ba661c6c8e54f5feb4.jpg)

  * ![](https://pic.imgdb.cn/item/64e86417661c6c8e54f61d36.jpg)

  * if-else语句：

  * ``` verilog
    module  decoder
    (
        input   wire            in_1,
        input   wire            in_2,
        input   wire            in_3,
        
        output  reg     [7:0]   out//这里用reg型
                                   //因为后面需要用到always语句
                                   //设置位宽为8bit
    );
    
    always@(*)
        if({in_1,in_2,in_3} == 3'b000)//这里用{}来拼接符号
            out = 8'b0000_0001;
        else if({in_1,in_2,in_3} == 3'b001)
            out = 8'b0000_0010;
        else if({in_1,in_2,in_3} == 3'b010)
            out = 8'b0000_0100;
        else if({in_1,in_2,in_3} == 3'b011)
            out = 8'b0000_1000;
        else if({in_1,in_2,in_3} == 3'b100)
            out = 8'b0001_0000;
        else if({in_1,in_2,in_3} == 3'b101)
            out = 8'b0010_0000;
        else if({in_1,in_2,in_3} == 3'b110)
            out = 8'b0100_0000;
        else if({in_1,in_2,in_3} == 3'b111)
            out = 8'b1000_0000;
        else
            out = out;
    endmodule
    ```

  * ![](https://pic.imgdb.cn/item/64e8648a661c6c8e54f64333.jpg)

## 7 时序逻辑的开始（触发器为例）

### 7.1 理论学习

* 组合逻辑最大的缺点就是会存在`竞争冒险`：
  * 竞争冒险问题常常会引起电路的不稳定性和工作时的不确定性。
  * 使用时序逻辑就可极大的避免这种问题，从而使系统更加稳定。
* 时序逻辑最基本的单元就是`寄存器`：
  * 寄存器具有存储功能，一般是由 D 触发器构成，由时钟脉冲控制，每个 D 触发器（D Flip  Flop ，DFF）能够存储一位二进制码。
* D 触发器的功能：
  * 在一个脉冲信号（一般为晶振产生的时钟脉冲）上升沿或下降沿的作用下，将信号从输入端 D 送到输出端 Q，如果时钟脉冲的边沿信号未出现，即使输入信号改变，输出信号仍然保持原值，且寄存器拥有复位清零功能，其复位又分为同步复位和异步复位。
* 区分一个设计是组合逻辑电路还是时序逻辑电路：
  * 主要是看数据工作是不是在时钟沿下进行的，在 FPGA 的设计中，复杂的电路设计都要用到时序逻辑电路，往往都是以时序逻辑电路为主，组合逻辑为辅的混合逻辑电路。

### 7.2 实战演练

#### 7.2.1 实验目标

* 当按键未按下时 led 灯处于熄灭状态；当按键被按下时 led 灯被点亮。
* 使用 D 触发器进行控制，和使用组合逻辑的控制方法有所不同。 

#### 7.2.2 程序设计

* （1）模块框图绘制：

  * 信号命名特点：sys表示系统，sys_xxx_n的n表示低电平有效，如果是高电平有效则不加后缀n。
  * <img src="https://pic.imgdb.cn/item/64ec0ce8661c6c8e54b8ec34.jpg" style="zoom:50%;" />

* （2）绘制波形图

  * D 触发器根据复位的不同分为两种`主要就是复位有效的条件是“立刻”执行还是等待“时钟沿”再执行的区别`：
    * 同步复位的 D 触发器
      * 同步复位的 D 触发器中的“同步”是和工作时钟同步的意思，即，当时钟的上升沿（也可以是下降沿，一般习惯上为上升沿触发）来到时检测到按键的复位操作才有效，否则无效。如图所示最右边的三根红色的竖线表达的就是这种效果，sys_rst_n 被拉低后led_out没有立刻变为0，而是当syc_clk的上升沿到来的时候led_out才复位成功，在复位释放的时候也是相同原因。
      * ![](https://pic.imgdb.cn/item/64ec0d63661c6c8e54b90d24.jpg)

    * 异步复位的 D 触发器
      * 异步复位的 D 触发器中的“异步”是和工作时钟不同步的意思，也就是说，寄存器的复位不关心时钟的上升沿来不来，只要有检测到按键被按下，就立刻执行复位操作。
      * ![](https://pic.imgdb.cn/item/64ec0e78661c6c8e54b99a60.jpg)

  * 时序电路特点：
    * `对于电路中产生的毛刺有着极好的屏蔽作用`： 如上图中间位置的一组红色竖线所示，是模拟在干扰情况下产生的毛刺现象，因为时序电路只有在沿到来时才检测信号是否有效，所以在两个上升沿之间的毛刺都会被自然的过滤掉，可以大大减少毛刺现象产生的干扰，提高了电路中数据的可靠性。
    * `延一拍`：上面两个图最左边的一组红色竖线所表达的就是这个现象。key_in 在复位后的第一个时钟的上升沿来到时拉高，我们可以发现此时 led_out 并没有在同一时刻也跟着拉高。这是因为：
      * 当表达`时序逻辑`时如果时钟和数据是对齐的，则默认当前时钟沿采集到的数据为在该时钟上升沿`前一时刻`的值；
      * 当表达`组合逻辑`时如果时钟和数据是对齐的，则默认当前时钟沿采集到的数据为在该时钟上升沿`同一时刻`的值。

* （5）代码编写

  * 打开“rtl”文件夹新建“.v”文件；

    * 同步复位代码：

      * ``` verilog
        module  flip_flop
        (
            input   wire    sys_clk     ,    //板载晶振传入的50MHz
            input   wire    sys_rst_n   ,
            input   wire    key_in      ,
            
            output  reg     led_out
        );
        
        always@(posedge sys_clk)    //当 always 块中的敏感列表为检测到 sys_clk 上升沿时
            if(sys_rst_n == 1'b0)
                led_out <= 1'b0;    //时序逻辑中赋值语句一定要用非阻塞式赋值
            else
                led_out <= key_in;
        
        endmodule
        ```

    * 异步复位代码：

      * ``` verilog
        module  flip_flop
        (
            input   wire    sys_clk     ,    //板载晶振传入的50MHz
            input   wire    sys_rst_n   ,
            input   wire    key_in      ,
            
            output  reg     led_out
        );
        
        always@(posedge sys_clk or negedge sys_rst_n)    //当 always 块中的敏感列表为检测到 sys_clk 上升沿时
            if(sys_rst_n == 1'b0)
                led_out <= 1'b0;    //时序逻辑中赋值语句一定要用非阻塞式赋值
            else
                led_out <= key_in;
        
        endmodule
        ```

  * 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

  * 导入“rtl”文件中编写完成的“.v”文件，进行编译.

    * 可以使用“RTL Viewer”查看代码综合出的视图。
      * 同步复位D触发器视图![](https://pic.imgdb.cn/item/64ec195f661c6c8e54bd20d3.jpg)
      * 异步复位D触发器![](https://pic.imgdb.cn/item/64ec1b63661c6c8e54bdb6e7.jpg)

#### 7.2.3 仿真验证

* (1)测试代码编写

  * 打开“sim”文件夹新建“.v”文件；

  * ``` verilog
    `timescale  1ns/1ns
    module  tb_flip_flop();
    
    //reg define
    reg     sys_clk     ;
    reg     sys_rst_n   ;
    reg     key_in      ;
    
    //wire deifne
    wire    led_out     ;
    
    //初始化系统时钟、全局复位和输入信号
    initial
        begin
            sys_clk = 1'b1;     //因为要现有时钟，所以时钟信号阻塞赋值，其他信号非阻塞赋值
            sys_rst_n <= 1'b0;
            key_in <= 1'b0;
            #20                 //延迟20个时间单位，延迟20ns
            sys_rst_n <= 1'b1;  //复位信号是低电平有效，先拉低再拉高完成初始化复位
            #210                //为了观察同步复位和异步复位的区别，在复位释放后
                                //电路工作 210ns 后再让复位有效。之所以选择延时 210ns
                                //而不是 200ns 或 220ns，是因为能够使复位信号在时钟下
                                //降沿时复位，能够清晰的看出同步复位和异步复位的差别
            sys_rst_n <= 1'b0;
            #40
            sys_rst_n <= 1'b1;
        end
        
    //sys_clk:模拟系统时钟，每 10ns 电平翻转一次，周期为 20ns，频率为 50MHz
    always  #10 sys_clk = ~sys_clk;
    
    //key_in:产生输入随机数，模拟按键的输入情况
    always  #20 key_in <= {$random} % 2; //取模求余数，产生非负随机数 0、1，每隔 20ns
                                        //产生一次随机数（之所以每 20ns 产生一次随机
                                        //数而不是之前的每 10ns 产生一次随机数，是为
                                        //了在时序逻辑中能够保证 key_in 信号的变化的
                                        //时间小于等于时钟的周期，这样就不会产生类
                                        //似毛刺的变化信号，虽然产生的毛刺在时序电
                                        //路中也能被滤除掉，但是不便于我们观察波形）
    
    //时间打印
    initial
        begin
            $timeformat(-9,0,"ns",6);
            $monitor("@time %t:sys_clk=%b,sys_rst_n=%b,key_in=%b,led_out=%b",$time,sys_clk,sys_rst_n,key_in,led_out);
        end
    
    //实例化
    flip_flop flip_inst
    (
        .sys_clk(sys_clk),
        .sys_rst_n(sys_rst_n),
        .key_in(key_in),
    
        .led_out(led_out)
    );
    
    endmodule
    ```

  * 导入测试文件，进行Modelsim仿真设置；

  * 开始联仿，查看波形图和打印信息：

    * 同步复位：

      ![](https://pic.imgdb.cn/item/64ec54ca661c6c8e54cf1450.jpg)

    * 异步复位：

      ![](https://pic.imgdb.cn/item/64ec55e4661c6c8e54cf79a4.jpg)

  * 与真值表对比即可。


#### 7.2.4 管脚绑定

* sys_clk-->E1
* sys_rst_n-->M15
* key_in---->key1-->M2
* led_out-->led1-->L7

#### 7.2.5 上板子验证

* 点击program按钮导入“.sof”文件；
* 连接板卡，点击“start”按钮下载程序；
* 上板验证；

#### 7.2.6 程序固化

* 详情参见3.1.11

## 8 阻塞赋值与非阻塞赋值

### 8.1 理论学习

* 阻塞赋值`"="`：

  * 对应的**电路结构**往往与`触发沿没有关系`，只与`输入电平的变化有关系`。

  * ``` verilog
    a = 1, b = 2, c = 3,
    begin
    	a = b + 1;
    	b = a + 2;
    	c = a - 1;
    end
    a = 3, b = 5, c = 2,
    ```

  * 执行操作时不允许有来自任何其他 Verilog 语句的干扰，直到现行的赋值完成时刻。

  * 即把当前赋值号右边的值赋值给左边的时刻完成后，它才允许下一条的赋值语句的执行。

  * 串行块（begin-end）中的各条阻塞型过程赋值语句将以它们在顺序块后的`排列次序依次执行`。

  * 阻塞的概念是指在同一个 always 块中，其后面的赋值语句从概念上是在前一句赋值语句结束后再开始下面的赋值。

* 非阻塞赋值`"<="`：

  * 对应的**电路结构**往往与`触发沿有关系`，只有`在触发沿的时刻才能进行非阻塞赋值`。

  * ``` verilog
    a = 1, b = 2, c = 3,
    begin
    	a <= b + 1;
    	b <= a + 2;
    	c <= a - 1;
    end
    a = 3, b = 3, c = 0,
    ```

  * 在计算非阻塞语句赋值号右边的语句和更新赋值号左边的**语句期间**，其他的 Verilog 语句包括其他的 Verilog 非阻塞赋值语句都能同时计算赋值号右边的语句和更新赋值号左边的语句，**允许**其他的 Verilog 语句**同时进行操作**。

  * 两个步骤的过程：在赋值开始时刻，计算赋值号右边的语句。在赋值结束时刻，更新赋值号左边的语句。

  * `注意：`非阻塞操作`只能`用于`对寄存器类型变量进行赋值`，因此只能用于`“initial”`和`“always”`块中，不允许用于连续赋值`“assign”`。

### 8.2 实战演练

#### 8.2.1 程序设计

* （1）代码编写

  * 打开“rtl”文件夹新建“.v”文件；

  * 阻塞赋值：

    * ``` verilog
      module  blocking
      (
          input   wire            sys_clk     ,
          input   wire            sys_rst_n   ,
          input   wire    [1:0]   in          ,
          
          output  reg     [1:0]   out           //因为使用always语句进行赋值，，所以变量是reg型
      );
      
      //定义中间变量，方便仿真波形的观察
      reg     in_reg  ;
      
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n = 1'b0)
              begin
                  in_reg = 2'b0;
                  out    = 2'b0;
              end
          else
              begin
                  in_reg = in;
                  out    = in_reg;
              end
        
      endmodule
      ```

  * 非阻塞赋值：

    * ``` verilog
      module  blocking
      (
          input   wire            sys_clk     ,
          input   wire            sys_rst_n   ,
          input   wire    [1:0]   in          ,
          
          output  reg     [1:0]   out           //因为使用always语句进行赋值，，所以变量是reg型
      );
      
      //定义中间变量，方便仿真波形的观察
      reg     in_reg  ;
      
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n = 1'b0)
              begin
                  in_reg <= 2'b0;
                  out    <= 2'b0;
              end
          else
              begin
                  in_reg <= in;
                  out    <= in_reg;
              end
        
      endmodule
      ```

  * 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

  * 导入“rtl”文件中编写完成的“.v”文件，进行编译.

    * 可以使用“RTL Viewer”查看代码综合出的视图。
      * 阻塞赋值：in-out延迟1个时钟周期![](https://pic.imgdb.cn/item/64edaed8661c6c8e541d9f2a.jpg)
      * 非阻塞赋值：in-out延迟2个时钟周期![](https://pic.imgdb.cn/item/64edaf9e661c6c8e541e0c32.jpg)

#### 8.2.2 仿真验证

* (1)测试代码编写

  * 打开“sim”文件夹新建“.v”文件；

  * ``` verilog
    `timescale 1ns/1ns
    module  tb_blocking();
    
    //reg define
    reg             sys_clk     ;
    reg             sys_rst_n   ;
    reg     [1:0]   in          ;
    
    //wire define
    wire    [1:0]   out         ;
    
    //初始化系统时钟，全局复位和输入信号
    initial
        begin
            sys_clk = 1'b1;
            sys_rst_n <= 1'b0;
            in <= 2'b0;
            #20
            sys_rst_n <= 1'b1;
        end
    
    //时钟频率50MHz
    always  #10 sys_clk = ~sys_clk;
    
    //模拟输入信号两位宽包括：00，01，10，11。
    //所以对4求余，可以生成非负数0，1，2，3
    always  #20 in <= {$random} % 4;
    
    initial
        begin
            $timeformat(-9,0,"ns",6);
            $monitor("@time %t:sys_clk=%b,sys_rst_n=%b,in=%b,out=%b",$time,sys_clk,sys_rst_n,in,out);
        end
        
    blocking    blocking_inst
    (
        .sys_clk  (sys_clk),
        .sys_rst_n(sys_rst_n),
        .in       (in),
        .out      (out)
    );
    endmodule
    ```

  * 导入测试文件，进行Modelsim仿真设置；

  * 开始联仿，查看波形图和打印信息：

    * 阻塞赋值：in-out延迟1个时钟周期

      ![](https://pic.imgdb.cn/item/64edaf26661c6c8e541dcf46.jpg)

    * 非阻塞赋值：in-out延迟2个时钟周期

      ![](https://pic.imgdb.cn/item/64edb06e661c6c8e541e471f.jpg)

  * 与真值表对比即可。


#### 8.2.3 总结

* 在编写时序逻辑的代码时采用非阻塞赋值的方式；
* 使用 always 块来编写组合逻辑的代码时要用阻塞赋值的方式；
* 在同一个 always 块中不要既要用非阻塞赋值又用阻塞方式赋值；
* 虽然锁存器电路建模是我们不推荐的，但是如果使用到要采用非阻塞赋值的方式。
* 一个 always 块只一个变量进行赋值。

## 9 计数器

### 9.1 理论学习

* 计数器的功能：

  * 计数器在数字系统中主要是对脉冲的个数进行计数，以实现测量、计数和控制的功能，同时兼有分频功能。
* 计数器的应用：
  * 计数器在数字系统中应用广泛，如在电子计算机的控制器中对指令地址进行计数，以便顺序取出下一条指令，在运算器中作乘法、除法运算时记下加法、减法次数，又如在数字仪器中对脉冲的计数等等。
* 计数器也是在 FPGA 设计中最常用的一种`时序逻辑电路`。

### 9.2 实战演练

#### 9.2.1 实验目标

* 让计数器计数 1s 时间间隔，实现 led 灯每隔 1s 闪烁一次。

#### 9.2.2 程序设计

* （1）模块框图绘制：

  * 信号命名特点：sys表示系统，sys_xxx_n的n表示低电平有效，如果是高电平有效则不加后缀n。
  * ![](https://pic.imgdb.cn/item/64eeaaef661c6c8e54813264.jpg)

* （2）绘制波形图

  * 计数器分析：
    * 系统时钟为50MHz，也就是一个周期时间为:[1/(50*10^6)Kz]s = 0.000_000_02s = 20ns，那么1秒就会计数50M个，0.5s闪烁一次，所以计数25M个复位1次，又因为计数器从0开始计数，所以0-24_999_999，计数翻转一次。所以需要25位宽的寄存器。(`考虑到资源量的问题，精简化设计`)

  * 有2种实现方法：
    * ①不带标志信号的计数器
      * 当 sys_rst_n 信号有效时，cnt 计数器清零；
      * 当 sys_rst_n 信号撤销后，时钟的上升沿时刻 cnt 计数器开始自加 1；
      * 当 cnt 计数器计数到 N（这里 N = 24_999_999）时清零，只要 sys_rst_n 不复位，该计数器将一直循环计数下去。
      * ![](https://pic.imgdb.cn/item/64eeb3a9661c6c8e5486f9a2.jpg)

    * ②带标志信号的计数器
      * 是再添加一个用于指示 cnt 计数器计数到 N 的脉冲信号 cnt_flag。
      * 当计数器计数到 N 时 led_out 信号先不取反，而是让 cnt_flag 脉冲信号产生一个时钟周期的高脉冲，led_out 信号每当检测到 cnt_flag 脉冲信号为 高时取反，也能够控制外部 led 灯实现闪烁的效果。
      * ![](https://pic.imgdb.cn/item/64eeb671661c6c8e548894f8.jpg)
      * 脉冲标志信号（flag）的优点：
        * 可以减少代码中 if 括号内的条件让代码更加清晰简洁；
        * 当需要在多处使用脉冲标志信号的地方要比全部写出的方式更节约逻辑资源，脉冲标志信号在指示某些状态时是非常有用的。

* （5）代码编写

  * 打开“rtl”文件夹新建“.v”文件；

    * ①不带标志信号的计数器：

      * 使用参数定义常量的好处是：

        * 我们在 RTL 代码中实例化该模块时，如果需要**两个不同计数值的计数器**我们不必设计两个模块，而是**直接修改参数**的值即可。
        * 写 Testbench 进行仿真时我们也需要实例化该模块，但是我们需要仿真至少 0.5s 的时间才能够看出到 led_out 效果，这会让仿真时间很长，也会导致产生的仿真文件很大，所以`可以通过直接修改参数的方式来缩短仿真的时间而看到相同的效果`，且不会影响到 RTL 代码模块中的实际值，因为 `parameter 定义的是局部参数`，所以只在本模块中有效。为了更好的区分，`参数名`习惯上都要`大写`。

      * ``` verilog
        module  counter
        /*使用参数的方式定义常量*/
        #(parameter     CNT_MAX = 25'd24_999_999)//参数传递法可以定义在模块外部可以在实例化时候进行参数修改
        
        (
            input       wire    sys_clk     ,
            input       wire    sys_rst_n   ,
            
            output      reg     led_out
        );
        
        reg     [24:0]  cnt;
        
        //cnt:计数器计数，当计数到 CNT_MAX 的值时清零
        always@(posedge sys_clk or negedge sys_rst_n)//异步复位
            if(sys_rst_n == 1'b0)
                cnt <= 25'd0;
            else    if(cnt == CNT_MAX)
                cnt <= 25'd0;
            else
                cnt <= cnt + 1'b1;
                
        //led_out:输出控制一个 LED 灯，每当计数满标志信号有效时取反
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                led_out <= 1'b0;
            else    if(cnt == CNT_MAX)
                led_out <= ~led_out;
        
        endmodule
        ```

    * ②带标志信号的计数器

      * ``` verilog
        module  counter
        /*使用参数的方式定义常量*/
        #(parameter     CNT_MAX = 25'd24_999_999)//参数传递法可以定义在模块外部可以在实例化时候进行参数修改
        
        (
            input       wire    sys_clk     ,
            input       wire    sys_rst_n   ,
            
            output      reg     led_out
        );
        
        reg     [24:0]  cnt;
        reg             cnt_flag;//带标志位计数器
        //cnt:计数器计数，当计数到 CNT_MAX 的值时清零
        always@(posedge sys_clk or negedge sys_rst_n)//异步复位
            if(sys_rst_n == 1'b0)
                cnt <= 25'd0;
            else    if(cnt == CNT_MAX)
                cnt <= 25'd0;
            else
                cnt <= cnt + 25'd1;
        
        //cnt_flag：计数器标志位
        always@(posedge sys_clk or negedge sys_rst_n)//异步复位
            if(sys_rst_n == 1'b0)
                cnt_flag <= 1'b0;
            else    if(cnt == (CNT_MAX - 25'd1))
                cnt_flag <= 1'b1;
            else
                cnt_flag <= 1'b0;
        
        //led_out:输出控制一个 LED 灯，每当计数满标志信号有效时取反
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                led_out <= 1'b0;
            else    if(cnt_flag == 1'b1)
                led_out <= ~led_out;
            else
                led_out <= led_out;
        
        endmodule
        ```

  * 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

  * 导入“rtl”文件中编写完成的“.v”文件，进行编译.

    * 可以使用“RTL Viewer”查看代码综合出的视图。
      * ①不带标志信号的计数器：![](https://pic.imgdb.cn/item/64eef1f1661c6c8e54ac48d3.jpg)
      * ②带标志信号的计数器![](https://pic.imgdb.cn/item/64eeff79661c6c8e54b112b6.jpg)

#### 9.2.3 仿真验证

* (1)测试代码编写

  * 打开“sim”文件夹新建“.v”文件；

  * ``` verilog
    `timescale  1ns/1ns
    module  tb_counter();
    
    reg     sys_clk     ;
    reg     sys_rst_n   ;
    
    wire    led_out     ;
    
    //初始化输入信号
    initial
        begin
            sys_clk = 1'b1;
            sys_rst_n <= 1'b0;
            #20
            sys_rst_n <= 1'b1;
        end
    //产生时钟信号，定义下频率，每10ns翻转一次电平，20ns周期
    always  #10 sys_clk = ~sys_clk;
    
    //时间打印
    initial
        begin
            $timeformat(-9,0,"ns",6);
            $monitor("@time %t:led_out=%b",$time,led_out);
        end
    
    //实例化
    counter//添加参数传递后，实例化名称count_inst写在端口列表前
    #(
        .CNT_MAX (25'd24)
    )
    counter_inst
    (
        .sys_clk   (sys_clk),
        .sys_rst_n (sys_rst_n),
    
        .led_out   (led_out)
    );
    
    endmodule
    ```

  * 导入测试文件，进行Modelsim仿真设置；

  * 开始联仿，查看波形图和打印信息：

    * ①不带标志信号的计数器：

      ![](https://pic.imgdb.cn/item/64eef889661c6c8e54aeaf15.jpg)

      ![](https://pic.imgdb.cn/item/64eef938661c6c8e54aef055.jpg)

    * ②带标志信号的计数器

      ![](https://pic.imgdb.cn/item/64eeff5d661c6c8e54b103be.jpg)

      ![](https://pic.imgdb.cn/item/64eef938661c6c8e54aef055.jpg)

  * 与真值表对比即可。


#### 9.2.4 管脚绑定

* sys_clk-->E1
* sys_rst_n-->M15
* led_out-->led1-->L7

#### 9.2.5 上板子验证

* 点击program按钮导入“.sof”文件；
* 连接板卡，点击“start”按钮下载程序；
* 上板验证；

#### 9.2.6 程序固化

* 详情参见3.1.11



## 10 分频器

### 10.1 理论学习

* 分频与倍频：
  * 若是想得到比固定的时钟频率`更慢`的时钟，可以将该固定时钟进行`分频`；
  * 若是想得到比固定时钟频率`更快`的时钟，则可以在固定时钟频率的基础上进行`倍频`。

* 分频的方法：
  * ①器件厂商提供的锁相环（PLL，后面章节会讲解）；
  * ②是自己动手 来用 Verilog 代码描述；而我们用 Verilog 代码描述的往往是分频电路，即分频器。

* 分频：
  * 是把输入信号的频率变成成倍数地低于输入频率的输出信号。


* 分频器原理：
  * 把输入的信号作为计数脉冲，由于计数器的输出端口是按一定规律输出脉冲的，所以对不同的端口输出的信号脉冲，就可以看作是对输入信号的“分频”。
  * 至于分频频率是怎样的，由选用的计数器所决定：
    * 如果是十进制的计数器那就是十分频；
    * 如果是二进制的计数器那就是二分频；
    * ......
  * 分频器是和计数器非常类似的功能，上一章节led_out信号就是对时钟信号sys_clk进行了分频。

### 10.2 实战演练（偶数分频）

#### 10.2.1 实验目标

* 实现对系统时钟进行 6 分频的 偶数分频电路。

#### 10.2.2 程序设计

* （1）模块框图绘制：

  * 信号命名特点：sys表示系统，sys_xxx_n的n表示低电平有效，如果是高电平有效则不加后缀n。
  * ![](https://pic.imgdb.cn/item/64f0044d661c6c8e54e4d51f.jpg)

* （2）绘制波形图

  * 有2种实现方法：
    * ①仅实现分频功能
      * 我们需要对输入的系统时钟时钟进行 6 分频，只需要让计数器从 0 计数到 2，即计 3 个数就可以了，然后每当计数器计数到 2 的时候就让 clk_out 输出信号取反即可；
      * ![](https://pic.imgdb.cn/item/64f006c2661c6c8e54e5c087.jpg)
      * `弊端`：所衍生的潜在问题在低速系统中不易察觉，而在高速系统中就很容易出现问题。
      * 这种种方式分频得到的时钟≠真正的时钟信号。因为这种分频的方式产生的 clk_out 信号并没有连接到全局时钟网络上。
    * ②实用的降频方法：
      * 可以产生一个用于标记 6 分频的 clk_flag 标志信号，这样每两clk_flag 脉 冲之间的频率就是对 sys_clk 时钟信号的 6 分频：
      * ![](https://pic.imgdb.cn/item/64effce0661c6c8e54e2cb4c.jpg)
      * 脉冲标志信号（clk_flag）的优点：
        * 为后级模块实现相同的降频效果；
        * 虽然这样会多使用一些寄存器资源，不过不用担心我们的系统是完全可以承担的起的，而得到的好处却远远大于这点资源的使用，能让系统更加稳定;
        * 产生的信号 A 是在 sys_clk 系统时钟的控制下产生的，和所有在 sys_clk 系统时钟下产生的信号都保持几乎相同的时钟关系，方法更优。（这计划不好理解，看下面这幅图，其中A为后级模块）
          * ![](https://pic.imgdb.cn/item/64f05fc3661c6c8e54039041.jpg)

* （3）代码编写

  * 打开“rtl”文件夹新建“.v”文件；

    * ①仅实现分频功能（分频方法）：

      * ``` verilog
        module  divider_six
        //定义模块输入输出变量
        (
            input   wire    sys_clk     ,
            input   wire    sys_rst_n   ,
            
            output  reg     clk_out     //因为赋值的时候在always语句中，所以要用reg型
        );
        
        //定义中间变量
        reg     [1:0]       cnt;
        
        //************仅实现分频功能************//
        //cnt：计数器计数，当从0计数到2时清零
        always@(posedge sys_clk or negedge sys_rst_n)//异步复位
            if(sys_rst_n == 1'b0)
                cnt <= 2'd0;
            else    if(cnt == 2'd2)
                cnt <= 2'd0;
            else
                cnt <= cnt + 2'd1;
        
        //clk_out:输出6分频50%占空比
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                clk_out <= 1'b0;
            else    if(cnt == 2'd2)
                clk_out <= ~clk_out;
            else
                clk_out <= clk_out;
        
        endmodule
        ```

    * ②实用的降频方法（降频方法）：

      * ``` verilog
        module  divider_six
        //定义模块输入输出变量
        (
            input   wire    sys_clk     ,
            input   wire    sys_rst_n   ,
            
            //output  reg     clk_out     //因为赋值的时候在always语句中，所以要用reg型
            output  reg     clk_flag
        );
        
        //定义中间变量
            reg     [2:0]       cnt;//这里要注意计数器数值增加，位数也要增加，否则无法完成计数
        
        //************实用的降频方法************//
        //cnt：计数器计数，当从0计数到2时清零
        always@(posedge sys_clk or negedge sys_rst_n)//异步复位
            if(sys_rst_n == 1'b0)
                cnt <= 3'd0;
            else    if(cnt == 3'd5)
                cnt <= 3'd0;
            else
                cnt <= cnt + 3'd1;
        
        //clk_flag:冲信号指示 6 分频
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                clk_flag <= 1'b0;
            else    if(cnt == 3'd4)
                clk_flag <= 1'b1;
            else
                clk_flag <= 1'b0;
        
        endmodulemodule  counter
        
        endmodule
        ```

  * 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

  * 导入“rtl”文件中编写完成的“.v”文件，进行编译.

    * 可以使用“RTL Viewer”查看代码综合出的视图。
      * ①仅实现分频功能：![](https://pic.imgdb.cn/item/64f008b9661c6c8e54e67d40.jpg)
      * ②实用的降频方法：![](https://pic.imgdb.cn/item/64f008d1661c6c8e54e688d1.jpg)


#### 10.2.3 仿真验证

* (1)测试代码编写

  * 打开“sim”文件夹新建“.v”文件；

    * ①仅实现分频功能（分频方法）：

      * ``` verilog
        `timescale  1ns/1ns
        module  tb_divider_six();
        
        reg     sys_clk     ;
        reg     sys_rst_n   ;
        
        wire    clk_out     ;
        
        //初始化输入信号
        initial
            begin
                sys_clk = 1'b1;
                sys_rst_n <= 1'b0;
                #20
                sys_rst_n <= 1'b1;
            end
        
        //产生系统时钟
        always  #10 sys_clk = ~sys_clk;
        
        //实例化
        divider_six divider_six_inst
        (
            .sys_clk  (sys_clk),
            .sys_rst_n(sys_rst_n),
            
            .clk_out  (clk_out)
        );
        endmodule
        ```

    * ②实用的降频方法（降频方法）：

      * ``` verilog
        `timescale  1ns/1ns
        module  tb_divider_six();
        
        reg     sys_clk     ;
        reg     sys_rst_n   ;
        
        wire    clk_flag     ;
        
        //初始化输入信号
        initial
            begin
                sys_clk = 1'b1;
                sys_rst_n <= 1'b0;
                #20
                sys_rst_n <= 1'b1;
            end
        
        //产生系统时钟
        always  #10 sys_clk = ~sys_clk;
        
        //实例化
        divider_six divider_six_inst
        (
            .sys_clk  (sys_clk),
            .sys_rst_n(sys_rst_n),
            
            .clk_flag  (clk_flag)
        );
        endmodule
        ```

  * 导入测试文件，进行Modelsim仿真设置；

  * 开始联仿，查看波形图和打印信息：

    * ①仅实现分频功能：

      ![](https://pic.imgdb.cn/item/64f00bb6661c6c8e54e7964e.jpg)

    * ②实用的降频方法：

      ![](https://pic.imgdb.cn/item/64f06203661c6c8e5404743c.jpg)

  * 与真值表对比即可。

#### 10.2.4 管脚绑定

* ①仅实现分频功能：

  * sys_clk-->E1

  * sys_rst_n-->M15

  * clk_out-->F15


* ②实用的降频方法：
  * sys_clk-->E1
  * sys_rst_n-->M15
  * clk_flag-->F15


#### 10.2.5 上板子验证

* 点击program按钮导入“.sof”文件；
* 连接板卡，点击“start”按钮下载程序；
* 上板验证；

#### 10.2.6 程序固化

* 详情参见3.1.11

### 10.3 实战演练（奇数分频）

#### 10.3.1 实验目标

* 实现对系统时钟进行 5分频的奇数分频电路。

#### 10.3.2 程序设计

* （1）模块框图绘制：

  * 信号命名特点：sys表示系统，sys_xxx_n的n表示低电平有效，如果是高电平有效则不加后缀n。
  * ![](https://pic.imgdb.cn/item/64f152b1661c6c8e543b0aa4.jpg)

* （2）绘制波形图

  * 有2种实现方法：
    * ①仅实现分频功能
      * 我们需要对输入的系统时钟时钟进行 5 分频，clk1是上升沿触发，clk2是下降沿触发。只需要让clk1和clk2取或运算即可获得占空比为50%的5分频；
      * ![](https://pic.imgdb.cn/item/64f152dc661c6c8e543b1532.jpg)
      * `弊端`：所衍生的潜在问题在低速系统中不易察觉，而在高速系统中就很容易出现问题。
      * 这种种方式分频得到的时钟≠真正的时钟信号。因为这种分频的方式产生的 clk_out 信号并没有连接到全局时钟网络上。
    * ②实用的降频方法：
      * 可以产生一个用于标记 5 分频的 clk_flag 标志信号，这样每两clk_flag 脉 冲之间的频率就是对 sys_clk 时钟信号的 5 分频：
      * ![](https://pic.imgdb.cn/item/64f15f69661c6c8e543ed260.jpg)
      * 脉冲标志信号（clk_flag）的优点：
        * 为后级模块实现相同的降频效果；
        * 虽然这样会多使用一些寄存器资源，不过不用担心我们的系统是完全可以承担的起的，而得到的好处却远远大于这点资源的使用，能让系统更加稳定;
        * 产生的信号 A 是在 sys_clk 系统时钟的控制下产生的，和所有在 sys_clk 系统时钟下产生的信号都保持几乎相同的时钟关系，方法更优。（这计划不好理解，看下面这幅图，其中A为后级模块）
          * ![](https://pic.imgdb.cn/item/64f05fc3661c6c8e54039041.jpg)

* （3）代码编写

  * 打开“rtl”文件夹新建“.v”文件；

    * ①仅实现分频功能（分频方法）：

      * ``` verilog
        module  divider_five
        //定义模块输入输出变量
        (
            input       wire        sys_clk     ,
            input       wire        sys_rst_n   ,
            
            output      wire        clk_out
        );
        
        //定义中间变量
        reg     [2:0]       cnt     ;
        reg                 clk_1    ;
        reg                 clk_2    ;
        
        //************仅实现分频功能************//
        //cnt：计数器计数，当从0计数到2时清零
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                cnt <= 3'd0;
            else    if(cnt == 3'd4)
                cnt <= 3'd0;
            else
                cnt <= cnt + 3'd1;
        
        //clk_1：上升沿触发，占空比低电平维持 3 个系统时钟周期，高电平维持 2 个系统时钟周期
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                clk_1 <= 1'b0;
            else    if(cnt == 3'd2)
                clk_1 <= 1'b1;
            else    if(cnt == 3'd4)
                clk_1 <= 1'b0;
            else
                clk_1 <= clk_1;
                
        //clk_2：下降沿触发，占空比低电平维持 3 个系统时钟周期，高电平维持 2 个系统时钟周期
        always@(negedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                clk_2 <= 1'b0;
            else    if(cnt == 3'd2)
                clk_2 <= 1'b1;
            else    if(cnt == 3'd4)
                clk_2 <= 1'b0;
            else
                clk_2 <= clk_2;
        
        //clk_out：50%占空比5分频输出
        assign  clk_out = (clk_1 | clk_2);
        
        endmodule
        ```

    * ②实用的降频方法（降频方法）：

      * ``` verilog
        module  divider_five
        //定义模块输入输出变量
        (
            input       wire        sys_clk     ,
            input       wire        sys_rst_n   ,
            
            output      reg         clk_flag
        );
        
        //定义中间变量
        reg     [2:0]       cnt     ;
        
        //cnt：计数器计数，当从0计数到2时清零
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                cnt <= 3'd0;
            else    if(cnt == 3'd4)
                cnt <= 3'd0;
            else
                cnt <= cnt + 3'd1;
        
        //************实用的降频方法************//
        //clk_flag:冲信号指示 6 分频
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                clk_flag <= 1'b0;
            else    if(cnt == 3'd3)
                clk_flag <= 1'b1;
            else
                clk_flag <= 1'b0;
        
        endmodule
        ```

  * 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

  * 导入“rtl”文件中编写完成的“.v”文件，进行编译.

    * 可以使用“RTL Viewer”查看代码综合出的视图。
      * ①仅实现分频功能：![](https://pic.imgdb.cn/item/64f15972661c6c8e543d64db.jpg)
      * ②实用的降频方法：![](https://pic.imgdb.cn/item/64f15f25661c6c8e543eb965.jpg)


#### 10.2.3 仿真验证

* （1）测试代码编写

  * 打开“sim”文件夹新建“.v”文件；

    * ①仅实现分频功能（分频方法）：

      * ``` verilog
        `timescale  1ns/1ns
        module  tb_divider_five();
        
        reg     sys_clk     ;
        reg     sys_rst_n   ;
        
        wire    clk_out     ;
        
        //初始化输入信号
        initial
            begin
                sys_clk = 1'b1;
                sys_rst_n <= 1'b0;
                #20
                sys_rst_n <= 1'b1;
            end
        
        //产生系统时钟
        always  #10 sys_clk = ~sys_clk;
        
        //实例化
        divider_five divider_five_inst
        (
            .sys_clk  (sys_clk),
            .sys_rst_n(sys_rst_n),
            
            .clk_out  (clk_out)
        );
        
        endmodule
        ```

    * ②实用的降频方法（降频方法）：

      * ```verilog
        `timescale  1ns/1ns
        module  tb_divider_five();
        
        reg     sys_clk     ;
        reg     sys_rst_n   ;
        
        wire    clk_flag     ;
        
        //初始化输入信号
        initial
            begin
                sys_clk = 1'b1;
                sys_rst_n <= 1'b0;
                #20
                sys_rst_n <= 1'b1;
            end
        
        //产生系统时钟
        always  #10 sys_clk = ~sys_clk;
        
        //实例化
        divider_five divider_five_inst
        (
            .sys_clk  (sys_clk),
            .sys_rst_n(sys_rst_n),
            
            .clk_flag  (clk_flag)
        );
        
        endmodule
        ```

* （2）导入测试文件，进行Modelsim仿真设置；

* （3）开始联仿，查看波形图和打印信息：

  * ①仅实现分频功能：

    ![](https://pic.imgdb.cn/item/64f15b1f661c6c8e543dbcdb.jpg)

  * ②实用的降频方法：

    ![](https://pic.imgdb.cn/item/64f160bc661c6c8e543f44f5.jpg)

#### 10.2.4 管脚绑定

* ①仅实现分频功能：

  * sys_clk-->E1

  * sys_rst_n-->M15

  * clk_out-->F15


* ②实用的降频方法：
  * sys_clk-->E1
  * sys_rst_n-->M15
  * clk_flag-->F15


#### 10.2.5 上板子验证

* 点击program按钮导入“.sof”文件；
* 连接板卡，点击“start”按钮下载程序；
* 上板验证；

#### 10.2.6 程序固化

* 详情参见3.1.11

## 11 按键消抖模块的设计与验证

### 11.1 理论学习

* 机械弹性开关：
  * 当机械触点断开、闭合时，由于机械触点的弹性作用，一个按键开关在闭合时不会马上稳定地接通，在断开时也不会一下子断开。因而在闭合及断开的瞬间均伴随有一连串的抖动，为了不产生这种现象而做的措施就是按键消抖。
  * ![](https://pic.imgdb.cn/item/64f2a7d7661c6c8e54a459b9.jpg)
* 硬件消抖：
  * RS 触发器为常用的硬件去抖。图中两个与非门构成一个 RS 触发器。当按键未按下时，输出为 0；当键按下时，输出为 1。
  * ![](https://pic.imgdb.cn/item/64f2a8f1661c6c8e54a4c9ea.jpg)
  * 此时即使用按键的机械性能，使按键因弹性抖动而产生瞬时断开(抖动跳开 B)，只要按键不返回原始状态 A，双稳态电路的状态不改变，输出保持为 0，不会产生抖动的波形。
  * 也就是说，即使 B 点的电压波形是抖动的，但经双稳态电路之后，其输出为正规的矩形波。这一点通过分析 RS 触发器的工作过程很容易得到验证。
* 软件消抖：
  * 如果按键个数较多，常用软件方法去抖，即检测出按键闭合后执行一个延时程序，根据抖动的时间为5ms~10ms，我们产生一个 20ms 的延时，让前沿抖动消失后再一次检测键的状态，如果仍保持闭合状态电平，则确认为真正有键按下。

### 11.2 实战演练

#### 11.2.1 实验目标

* 设计并实现一个按键消抖模块，将外部输入的单比特按键信号做消抖处理后输出，输出信号正常可被其他模块调用。

#### 11.2.2 程序设计

* （1）模块框图绘制：

  * 信号命名特点：sys表示系统，sys_xxx_n的n表示低电平有效，如果是高电平有效则不加后缀n。
  * ![](https://pic.imgdb.cn/item/64f2e10c661c6c8e54beb6ec.jpg)

* （2）绘制波形图

  * 添加一个名为 cnt_20ms 用于计数 20ms 时间的计数 器，每当系统检测到按键输入信号为低电平时 cnt_20ms 计数器就开始计数，在 cnt_20ms 计数器计数期间内，如果再次检测到按键为高电平则说明上次检测到的低电平一定是个抖动，那么我们就将这个计数器清零;
  * 考虑 cnt_20ms 计数器计数个数和计数满了后该怎么处理以及滤除抖动后的输出信号 key_flag 什么时候拉高、拉低的问题。
  * 50MHz晶振计数一次的周期为20ns，20ms需要1000_000个周期，一次从0开始计数需要到999_999即为20ms。
  * cnt_20ms 计数器计数到 999_998 的次数只有一个，而且最接近 999_999，在既保证去抖动时间的前提下使 key_flag 信号只产生一个脉冲信号：
  * ![](https://pic.imgdb.cn/item/64f2ebe9661c6c8e54c37080.jpg)

* （3）代码编写

  * 打开“rtl”文件夹新建“.v”文件；

    * ``` verilog
      module  key_filter
      /*使用参数的方式定义常量*/
      #(parameter     CNT_MAX = 20'd999_999)//参数传递法可以定义在模块外部可以在实例化时候进行参数修改
      
      //定义模块输入输出变量
      (
          input   wire    sys_clk     ,
          input   wire    sys_rst_n   ,
          input   wire    key_in      ,
          
          output  reg     key_flag    
      );
      
      //定义中间变量
      reg     [19:0]       cnt_20ms    ;//这里要注意计数器数值增加，位数也要增加，否则无法完成计数
      
      //cnt_20ms：计数器计数，当检测到低电平时开始计数，计数到999_999保持，等待低电平清零
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              cnt_20ms <= 20'd0;
          else    if(key_in == 1'b1)
              cnt_20ms <= 1'b0;
          else    if(cnt_20ms == CNT_MAX)
              cnt_20ms <= cnt_20ms;
          else
              cnt_20ms <= cnt_20ms + 20'd1;
      
      //key_flag：计数器标志位，当计数到999_998的时候进来一个脉冲信号
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              key_flag <= 1'b0;
          else    if(cnt_20ms == (CNT_MAX - 20'd1))
              key_flag <= 1'b1;
          else
              key_flag <= 1'b0;
      
      endmodule
      ```

  * 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

  * 导入“rtl”文件中编写完成的“.v”文件，进行编译.

    * 可以使用“RTL Viewer”查看代码综合出的视图。
      * ![](https://pic.imgdb.cn/item/64f2f248661c6c8e54c53699.jpg)


#### 11.2.3 仿真验证

* (1)测试代码编写

  * 打开“sim”文件夹新建“.v”文件；

    * ``` verilog
      `timescale  1ns/1ns
      module  tb_key_filter();
      
      //parameter define
      //为了缩短仿真时间，我们将参数化的时间值改小
      //但位宽依然定义和参数名的值保持一致
       //也可以将这些参数值改成和参数名的值一致
      
      parameter   CNT_1MS = 8'd19    ,
                  CNT_11MS = 8'd49   ,
                  CNT_41MS = 8'd149  ,
                  CNT_51MS = 8'd199  ,
                  CNT_60MS = 8'd249  ;
      
      reg     sys_clk     ;
      reg     sys_rst_n   ;
      reg     key_in      ;
      reg     [7:0]   tb_cnt      ;//用于模拟按键按下，抖动次数和低电平保持
      
      wire    key_flag    ;
      
      //初始化输入信号
      initial
          begin
              sys_clk = 1'b1;
              sys_rst_n <= 1'b0;
              #20
              sys_rst_n <= 1'b1;
          end
      
      //产生时钟信号，定义下频率，每10ns翻转一次电平，20ns周期
      always  #10 sys_clk = ~sys_clk;
      
      //按键过程计数器，通过该计数器的计数时间来模拟按键的抖动过程
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              tb_cnt <= 8'd0;
          else    if(tb_cnt == 8'd249)
              tb_cnt <= 8'd0;
          else
              tb_cnt <= tb_cnt + 8'd1;
      
      //产生输入随机数，模拟按键的输入情况
      always@(posedge sys_clk or negedge sys_rst_n)
          //按键未按下时的状态为为高电平
          if(sys_rst_n == 1'b0)
              key_in <= 1'b1;
          //在该计数区间内产生非负随机数 0、1 来模拟 10ms 的前抖动和 10ms 的后抖动
          else    if((tb_cnt >= CNT_1MS && tb_cnt <= CNT_11MS)
                      || (tb_cnt >= CNT_41MS && tb_cnt <= CNT_51MS))
              key_in <= {$random} % 2;
          else    if(tb_cnt > CNT_11MS && tb_cnt < CNT_41MS)
              key_in <= 1'b0;
          else
              key_in <= 1'b1;
      
      key_filter//添加参数传递后，实例化名称count_inst写在端口列表前
      #(
          .CNT_MAX (20'd24)
          //修改的 CNT_MAX 值一定要小于(CNT_41MS - CNT_11MS)
          //否则就会表现为按键一直处于“抖动状态”而没有“稳定状态”
          //无法模拟出按键消抖的效果
      )
      key_filter_inst
      (
          .sys_clk  (sys_clk),
          .sys_rst_n(sys_rst_n),
          .key_in   (key_in),
          
          .key_flag (key_flag)
      );
      endmodule
      ```

* （2）导入测试文件，进行Modelsim仿真设置；

* （3）开始联仿，查看波形图和打印信息：

  * ![](https://pic.imgdb.cn/item/64f2ffc1661c6c8e54c87d70.jpg)



## 12 触摸按键控制 LED

### 12.1 理论学习

* 触摸式按键可分为四大类（电阻式、红外线式于、表面波式触摸技术主要应用于触摸屏上，单个按键很少使用。）：
  * 电阻式：
    * 电阻式的工作原理就是使用人体迫压电阻，致使电阻大小改变。电阻的改变会致使整个电路信号出现变化，从而达到控制效果。
    * 缺点：迫压必须到一定程度，否则就无法达到其目的。
  * 电容式：
    * 主要是为了克服电阻屏的耐用性提出的，电容式触摸按键的结构与电阻式的相似，但其是采用电容量为判断标准。
    * ![](https://pic.imgdb.cn/item/64f58557661c6c8e544568fc.jpg)
  * 红外线式：
    * 利用光线对电流的影响，从而达到控制电路的作用，具体的工作原理为：每一个红外触摸按键都会发出光线，如果我们的手指将其中的红外光线遮挡，红外光线则会对电流进行控制从而达到对电路的控制。
  * 表面波式感应按键：
    * 利用声波扫描来识别是否按下。

### 12.2 实战演练

#### 12.2.1 实验目标

* 使用触摸按键去控制 led 灯的亮灭，led 灯初始状态为熄灭状态，当按下触摸按键时 led 灯点亮，当再次按下触摸按键时 led 灯熄灭。

#### 12.2.2 程序设计

* （1）模块框图绘制：

  * 信号命名特点：sys表示系统，sys_xxx_n的n表示低电平有效，如果是高电平有效则不加后缀n。
  * ![](https://pic.imgdb.cn/item/64f58f1f661c6c8e5447e7cd.jpg)

* （2）绘制波形图

  * 添加一个名为 cnt_20ms 用于计数 20ms 时间的计数 器，每当系统检测到按键输入信号为低电平时 cnt_20ms 计数器就开始计数，在 cnt_20ms 计数器计数期间内，如果再次检测到按键为高电平则说明上次检测到的低电平一定是个抖动，那么我们就将这个计数器清零;
  * ![](https://pic.imgdb.cn/item/64f58efe661c6c8e5447da80.jpg)

* （3）代码编写

  * 打开“rtl”文件夹新建“.v”文件；

    * ``` verilog
      module  touch_ctrl_led
      //端口列表，定义输入输出信号
      (
          input       wire        sys_clk     ,
          input       wire        sys_rst_n   ,
          input       wire        touch_key   ,
          
          output      reg         led         
      );
      
      //中间变量声明：touch_key_1和2是时序逻辑，用always语句（延迟1拍）；touch_flag是组合逻辑，用assign语句
      reg     touch_key_1     ;
      reg     touch_key_2     ;
      wire    touch_flag      ;
      
      //中间变量touch_key_1和2赋值
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              begin
                  touch_key_1 <= 1'b1;
                  touch_key_2 <= 1'b1;
              end
          else
              begin
                  touch_key_1 <= touch_key;
                  touch_key_2 <= touch_key_1;
              end
      
      //中间变量touch_flag赋值
      assign  touch_flag = ((touch_key_1 == 1'b0) && (touch_key_2 == 1'b1));
      
      //输出信号led赋值
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              led <= 1'b1;
          else    if(touch_flag == 1'b1)
              led <= ~led;
          else
              led <= led;
      
      endmodule
      ```

    * 这里要提到边沿检测：

      * 边沿检测作用：

        * 能够准确的识别出单比特信号的上升沿或下降沿，也就是我们希望当上升沿或下降沿来到时，能够产生一个唯一标识上升沿或下降沿的脉冲信号来告诉我们上升沿 或下降沿来了，我们就可以根据这个脉冲信号作为后续电路功能的启动。

        * ![](https://pic.imgdb.cn/item/64f596b6661c6c8e544a9aef.jpg)

        * 我们对同一信号打一拍后在①位置处就可以检测到上升沿，使之拉高一个时钟的脉冲；在②位置处可以检测到下降沿，使之拉高一个时钟的脉冲。

      * **上升沿**检测核心代码：在①处检测到 data 为高电平且 data_reg 为低电平时，表示有上升沿产生。（时序逻辑）

        * 方法1：与逻辑实现

          * ``` verilog
            always@(posedge sys_clk or negedge sys_rst_n)
            	if(sys_rst_n == 1'b0)
            		podge <= 1'b0;
            else if((touch_key_1 == 1'b1) && (touch_key_2 == 1'b0)) //核心逻辑
            		podge <= 1'b1;
            	else 
            		podge <= 1'b0;
            ```

        * 方法2：或逻辑实现

          * ``` verilog
            always@(posedge sys_clk or negedge sys_rst_n)
            	if(sys_rst_n == 1'b0)
            		podge <= 1'b0;
            else if((touch_key_1 == 1'b0) || (touch_key_2 == 1'b1)) //核心逻辑
            		podge <= 1'b0;
            	else 
            		podge <= 1'b1;
            ```

      * **下降沿**检测核心代码：在②处检测到 data 为低电平且 data_reg 为高电平时，表示有下降沿产生，和上升沿的情况刚好相反。（时序逻辑）

        * 方法1：与逻辑实现

          * ``` verilog
            always@(posedge sys_clk or negedge sys_rst_n)
            	if(sys_rst_n == 1'b0)
            		podge <= 1'b0;
            else if((touch_key_1 == 1'b0) && (touch_key_2 == 1'b1)) //核心逻辑
            		podge <= 1'b1;
            	else 
            		podge <= 1'b0;
            ```

        * 方法2：或逻辑实现

          * ``` verilog
            always@(posedge sys_clk or negedge sys_rst_n)
            	if(sys_rst_n == 1'b0)
            		podge <= 1'b0;
            else if((touch_key_1 == 1'b1) || (touch_key_2 == 1'b0)) //核心逻辑
            		podge <= 1'b0;
            	else 
            		podge <= 1'b1;
            ```

      * 其核心逻辑也可以结合三目运算符用组合逻辑 **assign** 来实现，会使 podge 和 nedge 检测到上升沿和下降沿的脉冲均会**提前一拍**。

  * 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

  * 导入“rtl”文件中编写完成的“.v”文件，进行编译.

    * 可以使用“RTL Viewer”查看代码综合出的视图。
      * ![](https://pic.imgdb.cn/item/64f59945661c6c8e544b3fae.jpg)


#### 12.2.3 仿真验证

* (1)测试代码编写

  * 打开“sim”文件夹新建“.v”文件；

    * ``` verilog
      `timescale  1ns/1ns
      module  tb_touch_ctrl_led();
      
      //输入输出变量声明
      reg     sys_clk     ;
      reg     sys_rst_n   ;
      reg     touch_key   ;
      
      wire    led         ;
      
      //初始化输入信号
      initial
          begin
              sys_clk = 1'b1;
              sys_rst_n <= 1'b0;
              touch_key <= 1'b1;
              #20
              sys_rst_n <= 1'b1;
              #200
              touch_key <= 1'b0;
              #2000
              touch_key <= 1'b1;
              #1000
              touch_key <= 1'b0;
              #3000
              touch_key <= 1'b1;
          end
      
      //产生系统时钟
      always  #10 sys_clk = ~sys_clk;
      
      //实例化
      touch_ctrl_led touch_ctrl_led_inst
      (
          .sys_clk  (sys_clk),
          .sys_rst_n(sys_rst_n),
          .touch_key(touch_key),
          
          .led  (led)
      );
      
      endmodule
      ```

* （2）导入测试文件，进行Modelsim仿真设置；

* （3）开始联仿，查看波形图和打印信息：

  * ![](https://pic.imgdb.cn/item/64f59b7b661c6c8e544bd52c.jpg)

#### 12.2.4 管脚绑定

* sys_clk-->E1
* sys_rst_n-->M15
* touch_key-->K11
* led-->L7


#### 12.2.5 上板子验证

* 点击program按钮导入“.sof”文件；
* 连接板卡，点击“start”按钮下载程序；
* 上板验证；

#### 12.2.6 程序固化

* 详情参见3.1.11



## 13 流水灯

### 13.1 理论学习

* 懂的都懂

### 13.2 实战演练

#### 13.2.1 实验目标

* 依次点亮板载的 4 个 LED 灯，实现流水灯的效果，两灯之间点亮间隔为 0.5s，LED 灯 一次点亮持续时间 0.5s

#### 13.2.2 程序设计

* （1）模块框图绘制：

  * 信号命名特点：sys表示系统，sys_xxx_n的n表示低电平有效，如果是高电平有效则不加后缀n。
  * ![](https://pic.imgdb.cn/item/64f67970661c6c8e5486aa37.jpg)

* （2）绘制波形图

  * 系统时钟 50MHz 的频率下计 0.5s 的时间需要计数的个数为 25_000_000 个，即计数器需要从 0 计数到 24_999_999;
  * ![](https://pic.imgdb.cn/item/64f67ffc661c6c8e548803cd.jpg)
  * ![](https://pic.imgdb.cn/item/64f68041661c6c8e54885422.jpg)

* （3）代码编写

  * 打开“rtl”文件夹新建“.v”文件；

    * ``` verilog
      module  water_led
      #(
          parameter     CNT_MAX = 25'd24_999_999
      )//参数传递法可以定义在模块外部可以在实例化时候进行参数修改
      (
          input   wire        sys_clk     ,
          input   wire        sys_rst_n   ,
          
          output  reg  [3:0]  led_out
      );
      
      //定义中间变量
      //这里要注意计数器数值增加，位数也要增加，否则无法完成计数
      reg     [24:0]     cnt     ;
      reg                cnt_flag;
      
      //cnt:计数器计数，当计数到 CNT_MAX 的值时清零
      always@(posedge sys_clk or negedge sys_rst_n)//异步复位
          if(sys_rst_n == 1'b0)
              cnt <= 25'd0;
          else    if(cnt == CNT_MAX)
              cnt <= 25'd0;
          else
              cnt <= cnt + 25'd1;
      
      //cnt_flag：计数器标志位
      always@(posedge sys_clk or negedge sys_rst_n)//异步复位
          if(sys_rst_n == 1'b0)
              cnt_flag <= 1'b0;
          else    if(cnt == (CNT_MAX - 25'd1))
              cnt_flag <= 1'b1;
          else
              cnt_flag <= 1'b0;
      
      //led_out:输出控制4个 LED 灯
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              led_out <= 4'b1110;
          else    if((led_out == 4'b0111)&&(cnt_flag == 1'b1))
              led_out <= 4'b1110;
          else    if((cnt_flag == 1'b1))
              led_out <= led_out << 1;
          else
              led_out <= led_out;
      endmodule
      ```

    * 这里在后续仿真中发现左移右移末尾补0的问题：

      * ![](https://pic.imgdb.cn/item/64f6869a661c6c8e5489c6ae.jpg)

      * 所以需要添加新的变量led_out_reg对输出信号寄存：

        * ![](https://pic.imgdb.cn/item/64f68777661c6c8e548a1fb3.jpg)

        * ``` verilog
          module  water_led
          #(
              parameter     CNT_MAX = 25'd24_999_999
          )//参数传递法可以定义在模块外部可以在实例化时候进行参数修改
          (
              input   wire        sys_clk     ,
              input   wire        sys_rst_n   ,
              
              output  wire [3:0]  led_out
          );
          
          //定义中间变量
          //这里要注意计数器数值增加，位数也要增加，否则无法完成计数
          reg     [24:0]     cnt          ;
          reg                cnt_flag     ;
          reg     [3:0]      led_out_reg  ;
          
          //cnt:计数器计数，当计数到 CNT_MAX 的值时清零
          always@(posedge sys_clk or negedge sys_rst_n)//异步复位
              if(sys_rst_n == 1'b0)
                  cnt <= 25'd0;
              else    if(cnt == CNT_MAX)
                  cnt <= 25'd0;
              else
                  cnt <= cnt + 25'd1;
          
          //cnt_flag：计数器标志位
          always@(posedge sys_clk or negedge sys_rst_n)//异步复位
              if(sys_rst_n == 1'b0)
                  cnt_flag <= 1'b0;
              else    if(cnt == (CNT_MAX - 25'd1))
                  cnt_flag <= 1'b1;
              else
                  cnt_flag <= 1'b0;
          
          //led_out_reg:输出控制4个 LED 灯
          always@(posedge sys_clk or negedge sys_rst_n)
              if(sys_rst_n == 1'b0)
                  led_out_reg <= 4'b0001;
              else    if((led_out_reg == 4'b1000)&&(cnt_flag == 1'b1))
                  led_out_reg <= 4'b0001;
              else    if((cnt_flag == 1'b1))
                  led_out_reg <= led_out_reg << 1;
              else
                  led_out_reg <= led_out_reg;
          
          //led_out：用assign语句进行组合逻辑，避免延迟1拍的效果
          assign  led_out = ~led_out_reg;
          endmodule
          ```

  * 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

  * 导入“rtl”文件中编写完成的“.v”文件，进行编译.

    * 可以使用“RTL Viewer”查看代码综合出的视图。


#### 13.2.3 仿真验证

* (1)测试代码编写

  * 打开“sim”文件夹新建“.v”文件；

    * ``` verilog
      `timescale  1ns/1ns
      module  tb_water_led();
      reg             sys_clk     ;
      reg             sys_rst_n   ;
      
      wire    [3:0]   led_out     ;
      
      //初始化输入信号
      initial
          begin
              sys_clk = 1'b1;
              sys_rst_n <= 1'b0;
              #20
              sys_rst_n <= 1'b1;
          end
      
      //产生时钟信号，定义下频率，每10ns翻转一次电平，20ns周期
      always  #10 sys_clk = ~sys_clk;
      
      water_led//添加参数传递后，实例化名称count_inst写在端口列表前
      #(
          .CNT_MAX (20'd24)
          //修改的 CNT_MAX 值一定要小于(CNT_41MS - CNT_11MS)
          //否则就会表现为按键一直处于“抖动状态”而没有“稳定状态”
          //无法模拟出按键消抖的效果
      )
      water_led_inst
      (
          .sys_clk  (sys_clk),
          .sys_rst_n(sys_rst_n),
          
          .led_out (led_out)
      );
      endmodule
      ```

* （2）导入测试文件，进行Modelsim仿真设置；

* （3）开始联仿，查看波形图和打印信息：

  * ![](https://pic.imgdb.cn/item/64f68a68661c6c8e548b641e.jpg)

#### 13.2.4 管脚绑定

* sys_clk-->E1
* sys_rst_n-->M15
* led_out[3]-->L7
* led_out[2]-->M6
* led_out[1]-->P3
* led_out[0]-->N3


#### 13.2.5 上板子验证

* 点击program按钮导入“.sof”文件；
* 连接板卡，点击“start”按钮下载程序；
* 上板验证；

#### 13.2.6 程序固化

* 详情参见3.1.11



## 14 呼吸灯

### 14.1 理论学习

* 工作原理是利用 PWM 来控制小灯在相同时间段内的不同占空比，即在同样小时间段内，小灯亮的时间依次增加到最大后再依次减小，从而实现渐亮到渐灭的“呼吸”效果。

### 14.2 实战演练

#### 14.2.1 实验目标

* 使用板载 LED 灯 D6 实现呼吸灯效果。

#### 14.2.2 程序设计

* （1）模块框图绘制：

  * 信号命名特点：sys表示系统，sys_xxx_n的n表示低电平有效，如果是高电平有效则不加后缀n。
  * ![](https://pic.imgdb.cn/item/64f7d265661c6c8e54d6ea58.jpg)

* （2）绘制波形图

  * PWM占空比控制led灯电压：
    * ![](https://pic.imgdb.cn/item/64f7dc80661c6c8e54d90efd.jpg)
  * 波形图：
    * led 灯在 1s 的时间内完成从灭到亮，为了让“呼吸”效果显得更加细腻，把 1s 的时间分成 1000 个时间相等的时间小段T，那么1000个T就有1000个不同变化，所以把一个T分为1000个t周期小段。
    * 也就是说需要计数器（系统时钟周期为20ns）：
      * cnt_1s对每秒进行计数（以T为基数需要计数：0-999）；
      * cnt_1ms对每个T计数（以t为基数需要计数：0-999）；
      * cnt_1us对每个t计数（以系统时钟为基数需要计数：0-49）。
    * ![](https://pic.imgdb.cn/item/64f837eb661c6c8e54f4713e.jpg)
    * ![](https://pic.imgdb.cn/item/64f83818661c6c8e54f479e9.jpg)

* （3）代码编写

  * 打开“rtl”文件夹新建“.v”文件；

    * ``` verilog
      module  breath_led
      #(
          parameter   CNT_1US_MAX = 6'd49   ,
                      CNT_1MS_MAX = 10'd999 ,
                      CNT_1S_MAX  = 10'd999
      )//参数传递法可以定义在模块外部可以在实例化时候进行参数修改
      
      //定义输入输出变量
      (
          input       wire        sys_clk     ,
          input       wire        sys_rst_n   ,
          
          output      reg         led_out
      );
      
      //定义中间变量
      reg         [5:0]       cnt_1us ;
      reg         [9:0]       cnt_1ms ;
      reg         [9:0]       cnt_1s  ;
      reg                     cnt_en  ;
      
      //cnt_1us:计数器计数，当计数到 CNT_MAX 的值时清零
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              cnt_1us <= 6'd0;
          else    if(cnt_1us == CNT_1US_MAX)
              cnt_1us <= 6'd0;
          else
              cnt_1us <= cnt_1us + 6'd1;
      
      //cnt_1ms:计数器计数，当计数到 CNT_1US_MAX  且 CNT_1US_MAX 的值时清零
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              cnt_1ms <= 10'd0;
          else    if((cnt_1ms == CNT_1MS_MAX) && (cnt_1us == CNT_1US_MAX))
              cnt_1ms <= 10'd0;
          else    if(cnt_1us == CNT_1US_MAX)
              cnt_1ms <= cnt_1ms + 10'd1;
          else
              cnt_1ms <= cnt_1ms;
      
      //cnt_1s:计数器计数，当计数到 CNT_1S_MAX 且 CNT_1MS_MAX 且 CNT_1US_MAX的值时清零
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              cnt_1s <= 10'd0;
          else    if((cnt_1ms == CNT_1MS_MAX) &&
                      (cnt_1us == CNT_1US_MAX) && (cnt_1s == CNT_1S_MAX))
              cnt_1s <= 10'd0;
          else    if((cnt_1ms == CNT_1MS_MAX) &&
                      (cnt_1us == CNT_1US_MAX))
              cnt_1s <= cnt_1s + 10'd1;
          else
              cnt_1s <= cnt_1s;
      
      //cnt_en:状态使能信号，低电平表示从熄灭到点亮，高电平表示从点亮到熄灭
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              cnt_en <= 1'b0;
          else    if((cnt_1ms == CNT_1MS_MAX) &&
                      (cnt_1us == CNT_1US_MAX) && (cnt_1s == CNT_1S_MAX))
              cnt_en <= ~cnt_en;
          else
              cnt_en <= cnt_en;
      
      //led_out:输出信号编写
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              led_out <= 1'b1;
          else    if(((cnt_en == 1'b0) && (cnt_1ms <= cnt_1s)) ||
                      ((cnt_en == 1'b1) && (cnt_1ms > cnt_1s)))
              led_out <= 1'b0;
          else
              led_out <= 1'b1;
      endmodule
      ```

  * 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

  * 导入“rtl”文件中编写完成的“.v”文件，进行编译.

    * 可以使用“RTL Viewer”查看代码综合出的视图。
      * ![](https://pic.imgdb.cn/item/64f837b1661c6c8e54f463ff.jpg)


#### 14.2.3 仿真验证

* (1)测试代码编写

  * 打开“sim”文件夹新建“.v”文件；

    * ``` verilog
      `timescale  1ns/1ns
      module  tb_breath_led();
      reg             sys_clk     ;
      reg             sys_rst_n   ;
      
      wire            led_out     ;
      
      //初始化输入信号
      initial
          begin
              sys_clk = 1'b1;
              sys_rst_n <= 1'b0;
              #20
              sys_rst_n <= 1'b1;
          end
      
      //产生时钟信号，定义下频率，每10ns翻转一次电平，20ns周期
      always  #10 sys_clk = ~sys_clk;
      
      breath_led//添加参数传递后，实例化名称count_inst写在端口列表前
      #(
          .CNT_1US_MAX (6'd4) ,
          .CNT_1MS_MAX (10'd9),
          .CNT_1S_MAX (10'd9)
          //修改的 CNT_MAX 值一定要小于(CNT_41MS - CNT_11MS)
          //否则就会表现为按键一直处于“抖动状态”而没有“稳定状态”
          //无法模拟出按键消抖的效果
      )
      breath_led_inst
      (
          .sys_clk  (sys_clk)  ,
          .sys_rst_n(sys_rst_n),
          
          .led_out  (led_out)
      );
      endmodule
      ```

* （2）导入测试文件，进行Modelsim仿真设置；

* （3）开始联仿，查看波形图和打印信息：

  * ![](https://pic.imgdb.cn/item/64f83759661c6c8e54f45161.jpg)
  * ![](https://pic.imgdb.cn/item/64f8377d661c6c8e54f45933.jpg)
  * ![](https://pic.imgdb.cn/item/64f83793661c6c8e54f45dc8.jpg)

#### 14.2.4 管脚绑定

* sys_clk-->E1
* sys_rst_n-->M15
* led_out-->L7


#### 14.2.5 上板子验证

* 点击program按钮导入“.sof”文件；
* 连接板卡，点击“start”按钮下载程序；
* 上板验证；

#### 14.2.6 程序固化

* 详情参见3.1.11

## 15 状态机

### 15.1 理论学习

* 想要处理具有前后顺序的事件就需要引入状态机。
* 状态机简写为 FSM（Finite State Machine），也称为同步有限状态机：
  * “同步”是因为状态机中所有的状态跳转都是在时钟的作用下进行。
  * “有限”则是说状态的个数是有限的。

* 状态机根据影响输出的原因分为两大类（其共同点是：状态的跳转都只和输入有关）：
  * Moore 型状态机：最后的输出只和当前状态有关而与输入无关。
  * Mealy 型状态机：最后的输出不仅和当前状态有关还和输入有关。


### 15.2 实战演练一

#### 15.2.1 实验目标

* 可乐机每次只能投入 1 枚 1 元硬币，且每瓶可乐卖 3 元钱，即投入 3 个硬币就可以让可乐机出可乐，如果投币不够 3 元想放弃投币需要按复位键，否则之前投入的钱不能退回。

#### 15.2.2 程序设计

* （1）模块框图绘制：

  * 信号命名特点：sys表示系统，sys_xxx_n的n表示低电平有效，如果是高电平有效则不加后缀n。
  * ![](https://pic.imgdb.cn/item/64f930b6661c6c8e54304c08.jpg)

* （2）状态转移图绘制：

  * 状态转移图需要知道以下三个要素：
    * 输入：投入 1 元硬币；
    * 输出：出可乐、不出可乐；
    * 状态：可乐机中有 0 元、可乐机中有 1 元、可乐机中有 2 元、可乐机中有 3 元。
  * ![](https://pic.imgdb.cn/item/64f9397f661c6c8e54331083.jpg)

* （2）绘制波形图

  * ![](https://pic.imgdb.cn/item/64f942a7661c6c8e5436af01.jpg)

* （3）代码编写

  * 打开“rtl”文件夹新建“.v”文件；

    * 这里用到了独热码表示状态：

      * ![](https://pic.imgdb.cn/item/64f9470c661c6c8e5437bfaf.jpg)

    * ``` verilog
      module  simple_fsm
      //定义输入输出变量
      (
          input   wire    sys_clk     ,
          input   wire    sys_rst_n   ,
          input   wire    pi_money    ,
          
          output  reg     po_cola
      );
      
      //定义三个状态：这里采用独热码的方式对状态机进行状态编码
      parameter   IDLE    =   3'b001;
      parameter   ONE     =   3'b010;
      parameter   TWO     =   3'b100;
      
      reg     [2:0]       state;
      
      /* //定义三个状态：这里采用二进制码的方式对状态机进行状态编码
      parameter   IDLE    =   2'b00;
      parameter   ONE     =   2'b01;
      parameter   TWO     =   2'b10;
      
      reg     [1:0]       state; */
      
      /* //定义三个状态：这里采用格雷码的方式对状态机进行状态编码
      parameter   IDLE    =   2'b00;
      parameter   ONE     =   2'b01;
      parameter   TWO     =   2'b11;
      
      reg     [1:0]       state; */
      
      //state：表示三种状态
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              state <= IDLE;
          else    case(state)
              IDLE:   if(pi_money == 1'b1)
                          state <= ONE    ;
                      else
                          state <= IDLE   ;
              ONE:    if(pi_money == 1'b1)
                          state <= TWO    ;
                      else
                          state <= ONE    ;
              TWO:    if(pi_money == 1'b1)
                          state <= IDLE   ;
                      else
                          state <= TWO    ;
              default:state <= IDLE;
          endcase
      
      //输出信号
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              po_cola <= 1'b0;
          else    if((state == TWO) && (pi_money == 1'b1))
              po_cola <= 1'b1;
          else
              po_cola <= 1'b0;
      
      endmodule
      ```

  * 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

  * 导入“rtl”文件中编写完成的“.v”文件，进行编译.

    * 可以使用“RTL Viewer”查看代码综合出的视图。
      * ![](https://pic.imgdb.cn/item/64f94aba661c6c8e5438f23b.jpg)
    * 可以使用“State Machine Viewer”查看代码综合出的状态转移视图。
      * ![](https://pic.imgdb.cn/item/64f94a81661c6c8e5438bd19.jpg)


#### 15.2.3 仿真验证

* (1)测试代码编写

  * 打开“sim”文件夹新建“.v”文件；

    * ``` verilog
      `timescale  1ns/1ns
      module  tb_simple_fsm();
      
      //定义输入输出变量
      reg     sys_clk     ;
      reg     sys_rst_n   ;
      reg     pi_money    ;
      
      wire    po_cola     ;
      
      //初始化
      initial
          begin
              sys_clk     =   1'b1;
              sys_rst_n   <=  1'b0;
              #20
              sys_rst_n   <=  1'b1;
          end
      
      //产生时钟信号，定义下频率，每10ns翻转一次电平，20ns周期
      always  #10 sys_clk = ~sys_clk;
      
      //产生投币信号，采用随机数的方法
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              pi_money <= 1'b0;
          else
              pi_money <= {$random} % 2;
      
      //引出实例化文件当中的state
      wire    [2:0]    state = simple_fsm_inst.state;
      
      //用系统函数打印数据
      initial
          begin
              $timeformat(-9,0,"ns",6);
              $monitor("@time %t:[pi_money] = %b,[state] = %b,[po_cola] = %b",$time,pi_money,state,po_cola);
          end
      
      //实例化
      simple_fsm simple_fsm_inst
      (
          .sys_clk  (sys_clk),
          .sys_rst_n(sys_rst_n),
          .pi_money (pi_money),
          
          .po_cola  (po_cola)
      );
      
      endmodule
      ```

* （2）导入测试文件，进行Modelsim仿真设置；

* （3）开始联仿，查看波形图和打印信息：

  * ![](https://pic.imgdb.cn/item/64f982fe661c6c8e54502341.jpg)



### 15.3 实战演练二

#### 15.3.1 实验目标

* 可乐定价为 2.5 元一瓶，可投入 0.5 元、1 元硬币，投币不够 2.5 元需要按复位键退回钱款，投币超过 2.5 元需找零。

#### 15.3.2 程序设计

* （1）模块框图绘制：

  * 信号命名特点：sys表示系统，sys_xxx_n的n表示低电平有效，如果是高电平有效则不加后缀n。
  * <img src="https://pic.imgdb.cn/item/64fa85ab661c6c8e548cb76d.jpg" style="zoom:50%;" />

* （2）状态转移图绘制：

  * 状态转移图需要知道以下三个要素：
    * 输入：投入0.5[01]、1[10]元硬币
      输出：不出可乐/不找零[00]、出可乐/不找零[01]、出可乐/找零[10]
      状态：可乐机中有0、0.5、1、1.5、2、2.5、3元
      输入：[00]、[01]、[10]
      输出：[00]、[10]、[11]
  * <img src="https://pic.imgdb.cn/item/64fa8577661c6c8e548ca161.jpg" style="zoom: 50%;" />

* （2）绘制波形图

  * ![](https://pic.imgdb.cn/item/64fa9377661c6c8e5490c74f.jpg)
  * ![](https://pic.imgdb.cn/item/64fa935d661c6c8e5490c286.jpg)

* （3）代码编写

  * 打开“rtl”文件夹新建“.v”文件；

    * 这里用到了独热码表示状态：

      * ![](https://pic.imgdb.cn/item/64f9470c661c6c8e5437bfaf.jpg)

    * ``` verilog
      module  complex_fsm
      //********************************************************************//
      //***************** Define Input and Output Variables ****************//
      //********************************************************************//
      (
          input   wire        sys_clk         ,
          input   wire        sys_rst_n       ,
          input   wire        pi_money_half   ,
          input   wire        pi_money_one    ,
          
          output  reg         po_cola         ,
          output  reg         po_money        
      );
      
      //********************************************************************//
      //******************** Paramete and Internal Signal ******************//
      //********************************************************************//
      
      //Parameter define
      //只有五种状态，使用独热码
      parameter   IDLE    = 5'b00001;
      parameter   HALF    = 5'b00010;
      parameter   ONE     = 5'b00100;
      parameter   ONE_HALF= 5'b01000;
      parameter   TWO     = 5'b10000;
      
      //Internal Signal define
      wire     [1:0]      pi_money;
      reg      [4:0]         state;
      
      //********************************************************************//
      //****************************** Main Code ***************************//
      //********************************************************************//
      
      //pi_money:为了减少变量的个数，我们用位拼接把输入的两个 1bit 信号拼接成 1 个 2bit 信号
      //投币方式可以为：不投币（00）、投 0.5 元（01）、投 1 元（10），每次只投一个币
      //pi_money:没有延迟一个时序周期，所以用assign语句
      assign  pi_money = {pi_money_one, pi_money_half};
      
      //第一段状态机，描述当前状态 state 如何根据输入跳转到下一状态
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              state <= IDLE;
          else    case(state)
              IDLE    :   if(pi_money == 2'b01)
                              state <= HALF;
                          else    if(pi_money == 2'b10)
                              state <= ONE;
                          else
                              state <= IDLE;
      
              HALF    :   if(pi_money == 2'b01)
                              state <= ONE;
                          else    if(pi_money == 2'b10)
                              state <= ONE_HALF;
                          else
                              state <= HALF;
      
              ONE     :   if(pi_money == 2'b01)
                              state <= ONE_HALF;
                          else    if(pi_money == 2'b10)
                              state <= TWO;
                          else
                              state <= ONE;
      
              ONE_HALF:   if(pi_money == 2'b01)
                              state <= TWO;
                          else    if(pi_money == 2'b10)
                              state <= IDLE;
                          else
                              state <= ONE_HALF;
      
              TWO     :   if((pi_money == 2'b01) || (pi_money == 2'b10))
                              state <= IDLE;
                          else
                              state <= TWO;
              default:state <= IDLE;
          endcase
      
      //第二段状态机，描述当前状态 state 和输入 pi_money 如何影响 po_cola 输出
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              po_cola <= 1'b0;
          else if(((state == TWO) && (pi_money == 2'b01))
                  || ((state == TWO) && (pi_money == 2'b10))
                  || ((state == ONE_HALF) && (pi_money == 2'b10)))
              po_cola <= 1'b1;
          else
              po_cola <= 1'b0;
      
      //第二段状态机，描述当前状态 state 和输入 pi_money 如何影响 po_money 输出
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              po_money <= 1'b0;
          else if((state == TWO) && (pi_money == 2'b10))
              po_money <= 1'b1;
          else
              po_money <= 1'b0;
      
      endmodule
      ```

  * 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

  * 导入“rtl”文件中编写完成的“.v”文件，进行编译.

    * 可以使用“RTL Viewer”查看代码综合出的视图。
      * ![](https://pic.imgdb.cn/item/64fa94a1661c6c8e5490f968.jpg)
    * 可以使用“State Machine Viewer”查看代码综合出的状态转移视图。
      * ![](https://pic.imgdb.cn/item/64fa955d661c6c8e549131bd.jpg)


#### 15.3.3 仿真验证

* (1)测试代码编写

  * 打开“sim”文件夹新建“.v”文件；

    * ``` verilog
      `timescale  1ns/1ns
      module  tb_complex_fsm();
      //********************************************************************//
      //******************** Paramete and Internal Signal ******************//
      //********************************************************************//
      
      //reg define
      reg     sys_clk         ;
      reg     sys_rst_n       ;
      reg     pi_money_half   ;
      reg     pi_money_one    ;
      reg     random_data_gen ;
      
      //wire define
      wire    po_cola         ;
      wire    po_money        ;
      
      //********************************************************************//
      //****************************** Main Code ***************************//
      //********************************************************************//
      
      //初始化系统时钟、全局复位
      initial
          begin
              sys_clk      = 1'b1;
              sys_rst_n   <= 1'b0;
              #20
              sys_rst_n   <= 1'b1;
          end
      
      //sys_clk:模拟系统时钟，每 10ns 电平翻转一次，周期为 20ns，频率为 50MHz
      always  #10 sys_clk = ~sys_clk;
      
      //random_data_gen:产生非负随机数 0、1
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              random_data_gen <= 1'b0;
          else
              random_data_gen <= {$random} % 2;
      
      //pi_money_half:模拟投入 0.5 元的情况
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              pi_money_half <= 1'b0;
          else
              pi_money_half <= random_data_gen;
      
      //pi_money_one:模拟投入 1 元的情况
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              pi_money_one <= 1'b0;
          else
              pi_money_one <= ~random_data_gen;
      
      //将 RTL 模块中的内部信号引入到 Testbench 模块中进行观察
      wire    [1:0]       pi_money = complex_fsm_inst.pi_money;
      wire    [4:0]       state    = complex_fsm_inst.state   ;
      
      //用系统函数打印数据
      initial
          begin
              $timeformat(-9,0,"ns",6);
              $monitor("@time %t:[pi_money_half = %b],[pi_money_one = %b],[pi_money = %b],[state = %b],[po_cola = %b],[po_money = %b]",$time,pi_money_half,pi_money_one,pi_money,state,po_cola,po_money);
          end
          
      //********************************************************************//
      //**************************** Instantiation *************************//
      //********************************************************************//
      complex_fsm complex_fsm_inst
      (
          .sys_clk      (sys_clk      ),
          .sys_rst_n    (sys_rst_n    ),
          .pi_money_half(pi_money_half),
          .pi_money_one (pi_money_one ),
      
          .po_cola      (po_cola      ),
          .po_money     (po_money     )
      );
      endmodule
      ```

* （2）导入测试文件，进行Modelsim仿真设置；

* （3）开始联仿，查看波形图和打印信息：

  * ![](https://pic.imgdb.cn/item/64faed3a661c6c8e54afd8e0.jpg)

  

## 16 蜂鸣器

### 16.1 理论学习

* 蜂鸣器按其是否带有信号源分为:

  * 有源蜂鸣器：内部装有集成电路，不需要音频驱动电路，只需要接通直流电源就能直接发出声响。
  * 无源蜂鸣器：只有外加音频驱动信号才能发出声响。
  * ![](https://pic.imgdb.cn/item/64fc26c2661c6c8e54efbd78.jpg)

* 无源蜂鸣器驱动原理：

  * 因其内部不带震荡源，所以其无法向有缘蜂鸣器那样直接用直流信号驱动，这里需要使用 PWM 方波才能驱动其发声。

    其中频率对音调有影响，占空比对音量大小有影响。


### 16.2 实战演练

#### 16.2.1 实验目标

* 们驱动无源蜂鸣器进行七个基本音调“哆来咪发梭拉西”的循环鸣叫，每个音阶持续鸣叫 0.5s 后鸣叫下一个音阶。
* ![](https://pic.imgdb.cn/item/64fc27d5661c6c8e54eff1f7.jpg)

#### 16.2.2 程序设计

* （1）模块框图绘制：

  * 信号命名特点：sys表示系统，sys_xxx_n的n表示低电平有效，如果是高电平有效则不加后缀n。
  * ![](https://pic.imgdb.cn/item/64fc2ac0661c6c8e54f13182.jpg)

* （2）绘制波形图

  * ![](https://pic.imgdb.cn/item/64fc38d9661c6c8e54f4d73f.jpg)
  * ![](https://pic.imgdb.cn/item/64fc3924661c6c8e54f4efd3.jpg)
  * ![](https://pic.imgdb.cn/item/64fc3901661c6c8e54f4ec97.jpg)

* （3）代码编写

  * 打开“rtl”文件夹新建“.v”文件；

    * ``` verilog
      module  beep
      
      //Parameter define
      #(
          parameter       CNT_MAX     = 25'd24_999_999,
          parameter       DO          = 18'd190839    ,   //"哆"音调分频计数值（频率 262）
          parameter       RE          = 18'd170067    ,   //"来"音调分频计数值（频率 294）
          parameter       MI          = 18'd151514    ,   //"咪"音调分频计数值（频率 330）
          parameter       FA          = 18'd143265    ,   //"发"音调分频计数值（频率 349）
          parameter       SO          = 18'd127550    ,   //"梭"音调分频计数值（频率 392）
          parameter       LA          = 18'd113635    ,   //"拉"音调分频计数值（频率 440）
          parameter       XI          = 18'd101214        //"西"音调分频计数值（频率 494）
      )
      
      //********************************************************************//
      //***************** Define Input and Output Variables ****************//
      //********************************************************************//
      (
          input       wire    sys_clk     ,
          input       wire    sys_rst_n   ,
          
          output      reg     beep        
      );
      
      
      //********************************************************************//
      //******************** Paramete and Internal Signal ******************//
      //********************************************************************//
      
      //Internal Signal define
      reg     [24:0]      cnt         ;
      reg     [2:0]       cnt_500ms   ;
      reg     [17:0]      freq_cnt    ;
      reg     [17:0]      freq_data   ;
      wire    [16:0]      duty_data   ;
      
      //********************************************************************//
      //****************************** Main Code ***************************//
      //********************************************************************//
      
      //cnt:0.5s 循环计数器
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              cnt <= 25'd0;
          else if(cnt == CNT_MAX)
              cnt <= 25'd0;
          else
              cnt <= cnt + 1'b1;
      
      //cnt_500ms：对 500ms 个数进行计数，每个音阶鸣叫时间 0.5s，7 个音节一循环
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              cnt_500ms <= 3'd0;
          else if((cnt_500ms == 3'd6) && (cnt == CNT_MAX))
              cnt_500ms <= 3'd0;
          else if(cnt == CNT_MAX)
              cnt_500ms <= cnt_500ms + 1'b1;
          else
              cnt_500ms <= cnt_500ms;
      
      //不同时间鸣叫不同的音阶
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              freq_data <= DO;
          else case(cnt_500ms)
              3'd0:  freq_data <= DO;
              3'd1:  freq_data <= RE;
              3'd2:  freq_data <= MI;
              3'd3:  freq_data <= FA;
              3'd4:  freq_data <= SO;
              3'd5:  freq_data <= LA;
              3'd6:  freq_data <= XI;
              default:freq_data <= DO;
          endcase
      
      //freq_cnt：当计数到音阶计数值或跳转到下一音阶时，开始重新计数
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              freq_cnt <= 18'd0;
          else if((freq_cnt == freq_data) || (cnt == CNT_MAX))
              freq_cnt <= 18'd0;
          else
              freq_cnt <= freq_cnt + 1'b1;
      
      //设置 50％占空比：音阶分频计数值的一半即为占空比的高电平数
      assign duty_data = freq_data >> 1'b1;
      
      //beep：输出蜂鸣器波形
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              beep <= 1'b0;
          else if(freq_cnt >= duty_data)
              beep <= 1'b1;
          else
              beep <= 1'b0;
      
      endmodule
      ```

  * 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

  * 导入“rtl”文件中编写完成的“.v”文件，进行编译.

    * 可以使用“RTL Viewer”查看代码综合出的视图。
      * ![](https://pic.imgdb.cn/item/64fc4541661c6c8e54f88026.jpg)


#### 16.2.3 仿真验证

* (1)测试代码编写

  * 打开“sim”文件夹新建“.v”文件；

    * ``` verilog
      `timescale  1ns/1ns
      module  tb_beep();
      //********************************************************************//
      //******************** Paramete and Internal Signal ******************//
      //********************************************************************//
      
      //reg define
      reg     sys_clk         ;
      reg     sys_rst_n       ;
      
      //wire define
      wire    beep            ;
      
      //********************************************************************//
      //****************************** Main Code ***************************//
      //********************************************************************//
      
      //初始化系统时钟、全局复位
      initial
          begin
              sys_clk      = 1'b1;
              sys_rst_n   <= 1'b0;
              #20
              sys_rst_n   <= 1'b1;
          end
      
      //sys_clk:模拟系统时钟，每 10ns 电平翻转一次，周期为 20ns，频率为 50MHz
      always  #10 sys_clk = ~sys_clk;
      
      //********************************************************************//
      //**************************** Instantiation *************************//
      //********************************************************************//
      beep
      #(
          .CNT_MAX     (25'd24_999_99),
          .DO          (18'd19083    ),
          .RE          (18'd17006    ),
          .MI          (18'd15151    ),
          .FA          (18'd14326    ),
          .SO          (18'd12755    ),
          .LA          (18'd11363    ),
          .XI          (18'd10121    )
      )
      beep_inst
      (
          .sys_clk    (sys_clk      ),
          .sys_rst_n  (sys_rst_n    ),
      
          .beep       (beep         )
      );
      
      endmodule
      ```

* （2）导入测试文件，进行Modelsim仿真设置；

* （3）开始联仿，查看波形图和打印信息：

  * ![](https://pic.imgdb.cn/item/64fc5b6a661c6c8e54fc62c4.jpg)

#### 16.2.4 管脚绑定

* sys_clk-->E1
* sys_rst_n-->M15
* beep-->J11


#### 16.2.5 上板子验证

* 点击program按钮导入“.sof”文件；
* 连接板卡，点击“start”按钮下载程序；
* 上板验证；

#### 16.2.6 程序固化

* 详情参见3.1.11



## 17 数码管静态显示

### 17.1 理论学习

* 八段数码管:

  * 八段数码管是一个八字型数码管，分为八段：a、b、c、d、e、f、g、dp，其中 dp 为小数点，每一段即为一个发光二极管，这样的八段我们称之为段选信号。数码管常用的有 10 根管脚，每一段有一根管脚，另外两根管脚为一个数码管的公共端，两根互相连接。
    * ![](https://pic.imgdb.cn/item/64fd1d0b661c6c8e541f395f.jpg)
  * 二进制段码`右边为高位`，`左边为低位`。我们只要点亮相应的段码，就能显示我们需要显示的内容。
    * ![](https://pic.imgdb.cn/item/64fd1f5d661c6c8e541f89a4.jpg)![](https://pic.imgdb.cn/item/64fd1f6f661c6c8e541f8af7.jpg)

* 六位八段数码管：

  * 段式数码管工作方式有两种：静态显示和动态显示。
    * 静态显示的特点是每个数码管的段选必须接一个 8 位数据线来显示字形，显示字形可一直保持，直到送入新字形码为止。
      * ![](https://pic.imgdb.cn/item/64fd2211661c6c8e541fdd5b.jpg)
      * 将六个数码管的段选信号连接在一起，而位选（sel）独立控制，这样六个数码管接在一起就少了 8×5 个 I/O 口。
      * 这里对位选信号特别说明一下：由上 图可以看到每一个数码管都有一个位选信号，而这个位选信号就控制着数码管的亮灭。这样我们就可以通过位选信号去控制数码管亮，而在同一时刻，位选选通的数码管上显示的字形是一样的，因为我们将 6 个数码管相对应的段选连在了一起，数码管的显示自然就相同了，数码管的这种显示方式即为静态显示。而如果要让每个数码管显示的值不同，我们要用到另外一种显示方式，即动态显示，将在下一章节给大家介绍。
* 74HC595 简介：
  * 74HC595 是一个 8 位串行输入、并行输出的位移缓存器。其内部具有 8 位移位寄存器和一个存储器，具有三态输出功能。
    * ![](https://pic.imgdb.cn/item/64fd2294661c6c8e541feb8b.jpg)
    * 使用两片 74HC595 芯片进行输出，事实上这正是 74HC595 的一大特点，可以进行级联。 74HC595 芯片有个 Q7S 引脚，该引脚的功能为串行数据输出，我们将这个输出接入下一片 74HC595 芯片的串行数据输入端，这样后面的数据就会在下一片 74HC595 芯片输出了。应用好这个联级功能，我们最少只需占用三个 I/O 口就可以控制很多片74HC595。
    * ![](https://pic.imgdb.cn/item/64fd248e661c6c8e5420679d.jpg)
  * 最后我们总结一下 74HC595 的使用步骤：
    *  1、 首先把要传输的数据通过引脚 DS 输入到 74HC595 中。
    *  2、 产生 SHCP 时钟，将 DS 上的数据串行移入移位寄存器。 
    *  3、 产生 STCP 时钟，将移位寄存器里的数据送入存储寄存器。
    *  4、 将 OE 引脚置为低电平，存储寄存器的数据会在 Q0—Q7 并行输出，同时并行输出的数据会被锁存起来。
    *  ![](https://pic.imgdb.cn/item/64fd24e2661c6c8e54207291.jpg)


### 17.2 实战演练

#### 17.2.1 实验目标

* 6 位数码管静态显示：控制六位数码管让其以 000000、111111、222222 一直到 FFFFFF 循环显示。每个字符显示 0.5s 后变化。
* ![](https://pic.imgdb.cn/item/64fd267f661c6c8e54215eec.jpg)
* 开发板上搭载了两片 74HC595 芯片用于输出数码管驱动信号，其中 MR 我们接到了 VCC 防止数据清零，所以这两片 74HC595 芯片我们只用四个 IO 口控制即可。 我们将两片 74HC595 进行级联，一片的 Q7S 输出端接到另一片的数据输入端 DS，这样我们输入的 14 位串行输入数码管信号的前六位就会在第二片就行输出。（注意：因为是移位寄存器，如果一次共输入 14 位数据，那么第一位输入的串行数据会在第二片 74HC595 芯片的 Q5 输出）。

#### 17.2.2 程序设计

* （1）模块框图绘制：

  * 实验一共分为 3 个模块：
    * 顶层模块是：![](https://pic.imgdb.cn/item/64fd4050661c6c8e54259a6a.jpg)
      * 其中包括两个子模块![](https://pic.imgdb.cn/item/64fd4a58661c6c8e542758fa.jpg)
  * 将三个模块整合在一起就是：![](https://pic.imgdb.cn/item/64fd40c2661c6c8e5425a9ca.jpg)

* （2）绘制波形图

  * ①[子模块]静态数码管驱动模块：
    * ![](https://pic.imgdb.cn/item/64fe8947661c6c8e54653534.jpg)
  * ②[子模块]74HC595控制模块：
    * ![](https://pic.imgdb.cn/item/64fe924c661c6c8e5467acdb.jpg)

* （3）子模块①静态数码管驱动模块代码编写及仿真测试

  * 代码编写：

    * ①[子模块]静态数码管驱动模块：

      * ``` verilog
        module  seg_static
        
        //Parameter define
        #(
            parameter       CNT_MAX     = 25'd24_999_999
        )
        
        //********************************************************************//
        //***************** Define Input and Output Variables ****************//
        //********************************************************************//
        (
            input       wire            sys_clk     ,
            input       wire            sys_rst_n   ,
            
            output      reg     [5:0]   sel         ,
            output      reg     [7:0]   seg         
        );
        
        //********************************************************************//
        //******************** Paramete and Internal Signal ******************//
        //********************************************************************//
        
        //Parameter define
        parameter       SEG_0       = 8'hc0,
                        SEG_1       = 8'hf9,
                        SEG_2       = 8'ha4,
                        SEG_3       = 8'hb0,
                        SEG_4       = 8'h99,
                        SEG_5       = 8'h92,
                        SEG_6       = 8'h82,
                        SEG_7       = 8'hf8,
                        SEG_8       = 8'h80,
                        SEG_9       = 8'h90,
                        SEG_A       = 8'h88,
                        SEG_B       = 8'h83,
                        SEG_C       = 8'hc6,
                        SEG_D       = 8'ha1,
                        SEG_E       = 8'h86,
                        SEG_F       = 8'h8e;
        parameter       IDLE        = 8'hff;
        
        //Internal Signal define
        reg     [24:0]      cnt     ;
        reg     [3:0]       data    ;
        reg                 cnt_flag;
        
        //********************************************************************//
        //****************************** Main Code ***************************//
        //********************************************************************//
        
        //cnt：0.5s 循环计数器
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                cnt <= 25'd0;
            else if(cnt == CNT_MAX)
                cnt <= 25'd0;
            else
                cnt <= cnt + 1'b1;
        
        //cnt_flag：0.5s 拉高一个标志信号
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                cnt_flag <= 1'b0;
            else    if(cnt == CNT_MAX -1)
                cnt_flag <= 1'b1;
            else
                cnt_flag <= 1'b0;
        
        //data:从 4'h0 加到 4'hf 循环
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                data <= 4'd0;
            else    if((cnt_flag == 1'b1) && (data == 4'd15))
                data <= 4'd0;
            else    if(cnt == CNT_MAX)
                data <= data + 1'b1;
            else
                data <= data;
        
        //sel：选中六个数码管
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                sel <= 6'b0;
            else
                sel <= 6'b111_111;
        
        //给要显示的值编码
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                seg <= IDLE;
            else    case(data)
                4'd0:   seg <= SEG_0;
                4'd1:   seg <= SEG_1;
                4'd2:   seg <= SEG_2;
                4'd3:   seg <= SEG_3;
                4'd4:   seg <= SEG_4;
                4'd5:   seg <= SEG_5;
                4'd6:   seg <= SEG_6;
                4'd7:   seg <= SEG_7;
                4'd8:   seg <= SEG_8;
                4'd9:   seg <= SEG_9;
                4'd10:  seg <= SEG_A;
                4'd11:  seg <= SEG_B;
                4'd12:  seg <= SEG_C;
                4'd13:  seg <= SEG_D;
                4'd14:  seg <= SEG_E;
                4'd15:  seg <= SEG_F;
                default:seg <= IDLE ;
            endcase
        endmodule
        ```

      * 【编译报错1】

        * ![](https://pic.imgdb.cn/item/64fd5308661c6c8e54294869.jpg)![](https://pic.imgdb.cn/item/64fd58d5661c6c8e542a46a8.jpg)

          * 方法①：重新编写一个顶层模块，将子功能模块实例化在顶层模块中，对顶层模块进行编译（采用）。

            * ``` verilog
              module  seg_595_static
              
              //Parameter define
              
              //********************************************************************//
              //***************** Define Input and Output Variables ****************//
              //********************************************************************//
              (
                  input       wire            sys_clk     ,
                  input       wire            sys_rst_n   ,
                  
                  output      wire            ds          ,
                  output      wire            shcp        ,
                  output      wire            stcp        ,
                  output      wire            oe         
              );
              
              //********************************************************************//
              //******************** Paramete and Internal Signal ******************//
              //********************************************************************//
              
              //将子模块中的内部信号引入到顶层模块中
              wire    [5:0]       sel;
              wire    [7:0]       seg;
              
              //********************************************************************//
              //**************************** Instantiation *************************//
              //********************************************************************//
              seg_static
              #(
                  .CNT_MAX    (25'd24)
              )
              seg_static_inst
              (
                  .sys_clk  (sys_clk)  ,
                  .sys_rst_n(sys_rst_n),
              
                  .sel      (sel)      ,
                  .seg      (seg)
              );
              
              endmodule
              ```

          * 方法②：选中.v文件，选择强制转化为顶层文件。

            * ![](https://pic.imgdb.cn/item/64fd53b6661c6c8e542964f6.jpg)

      * 【编译报错2】

        * ![](https://pic.imgdb.cn/item/64fd58d5661c6c8e542a46a8.jpg)
          * 方法：检查if()语句中是否把sys_rst_n复位信号写成了sys_clk时钟信号。
          * ![](https://pic.imgdb.cn/item/64fd5950661c6c8e542a5ba8.jpg)

  * 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

  * 导入“rtl”文件中编写完成的“.v”文件，进行编译.

    * 可以使用“RTL Viewer”查看代码综合出的视图。

  * 仿真测试：

    * 打开“sim”文件夹新建“.v”文件：

      * ``` verilog
        `timescale  1ns/1ns
        module  tb_seg_static();
        //********************************************************************//
        //******************** Paramete and Internal Signal ******************//
        //********************************************************************//
        
        //reg define
        reg     sys_clk         ;
        reg     sys_rst_n       ;
        
        //wire define
        wire    [5:0]   sel     ;
        wire    [7:0]   seg     ;
        
        //********************************************************************//
        //****************************** Main Code ***************************//
        //********************************************************************//
        
        //初始化系统时钟、全局复位
        initial
            begin
                sys_clk      = 1'b1;
                sys_rst_n   <= 1'b0;
                #20
                sys_rst_n   <= 1'b1;
            end
        
        //sys_clk:模拟系统时钟，每 10ns 电平翻转一次，周期为 20ns，频率为 50MHz
        always  #10 sys_clk = ~sys_clk;
        
        //********************************************************************//
        //**************************** Instantiation *************************//
        //********************************************************************//
        seg_static
        #(
            .CNT_MAX    (25'd24)
        )
        seg_static_inst
        (
            .sys_clk  (sys_clk)  ,
            .sys_rst_n(sys_rst_n),
        
            .sel      (sel)      ,
            .seg      (seg)
        );
        
        endmodule
        ```

    * 导入测试文件，进行Modelsim仿真设置；

    * 开始联仿，查看波形图和打印信息：

      * ![](https://pic.imgdb.cn/item/64fd5c1f661c6c8e542af073.jpg)

  （4）子模块②74HC595控制模块代码编写及仿真测试

  * 代码编写：

    * ②[子模块]74HC595控制模块：

      * ``` verilog
        module  hc595_ctrl
        
        //********************************************************************//
        //***************** Define Input and Output Variables ****************//
        //********************************************************************//
        (
            input       wire            sys_clk     ,
            input       wire            sys_rst_n   ,
            input       wire    [5:0]   sel         ,
            input       wire    [7:0]   seg         ,
            
            output      reg             ds          ,
            output      reg             shcp        ,
            output      reg             stcp        ,
            output      wire            oe          
        );
        
        //********************************************************************//
        //******************** Paramete and Internal Signal ******************//
        //********************************************************************//
        
        //Internal Signal define
        wire    [13:0]      data     ;
        reg     [1:0]       cnt      ;
        reg     [3:0]       cnt_bit  ;
        
        //********************************************************************//
        //****************************** Main Code ***************************//
        //********************************************************************//
        
        //data：将数码管信号寄存
        assign data = {seg[0],seg[1],seg[2],seg[3],seg[4],seg[5],seg[6],seg[7],sel};
        
        //cnt：分频计数器:0~3 循环计数
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                cnt <= 2'd0;
            else if(cnt == 2'd3)
                cnt <= 2'd0;
            else
                cnt <= cnt + 1'b1;
        
        //cnt_bit：每输入一位数据加一
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                cnt_bit <= 4'd0;
            else    if((cnt == 2'd3) && (cnt_bit == 4'd13))
                cnt_bit <= 4'd0;
            else    if(cnt == 2'd3)
                cnt_bit <= cnt_bit + 1'b1;
            else
                cnt_bit <= cnt_bit;
        
        //ds：将寄存器里存储的数码管信号输入即
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                ds <= 14'd0;
            else    if(cnt == 2'd0)
                ds <= data[cnt_bit];
            else
                ds <= ds;
        
        //shcp：产生四分频移位时钟
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                shcp <= 1'b0;
            else    if(cnt >= 2'd2)
                shcp <= 1'b1;
            else
                shcp <= 1'b0;
        
        //stcp：14个信号传输完成之后产生一个上升沿
        always@(posedge sys_clk or negedge sys_rst_n)
            if(sys_rst_n == 1'b0)
                stcp <= 1'b0;
            else    if((cnt_bit == 4'd0) && (cnt <= 2'd1))
                stcp <= 1'b1;
            else
                stcp <= 1'b0;
        
        //oe：//将复位取反后赋值给其即可
        assign  oe = ~sys_rst_n;
        
        endmodule
        ```

      * 【编译报错1】这里是对加入新的子模块后顶层文件的补充

        * ![](https://pic.imgdb.cn/item/64fd5308661c6c8e54294869.jpg)![](https://pic.imgdb.cn/item/64fd58d5661c6c8e542a46a8.jpg)

          * 方法①：重新编写一个顶层模块，将子功能模块实例化在顶层模块中，对顶层模块进行编译（采用）。

            * ``` verilog
              module  seg_595_static
              
              //Parameter define
              
              //********************************************************************//
              //***************** Define Input and Output Variables ****************//
              //********************************************************************//
              (
                  input       wire            sys_clk     ,
                  input       wire            sys_rst_n   ,
                  
                  output      wire            ds          ,
                  output      wire            shcp        ,
                  output      wire            stcp        ,
                  output      wire            oe         
              );
              
              //********************************************************************//
              //******************** Paramete and Internal Signal ******************//
              //********************************************************************//
              
              //将子模块中的内部信号引到顶层模块中
              wire    [5:0]       sel;
              wire    [7:0]       seg;
              
              //********************************************************************//
              //**************************** Instantiation *************************//
              //********************************************************************//
              
              //静态数码管驱动模块
              seg_static
              #(
                  .CNT_MAX    (25'd24)
              )
              seg_static_inst
              (
                  .sys_clk  (sys_clk)  ,
                  .sys_rst_n(sys_rst_n),
              
                  .sel      (sel)      ,
                  .seg      (seg)
              );
              
              //74HC595控制模块
              hc595_ctrl hc595_ctrl_inst
              (
                  .sys_clk  (sys_clk),
                  .sys_rst_n(sys_rst_n),
                  .sel      (sel),
                  .seg      (seg),
                  
                  .ds       (ds),
                  .shcp     (shcp),
                  .stcp     (stcp),
                  .oe       (oe)
              );
              
              endmodule
              ```

          * 方法②：选中.v文件，选择强制转化为顶层文件。

            * ![](https://pic.imgdb.cn/item/64fd53b6661c6c8e542964f6.jpg)

  * 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

  * 导入“rtl”文件中编写完成的“.v”文件，进行编译.

    * 可以使用“RTL Viewer”查看代码综合出的视图。
      * ![](https://pic.imgdb.cn/item/64fe93b4661c6c8e54684b83.jpg)

  * 仿真测试：

    * 打开“sim”文件夹新建“.v”文件：

      * ``` verilog
        `timescale  1ns/1ns
        module  tb_seg_595_static();
        //********************************************************************//
        //******************** Paramete and Internal Signal ******************//
        //********************************************************************//
        
        //reg define
        reg     sys_clk         ;
        reg     sys_rst_n       ;
        
        //wire define
        wire    [5:0]   sel     ;
        wire    [7:0]   seg     ;
        wire            ds      ;
        wire            shcp    ;
        wire            stcp    ;
        wire            oe      ;
        
        //********************************************************************//
        //****************************** Main Code ***************************//
        //********************************************************************//
        
        //初始化系统时钟、全局复位
        initial
            begin
                sys_clk      = 1'b1;
                sys_rst_n   <= 1'b0;
                #20
                sys_rst_n   <= 1'b1;
            end
        
        //sys_clk:模拟系统时钟，每 10ns 电平翻转一次，周期为 20ns，频率为 50MHz
        always  #10 sys_clk = ~sys_clk;
        
        //********************************************************************//
        //**************************** Instantiation *************************//
        //********************************************************************//
        seg_595_static seg_595_static_inst
        (
            .sys_clk  (sys_clk),
            .sys_rst_n(sys_rst_n),
        
            .ds       (ds),
            .shcp     (shcp),
            .stcp     (stcp),
            .oe       (oe)
        );
        
        endmodule
        ```

    * 导入测试文件，进行Modelsim仿真设置；

    * 开始联仿，查看波形图和打印信息：

      * ![](https://pic.imgdb.cn/item/64fe9606661c6c8e5468cf15.jpg)


#### 17.2.3 仿真验证

* (1)测试代码编写

  * 打开“sim”文件夹新建“.v”文件；

    * ``` verilog
      `timescale  1ns/1ns
      module  tb_seg_595_static();
      //********************************************************************//
      //******************** Paramete and Internal Signal ******************//
      //********************************************************************//
      
      //reg define
      reg     sys_clk         ;
      reg     sys_rst_n       ;
      
      //wire define
      wire    [5:0]   sel     ;
      wire    [7:0]   seg     ;
      wire            ds      ;
      wire            shcp    ;
      wire            stcp    ;
      wire            oe      ;
      
      //********************************************************************//
      //****************************** Main Code ***************************//
      //********************************************************************//
      
      //初始化系统时钟、全局复位
      initial
          begin
              sys_clk      = 1'b1;
              sys_rst_n   <= 1'b0;
              #20
              sys_rst_n   <= 1'b1;
          end
      
      //sys_clk:模拟系统时钟，每 10ns 电平翻转一次，周期为 20ns，频率为 50MHz
      always  #10 sys_clk = ~sys_clk;
      
      //********************************************************************//
      //**************************** Instantiation *************************//
      //********************************************************************//
      seg_595_static seg_595_static_inst
      (
          .sys_clk  (sys_clk),
          .sys_rst_n(sys_rst_n),
      
          .ds       (ds),
          .shcp     (shcp),
          .stcp     (stcp),
          .oe       (oe)
      );
      
      endmodule
      ```

* （2）导入测试文件，进行Modelsim仿真设置；

* （3）开始联仿，查看波形图和打印信息：

  * ![](https://pic.imgdb.cn/item/64fece3c661c6c8e5477f412.jpg)
  * ![](https://pic.imgdb.cn/item/64fe9606661c6c8e5468cf15.jpg)

#### 17.2.4 管脚绑定

* sys_clk-->E1
* sys_rst_n-->M15
* ds-->R1
* oe-->L11
* shcp-->B1
* stcp-->K9


#### 17.2.5 上板子验证

* 点击program按钮导入“.sof”文件；

* 连接板卡，点击“start”按钮下载程序；

* 上板验证；

  * `全是8有问题，`

  * `目前推测是stcp和野火给的例程有差别`（没有影响）

  * `以及data与cnt_bit的长度存在差异，需要后续验证`（没有影响）

  * 问题出现在顶层文件`seg_595_static`中`seg_static`模块实例化中，只要注释掉对局部参数CNT_MAX的语句就可以解决6个8的问题。

    * ``` verilog
      `timescale  1ns/1ns
      module  seg_595_static
      
      //Parameter define
      
      //********************************************************************//
      //***************** Define Input and Output Variables ****************//
      //********************************************************************//
      (
          input       wire            sys_clk     ,
          input       wire            sys_rst_n   ,
          
          output      wire            ds          ,
          output      wire            shcp        ,
          output      wire            stcp        ,
          output      wire            oe         
      );
      
      //********************************************************************//
      //******************** Paramete and Internal Signal ******************//
      //********************************************************************//
      
      //将子模块中的内部信号引到顶层模块中
      wire    [5:0]       sel;
      wire    [7:0]       seg;
      
      //********************************************************************//
      //**************************** Instantiation *************************//
      //********************************************************************//
      
      //静态数码管驱动模块
      seg_static
      /* #(
          .CNT_MAX    (25'd24)
      ) */
      seg_static_inst
      (
          .sys_clk  (sys_clk)  ,
          .sys_rst_n(sys_rst_n),
      
          .sel      (sel)      ,
          .seg      (seg)
      );
      
      //74HC595控制模块
      hc595_ctrl  hc595_ctrl_inst
      (
          .sys_clk  (sys_clk),
          .sys_rst_n(sys_rst_n),
          .sel      (sel),
          .seg      (seg),
          
          .ds       (ds),
          .shcp     (shcp),
          .stcp     (stcp),
          .oe       (oe)
      );
      
      endmodule
      ```


#### 17.2.6 程序固化

* 详情参见3.1.11

## 18 数码管动态显示

### 17.1 理论学习

* 八段数码管动态显示原理:
  * ![](https://pic.imgdb.cn/item/64fd2211661c6c8e541fdd5b.jpg)
  * 动态扫描，从左往右依次闪烁循环，即利用人眼视觉暂留特性和数码管余晖效应。
* BCD码
  * 数码管动态显示需要通过位选和段选信号控制没各个数码管轮流显示一组数据中的单个数据，达到视觉暂留和余晖效应。而传统的2进制码无法显示一组数据中中的每一位数，所以需要转换成十进制的BCD码，一组BCD码即表示一位数，这样就可以轮流显示。
  * 二进制如何转换成十进制BCD码
    * ![](https://pic.imgdb.cn/item/65026078661c6c8e54c35fc4.jpg)


### 17.2 实战演练

#### 17.2.1 实验目标

* 让六位数码管显示从十进制数 0 开始计数，每 0.1s 加 1，一直到加到十进制数 999999。到达 999999 之后回到 0 开始重新计数。

#### 17.2.2 程序设计

* （1）模块框图绘制：

  * ![](https://pic.imgdb.cn/item/64ffc9c5661c6c8e54b23bfe.jpg)
  * 将三层模块整合在一起就是：
    * ![](https://pic.imgdb.cn/item/64ffc92f661c6c8e54b1bc00.jpg)
  * 加入二进制转BCD码模块
    * ![](https://pic.imgdb.cn/item/650265a0661c6c8e54c4d459.jpg)
  * Update
    * ![](https://pic.imgdb.cn/item/6502672f661c6c8e54c563a2.jpg)

* （2）绘制波形图

  * ①[子模块]数据生成模块：
    * ![](https://pic.imgdb.cn/item/64ffd62d661c6c8e54b541e8.jpg)
  * ③[子模块]二进制转BCD码模块：
    * ![](https://pic.imgdb.cn/item/65051606661c6c8e54f2b814.jpg)
  * ③[子模块]动态显示驱动模块：
    * ![](https://pic.imgdb.cn/item/650523a9661c6c8e54f5e965.jpg)


##### 17.2.2.1 子模块①数据生成模块：

* 代码编写：

  * ①[子模块]数据生成模块：

    * ``` verilog
      module data_gen
      //Parameter define
      #(
          parameter       CNT_MAX     = 23'd4_999_999,
          parameter       DATA_MAX    = 20'd999_999  
      )
      //********************************************************************//
      //***************** Define Input and Output Variables ****************//
      //********************************************************************//
      (
          input   wire        sys_clk     ,
          input   wire        sys_rst_n   ,
          
          output  reg  [19:0] data        ,
          output  wire [5:0]  point       ,
          output  wire        sign        ,
          output  reg         seg_en          
      );
      //********************************************************************//
      //******************** Paramete and Internal Signal ******************//
      //********************************************************************//
      
      //Internal Signal define
      reg     [22:0]      cnt_100ms   ;
      reg                 cnt_flag    ;
      
      //********************************************************************//
      //****************************** Main Code ***************************//
      //********************************************************************//
      
      //cnt_100ms：用 50MHz 时钟从 0 到 4999_999 计数即为 100ms
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              cnt_100ms <= 23'd0;
          else    if(cnt_100ms == CNT_MAX)
              cnt_100ms <= 23'd0;
          else
              cnt_100ms <= cnt_100ms + 1'b1;
      
      //cnt_flag:每 100ms 产生一个标志信号
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              cnt_flag <= 1'b0;
          else    if(cnt_100ms == CNT_MAX - 1'b1)
              cnt_flag <= 1'b1;
          else
              cnt_flag <= 1'b0;
      
      //data：数码管显示的数据:0-999_999
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              data <= 20'd0;
          else    if((data == DATA_MAX) && (cnt_flag == 1'b1))
              data <= 20'd0;
          else    if(cnt_flag == 1'b1)
              data <= data + 1'b1;
          else
              data <= data;
      
      //point,sign：不显示小数点以及负数
      assign  point   =   6'b000_000  ;
      assign  sign    =   1'b0        ;
      
      //seg_en：数码管使能信号给高即可
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              seg_en <= 1'b0;
          else
              seg_en <= 1'b1;
      
      endmodule
      ```

    * 【编译报错1】

      * ![](https://pic.imgdb.cn/item/64fd5308661c6c8e54294869.jpg)![](https://pic.imgdb.cn/item/64fd58d5661c6c8e542a46a8.jpg)

        * 方法①：重新编写一个顶层模块，将子功能模块实例化在顶层模块中，对顶层模块进行编译（参考上一章节）。

        * 方法②：选中.v文件，选择强制转化为顶层文件。（本试验采用该方法）

          * ![](https://pic.imgdb.cn/item/64fd53b6661c6c8e542964f6.jpg)

    * 【编译报错2】

      * module "data_gen" cannot be declared more than once

        * 方法：是应为在`tb_data_gen`文件中头文件没有加`tb`:

          * ``` verilog
            `timescale  1ns/1ns
            module  data_gen();
            //错了，应该是：
            `timescale  1ns/1ns
            module  tb_data_gen();
            ```

* 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

* 导入“rtl”文件中编写完成的“.v”文件，进行编译.

  * 可以使用“RTL Viewer”查看代码综合出的视图。

* 仿真测试：

  * 打开“sim”文件夹新建“.v”文件：

    * ``` verilog
      `timescale  1ns/1ns
      module  tb_data_gen();
      //********************************************************************//
      //******************** Paramete and Internal Signal ******************//
      //********************************************************************//
      reg        sys_clk     ;
      reg        sys_rst_n   ;
      
      wire    [19:0]  data   ;
      wire    [5:0]   point  ;
      wire            sign   ;
      wire            seg_en ;
      
      //********************************************************************//
      //****************************** Main Code ***************************//
      //********************************************************************//
      
      //初始化系统时钟、全局复位
      initial
          begin
              sys_clk      = 1'b1;
              sys_rst_n   <= 1'b0;
              #20
              sys_rst_n   <= 1'b1;
          end
      
      //sys_clk:模拟系统时钟，每 10ns 电平翻转一次，周期为 20ns，频率为 50MHz
      always  #10 sys_clk = ~sys_clk;
      
      //********************************************************************//
      //**************************** Instantiation *************************//
      //********************************************************************//
      data_gen
      #(
          .CNT_MAX    (23'd9),
          .DATA_MAX   (20'd9)
      )
      data_gen_inst
      (
          .sys_clk     (sys_clk  ),
          .sys_rst_n   (sys_rst_n),
      
          .data        (data     ),
          .point       (point    ),
          .sign        (sign     ),
          .seg_en      (seg_en   ) 
      );
          
      endmodule
      ```

  * 导入测试文件，进行Modelsim仿真设置；

  * 开始联仿，查看波形图和打印信息：

    * ![](https://pic.imgdb.cn/item/64ffde65661c6c8e54b7aae8.jpg)
    * ![](https://pic.imgdb.cn/item/64ffdf94661c6c8e54b812b3.jpg)

##### 17.2.2.2 子模块②二进制转BCD码模块

* 代码编写：

  * ②[子模块]74HC595控制模块：

    * ``` verilog
      module bcd_8421
      
      //********************************************************************//
      //***************** Define Input and Output Variables ****************//
      //********************************************************************//
      (
          input   wire        sys_clk     ,
          input   wire        sys_rst_n   ,
          input   wire [19:0] data        ,
          
          output  reg  [3:0]  unit        ,
          output  reg  [3:0]  ten         ,
          output  reg  [3:0]  hun         ,
          output  reg  [3:0]  tho         ,
          output  reg  [3:0]  t_tho       ,
          output  reg  [3:0]  h_hun       
      );
      //********************************************************************//
      //******************** Paramete and Internal Signal ******************//
      //********************************************************************//
      
      //Internal Signal define
      reg     [4:0]       cnt_shift   ;
      reg     [43:0]      data_shift  ;
      reg                 shift_flag  ;
      
      //********************************************************************//
      //****************************** Main Code ***************************//
      //********************************************************************//
      
      //shift_flag：移位判断标志信号，用于控制移位判断的先后顺序
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              shift_flag <= 1'b0;
          else
              shift_flag <= ~shift_flag;
      
      //cnt_shift：从 0 到 21 循环计数
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              cnt_shift <= 5'd0;
          else    if((cnt_shift == 5'd21) && (shift_flag == 1'b1))
              cnt_shift <= 5'd0;
          else    if(shift_flag == 1'b1)
              cnt_shift <= cnt_shift + 1'b1;
          else
              cnt_shift <= cnt_shift;
      
      //data_shift：计数器为 0 时赋初值，计数器为 1~20 时进行移位判断操作
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              data_shift <= 44'b0;
          else    if(cnt_shift == 5'd0)
              data_shift <= {24'b0,data};
          else    if((cnt_shift <= 5'd20) && (shift_flag == 1'b0))
              begin
                  data_shift[23:20] <= (data_shift[23:20] > 4) ? (data_shift[23:20] + 2'd3):(data_shift[23:20]);
                  data_shift[27:24] <= (data_shift[27:24] > 4) ? (data_shift[27:24] + 2'd3):(data_shift[27:24]);
                  data_shift[31:28] <= (data_shift[31:28] > 4) ? (data_shift[31:28] + 2'd3):(data_shift[31:28]);
                  data_shift[35:32] <= (data_shift[35:32] > 4) ? (data_shift[35:32] + 2'd3):(data_shift[35:32]);
                  data_shift[39:36] <= (data_shift[39:36] > 4) ? (data_shift[39:36] + 2'd3):(data_shift[39:36]);
                  data_shift[43:40] <= (data_shift[43:40] > 4) ? (data_shift[43:40] + 2'd3):(data_shift[43:40]);
              end
          else    if((cnt_shift <= 5'd20) && (shift_flag == 1'b1))
              data_shift <= data_shift << 1;
          else
              data_shift <= data_shift;
      
      //输出：当计数器等于 20 时，移位判断操作完成，对各个位数的 BCD 码进行赋值
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              begin
                  unit  <= 4'b0;
                  ten   <= 4'b0;
                  hun   <= 4'b0;
                  tho   <= 4'b0;
                  t_tho <= 4'b0;
                  h_hun <= 4'b0;
              end
          else    if(cnt_shift == 5'd21)
              begin
                  unit  <= data_shift[23:20];
                  ten   <= data_shift[27:24];
                  hun   <= data_shift[31:28];
                  tho   <= data_shift[35:32];
                  t_tho <= data_shift[39:36];
                  h_hun <= data_shift[43:40];
              end
      
      endmodule
      ```

    * 【编译报错1】打开modelsim仿真软件后无法显示编译完成的仿真波形和文件

      * ![](https://pic.imgdb.cn/item/6502b7cb661c6c8e54e71989.jpg)

        * 方法①：问题出在实例化中最后一个数据多加了","符号，迷糊啊你。

          * ``` verilog
            bcd_8421 bcd_8421_inst
            (
                .sys_clk  (sys_clk),
                .sys_rst_n(sys_rst_n),
                .data     (data),
            
                .unit     (unit),
                .ten      (ten),
                .hun      (hun),
                .tho      (tho),
                .t_tho    (t_tho),
                .h_hun    (h_hun)，//这是错误的，应该删除“，”！
            );
            ```

* 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

* 导入“rtl”文件中编写完成的“.v”文件，进行编译.

  * 可以使用“RTL Viewer”查看代码综合出的视图。

* 仿真测试：

  * 打开“sim”文件夹新建“.v”文件：

    * ``` verilog
      `timescale  1ns/1ns
      module  tb_bcd_8421();
      //********************************************************************//
      //******************** Paramete and Internal Signal ******************//
      //********************************************************************//
      
      reg                 sys_clk     ;
      reg                 sys_rst_n   ;
      reg      [19:0]     data        ;
      
      wire     [3:0]      unit        ;
      wire     [3:0]      ten         ;
      wire     [3:0]      hun         ;
      wire     [3:0]      tho         ;
      wire     [3:0]      t_tho       ;
      wire     [3:0]      h_hun       ;
      
      //********************************************************************//
      //****************************** Main Code ***************************//
      //********************************************************************//
      
      //初始化系统时钟、全局复位
      initial
          begin
              sys_clk      = 1'b1;
              sys_rst_n   <= 1'b0;
              data        <= 20'd0;
              #30
              sys_rst_n   <= 1'b1;
              data        <= 20'd123_456;
              #3000
              data        <= 20'd654_321;
              #3000
              data        <= 20'd987_654;
              #3000
              data        <= 20'd999_999;
          end
      
      //sys_clk:模拟系统时钟，每 10ns 电平翻转一次，周期为 20ns，频率为 50MHz
      always  #10 sys_clk = ~sys_clk;
      
      //********************************************************************//
      //**************************** Instantiation *************************//
      //********************************************************************//
      bcd_8421 bcd_8421_inst
      (
          .sys_clk  (sys_clk),
          .sys_rst_n(sys_rst_n),
          .data     (data),
      
          .unit     (unit),
          .ten      (ten),
          .hun      (hun),
          .tho      (tho),
          .t_tho    (t_tho),
          .h_hun    (h_hun)
      );
      
      endmodule
      ```

  * 导入测试文件，进行Modelsim仿真设置；

  * 开始联仿，查看波形图和打印信息：

    * ![](https://pic.imgdb.cn/item/6502c259661c6c8e54ed355c.jpg)
    * ![](https://pic.imgdb.cn/item/6502c20b661c6c8e54ed0706.jpg)
    * 
    * ![](https://pic.imgdb.cn/item/6502c3a7661c6c8e54ee4440.jpg)
    * ![](https://pic.imgdb.cn/item/6502c436661c6c8e54ee6a56.jpg)

##### 17.2.2.3 子模块②动态显示驱动模块

* 代码编写：

  * ③[子模块]动态显示驱动模块：

    * ``` verilog
      module seg_dynamic
      //********************************************************************//
      //***************** Define Input and Output Variables ****************//
      //********************************************************************//
      (
          input   wire        sys_clk     ,
          input   wire        sys_rst_n   ,
          input   wire [19:0] data        ,
          input   wire [5:0]  point       ,
          input   wire        sign        ,
          input   wire        seg_en      ,
          
          output  reg [5:0]  sel          ,
          output  reg [7:0]  seg         
      );
      //********************************************************************//
      //******************** Paramete and Internal Signal ******************//
      //********************************************************************//
      
      parameter   CNT_MAX = 16'd49_999;
      //Internal Signal define
      wire    [3:0]     unit        ;
      wire    [3:0]     ten         ;
      wire    [3:0]     hun         ;
      wire    [3:0]     tho         ;
      wire    [3:0]     t_tho       ;
      wire    [3:0]     h_hun       ;
      reg     [23:0]    data_reg    ;
      reg     [15:0]    cnt_1ms     ;
      reg               flag_1ms    ;
      reg     [2:0]     cnt_sel     ;
      reg     [5:0]     sel_reg     ;
      reg     [3:0]     data_disp   ;
      reg               dot_disp    ;
      
      //********************************************************************//
      //****************************** Main Code ***************************//
      //********************************************************************//
      
      //data_reg:移位判断标志信号，用于控制移位判断的先后顺序
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              data_reg <= 24'b0;
          //若显示的十进制数的十万位为非零数据或需显示小数点，则六个数码管全显示
          else    if((h_hun) || (point[5]))
              data_reg <= {h_hun,t_tho,tho,hun,ten,unit};
          //若显示的十进制数的万位为非零数据或需显示小数点，则值显示在 5 个数码管上
          //打比方我们输入的十进制数据为 20’d12345，我们就让数码管显示 12345 而不是 012345
          else    if(((t_tho) || (point[4])) && (sign == 1'b1))//显示负号
              data_reg <= {4'd10,t_tho,tho,hun,ten,unit};//4'd10 我们定义为显示负号
          else    if(((t_tho) || (point[4])) && (sign == 1'b0))
              data_reg <= {4'd11,t_tho,tho,hun,ten,unit};//4'd11 我们定义为不显示
          //若显示的十进制数的千位为非零数据或需显示小数点，则值显示 4 个数码管
          else    if(((tho) || (point[3])) && (sign == 1'b1))
              data_reg <= {4'd11,4'd10,tho,hun,ten,unit};
          else    if(((tho) || (point[3])) && (sign == 1'b0))
              data_reg <= {4'd11,4'd11,tho,hun,ten,unit};
          //若显示的十进制数的百位为非零数据或需显示小数点，则值显示 3 个数码管
          else    if(((hun) || (point[2])) && (sign == 1'b1))
              data_reg <= {4'd11,4'd11,4'd10,hun,ten,unit};
          else    if(((hun) || (point[2])) && (sign == 1'b0))
              data_reg <= {4'd11,4'd11,4'd11,hun,ten,unit};
          //若显示的十进制数的十位为非零数据或需显示小数点，则值显示 2 个数码管
          else    if(((ten) || (point[1])) && (sign == 1'b1))
              data_reg <= {4'd11,4'd11,4'd11,4'd10,ten,unit};
          else    if(((ten) || (point[1])) && (sign == 1'b0))
              data_reg <= {4'd11,4'd11,4'd11,4'd11,ten,unit};
          //若显示的十进制数的个位且需显示负号
          else    if(((unit) || (point[0])) && (sign == 1'b1))
              data_reg <= {4'd11,4'd11,4'd11,4'd11,4'd10,unit};
          //若上面都不满足都只显示一位数码管
          else
              data_reg <= {4'd11,4'd11,4'd11,4'd11,4'd11,unit};
      
      //cnt_1ms:1ms 循环计数
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              cnt_1ms <= 16'd0;
          else    if(cnt_1ms == CNT_MAX)
              cnt_1ms <= 16'd0;
          else
              cnt_1ms <= cnt_1ms + 1'b1;
      
       //flag_1ms:1ms 标志信号
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              flag_1ms <= 1'b0;
          else    if(cnt_1ms == (CNT_MAX - 1'b1))
              flag_1ms <= 1'b1;
          else
              flag_1ms <= 1'b0;
      
      //cnt_sel：从 0 到 5 循环数，用于选择当前显示的数码管
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              cnt_sel <= 3'd0;
          else    if((cnt_sel == 3'd5) && (flag_1ms == 1'b1))
              cnt_sel <= 3'd0;
          else    if(flag_1ms == 1'b1)
              cnt_sel <= cnt_sel + 1'b1;
          else
              cnt_sel <= cnt_sel;
      
      //sel_reg:数码管位选信号寄存器
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              sel_reg <= 6'b000_000;
          else    if((cnt_sel == 3'd0) && (flag_1ms == 1'b1))
              sel_reg <= 6'b000_001;
          else    if(flag_1ms == 1'b1)
              sel_reg <= sel_reg << 1;
          else
              sel_reg <= sel_reg;
      
      //data_disp:控制数码管的位选信号，使六个数码管轮流显示
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              data_disp <= 4'b0;
          else    if((seg_en == 1'b1) && (flag_1ms == 1'b1))
              case(cnt_sel)
              3'd0:   data_disp <= data_reg[3:0]  ;
              3'd1:   data_disp <= data_reg[7:4]  ;
              3'd2:   data_disp <= data_reg[11:8] ;
              3'd3:   data_disp <= data_reg[15:12];
              3'd4:   data_disp <= data_reg[19:16];
              3'd5:   data_disp <= data_reg[23:20];
              default:data_disp <= 4'b0;
              endcase
          else
              data_disp <= data_disp;
      
      //dot_disp：小数点低电平点亮，需对小数点有效信号取反
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              dot_disp <= 1'b1;
          else    if(flag_1ms == 1'b1)
              dot_disp <= ~point[cnt_sel];
          else
              dot_disp <= dot_disp;
      
      //seg:控制数码管段选信号，显示数字
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              seg <= 8'b1111_1111;
          else
              case(data_disp)
                  4'd0    :   seg <= {dot_disp,7'b100_0000};  //显示数字0
                  4'd1    :   seg <= {dot_disp,7'b111_1001};  //显示数字1
                  4'd2    :   seg <= {dot_disp,7'b010_0100};  //显示数字2
                  4'd3    :   seg <= {dot_disp,7'b011_0000};  //显示数字3
                  4'd4    :   seg <= {dot_disp,7'b001_1001};  //显示数字4
                  4'd5    :   seg <= {dot_disp,7'b001_0010};  //显示数字5
                  4'd6    :   seg <= {dot_disp,7'b000_0010};  //显示数字6
                  4'd7    :   seg <= {dot_disp,7'b111_1000};  //显示数字7
                  4'd8    :   seg <= {dot_disp,7'b000_0000};  //显示数字8
                  4'd9    :   seg <= {dot_disp,7'b001_0000};  //显示数字9
                  4'd10   :   seg <= 8'b1011_1111          ;  //显示负号
                  4'd11   :   seg <= 8'b1111_1111          ;  //不显示任何字符
                  default :   seg <= 8'b1100_0000          ;
              endcase
      
      //sel:数码管位选信号赋值
      always@(posedge sys_clk or negedge sys_rst_n)
          if(sys_rst_n == 1'b0)
              sel <= 6'b000_000;
          else
              sel <= sel_reg;
      
      //********************************************************************//
      //**************************** Instantiation *************************//
      //********************************************************************//
      bcd_8421 bcd_8421_inst
      (
          .sys_clk  (sys_clk),
          .sys_rst_n(sys_rst_n),
          .data     (data),
      
          .unit     (unit),
          .ten      (ten),
          .hun      (hun),
          .tho      (tho),
          .t_tho    (t_tho),
          .h_hun    (h_hun)
      );
      
      endmodule
      ```

    * 【编译报错1】打开modelsim仿真软件后无法显示编译完成的仿真波形和文件

      * ![](https://pic.imgdb.cn/item/650567ea661c6c8e54091198.jpg)

        * 方法①：只需要把工程路径中 .\incremental_db\compiled_partitions文件夹删掉，重新编译即可。
          如果删除后还是出现这个报错，那就把.\incremental_db、.\db和.\output_files三个文件夹都删掉（此时得退出quartus才能把这三个文件夹都删掉），之后再重新打开quartus打开工程，再编译即可。

* 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

* 导入“rtl”文件中编写完成的“.v”文件，进行编译.

  * 可以使用“RTL Viewer”查看代码综合出的视图。

* 仿真测试：

  * 打开“sim”文件夹新建“.v”文件：

    * ``` verilog
      `timescale  1ns/1ns
      module  tb_seg_dynamic();
      //********************************************************************//
      //******************** Paramete and Internal Signal ******************//
      //********************************************************************//
      reg         sys_clk     ;
      reg         sys_rst_n   ;
      reg  [19:0] data        ;
      reg  [5:0]  point       ;
      reg         sign        ;
      reg         seg_en      ;
      
      wire [5:0]  sel         ;
      wire [7:0]  seg         ;
      
      //********************************************************************//
      //****************************** Main Code ***************************//
      //********************************************************************//
      
      //初始化系统时钟、全局复位
      initial
          begin
              sys_clk      = 1'b1;
              sys_rst_n   <= 1'b0;
              data        <= 20'd0;
              point       <= 6'b0;
              sign        <= 1'b0;
              seg_en      <= 1'b0;
              #30
              sys_rst_n   <= 1'b1;
              data        <= 20'd9876;
              point       <= 6'b000_010;
              sign        <= 1'b1;
              seg_en      <= 1'b1;
          end
      
      //sys_clk:模拟系统时钟，每 10ns 电平翻转一次，周期为 20ns，频率为 50MHz
      always  #10 sys_clk = ~sys_clk;
      
      //********************************************************************//
      //**************************** Instantiation *************************//
      //********************************************************************//
      defparam    seg_dynamic_inst.CNT_MAX = 20'd5;
      seg_dynamic seg_dynamic_inst
      (
          .sys_clk  (sys_clk  ),
          .sys_rst_n(sys_rst_n),
          .data     (data     ),
          .point    (point    ),
          .sign     (sign     ),
          .seg_en   (seg_en   ),
      
          .sel      (sel      ),
          .seg      (seg      )
      );
      
      endmodule
      ```

  * 导入测试文件，进行Modelsim仿真设置；

  * 开始联仿，查看波形图和打印信息：

    * ![](https://pic.imgdb.cn/item/65057763661c6c8e540f8a0b.jpg)

##### 17.2.2.4 次顶层模块①动态显示驱动模块

* 代码编写：

  * ②[次顶层模块]数码管动态显示模块：

    * ``` verilog
      module seg_595_dynamic
      //********************************************************************//
      //***************** Define Input and Output Variables ****************//
      //********************************************************************//
      (
          input   wire        sys_clk     ,
          input   wire        sys_rst_n   ,
          input   wire [19:0] data        ,
          input   wire [5:0]  point       ,
          input   wire        sign        ,
          input   wire        seg_en      ,
          
          output  reg         ds          ,
          output  reg         shcp        ,
          output  reg         stcp        ,
          output  wire        oe          
      );
      //********************************************************************//
      //******************** Paramete and Internal Signal ******************//
      //********************************************************************//
      
      //申明中间变量，将seg_dynamic的段选和位选信号引出给hc595_ctrl
      wire    [5:0]   sel     ;
      wire    [7:0]   seg     ;
      //********************************************************************//
      //****************************** Main Code ***************************//
      //********************************************************************//
      
      //********************************************************************//
      //**************************** Instantiation *************************//
      //********************************************************************//
      seg_dynamic seg_dynamic_inst
      (
          .sys_clk  (sys_clk  ),
          .sys_rst_n(sys_rst_n),
          .data     (data     ),
          .point    (point    ),
          .sign     (sign     ),
          .seg_en   (seg_en   ),
      
          .sel      (sel      ),
          .seg      (seg      )
      );
      
      hc595_ctrl hc595_ctrl_inst
      (
          .sys_clk  (sys_clk  ),
          .sys_rst_n(sys_rst_n),
          .sel      (sel      ),
          .seg      (seg      ),
      
          .ds       (ds       ),
          .shcp     (shcp     ),
          .stcp     (stcp     ),
          .oe       (oe       )
      );
      
      endmodule
      ```

    * 【编译报错1】打开modelsim仿真软件后无法显示编译完成的仿真波形和文件

      * ![](https://pic.imgdb.cn/item/650599d4661c6c8e541d3528.jpg)

        * 方法①：没有在Quartus II中添加hc595_ctrl文件，添加即可解决

* 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

* 导入“rtl”文件中编写完成的“.v”文件，进行编译.

  * 可以使用“RTL Viewer”查看代码综合出的视图。



##### 17.2.2.5 顶层模块

* 代码编写：

  * 顶层模块：

    * ``` verilog
      module top_seg_595
      //********************************************************************//
      //***************** Define Input and Output Variables ****************//
      //********************************************************************//
      (
          input   wire        sys_clk     ,
          input   wire        sys_rst_n   ,
          
          output  wire        ds          ,
          output  wire        shcp        ,
          output  wire        stcp        ,
          output  wire        oe          
      );
      //********************************************************************//
      //******************** Paramete and Internal Signal ******************//
      //********************************************************************//
      wire    [19:0]  data    ;
      wire    [5:0]   point   ;
      wire            sign    ;
      wire            seg_en  ;
      //********************************************************************//
      //****************************** Main Code ***************************//
      //********************************************************************//
      
      //********************************************************************//
      //**************************** Instantiation *************************//
      //********************************************************************//
      data_gen
      #(
          .CNT_MAX     (23'd4_999_999),
          .DATA_MAX    (20'd999_999  )
      )
      data_gen_inst
      (
          .sys_clk  (sys_clk  ),
          .sys_rst_n(sys_rst_n),
      
          .data     (data     ),
          .point    (point    ),
          .sign     (sign     ),
          .seg_en   (seg_en   )
      );
      
      seg_595_dynamic seg_595_dynamic_inst
      (
          .sys_clk  (sys_clk  ),
          .sys_rst_n(sys_rst_n),
          .data     (data     ),
          .point    (point    ),
          .sign     (sign     ),
          .seg_en   (seg_en   ),
      
          .ds       (ds       ),
          .shcp     (shcp     ),
          .stcp     (stcp     ),
          .oe       (oe       )
      );
      
      endmodule
      ```

    * 【编译报错1】打开modelsim仿真软件后无法显示编译完成的仿真波形和文件

      * * 

* 打开Quartus II软件新建工程文件在“quartus_prj”文件夹当中，并设置好芯片型号和仿真软件；

* 导入“rtl”文件中编写完成的“.v”文件，进行编译.

  * 可以使用“RTL Viewer”查看代码综合出的视图。

    * ![](https://pic.imgdb.cn/item/6505a09e661c6c8e54207117.jpg)

      ![](https://pic.imgdb.cn/item/6505a0e8661c6c8e54207806.jpg)

      ![](https://pic.imgdb.cn/item/6505a0fe661c6c8e54207a70.jpg)

* 仿真测试：

  * 打开“sim”文件夹新建“.v”文件：

    * ``` verilog
      `timescale  1ns/1ns
      module  tb_top_seg_595();
      //********************************************************************//
      //******************** Paramete and Internal Signal ******************//
      //********************************************************************//
      reg         sys_clk     ;
      reg         sys_rst_n   ;
      
      wire        ds          ;
      wire        shcp        ;
      wire        stcp        ;
      wire        oe          ;
      
      
      //********************************************************************//
      //****************************** Main Code ***************************//
      //********************************************************************//
      
      //初始化系统时钟、全局复位
      initial
          begin
              sys_clk      = 1'b1;
              sys_rst_n   <= 1'b0;
              #30
              sys_rst_n   <= 1'b1;
          end
      
      //sys_clk:模拟系统时钟，每 10ns 电平翻转一次，周期为 20ns，频率为 50MHz
      always  #10 sys_clk = ~sys_clk;
      
      //********************************************************************//
      //**************************** Instantiation *************************//
      //********************************************************************//
      defparam    top_seg_595_inst.data_gen_inst.CNT_MAX = 23'd49;
      defparam    top_seg_595_inst.seg_595_dynamic_inst.seg_dynamic_inst.CNT_MAX = 16'd19;
      
      top_seg_595 top_seg_595_inst
      (
          .sys_clk  (sys_clk  ),
          .sys_rst_n(sys_rst_n),
                     
          .ds       (ds       ),
          .shcp     (shcp     ),
          .stcp     (stcp     ),
          .oe       (oe       )
      );
      
      endmodule
      ```

  * 导入测试文件，进行Modelsim仿真设置；

  * 开始联仿，查看波形图和打印信息：

    * ![](https://pic.imgdb.cn/item/6505a634661c6c8e54212e73.jpg)
    * ![](https://pic.imgdb.cn/item/6505a6e4661c6c8e542188db.jpg)
    * ![](https://pic.imgdb.cn/item/6505a7b9661c6c8e5421b631.jpg)
    * ![](https://pic.imgdb.cn/item/6505a819661c6c8e5421c2c6.jpg)

#### 17.2.4 管脚绑定

* sys_clk-->E1
* sys_rst_n-->M15
* ds-->R1
* oe-->L11
* shcp-->B1
* stcp-->K9


#### 17.2.5 上板子验证

* 点击program按钮导入“.sof”文件；

* 连接板卡，点击“start”按钮下载程序；

* 上板验证；

#### 17.2.6 程序固化

* 详情参见3.1.11

