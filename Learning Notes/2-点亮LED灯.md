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
  * ![image](https://github.com/user-attachments/assets/9c249afc-3cc3-4371-a275-1a3686c4525f)

  * ![image](https://github.com/user-attachments/assets/97023c7d-83cb-49b3-9f18-43b176fff62b)

  * KEY1引脚为：CLK2_M2
  * ![image](https://github.com/user-attachments/assets/a9131b5f-2012-4524-95b3-613acfcb5ad5)

  * D6引脚为：IO_B3_L7
  * ![image](https://github.com/user-attachments/assets/4a2b9ae1-559c-46c3-b457-92bf87441cfa)


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
