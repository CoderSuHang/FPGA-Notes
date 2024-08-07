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

  * ![image](https://github.com/user-attachments/assets/7f86215e-ed04-4e2b-97f2-0f01c83f726e)

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

  * ![image](https://github.com/user-attachments/assets/2e359846-ccbb-417c-9823-a87041ec40bc)


  * ![image](https://github.com/user-attachments/assets/d934e8b6-c6c0-4eed-914b-47644f202aae)


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

  * ![image](https://github.com/user-attachments/assets/c017404d-55e9-4f1f-9ff4-daab18d012d9)


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

  * ![image](https://github.com/user-attachments/assets/8bcc8d73-a2a9-48a3-b0d2-518864d1e4bb)


  * ![image](https://github.com/user-attachments/assets/f80ee19f-6d6c-455c-9f93-8251739222b6)


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

  * ![image](https://github.com/user-attachments/assets/f57afa8f-211f-4d01-897c-f24e46b02904)


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

  * ![image](https://github.com/user-attachments/assets/fb484643-eb4e-49cf-9230-faef92ebbdd0)


  * ![image](https://github.com/user-attachments/assets/e519532a-c0fa-42b1-9e56-63fe6064b99d)


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
