# Verilog

Verilog HDL（简称 Verilog ）是一种硬件描述语言，用于数字电路的系统设计。可对算法级、门级、开关级等多种抽象设计层次进行建模，适合硬件密码学编程。

## 基础语法

### 格式

Verilog 是区分大小写的，格式自由，可以在一行内编写，也可跨多行编写。

每个语句必须以分号为结束符。空白符（换行、制表、空格）都没有实际的意义，在编译阶段可忽略。例如下面两中编程方式都是等效的。

**不换行（不推荐）**

```verilog
**wire** [1:0] results ;**assign** results = (a == 1'b0) ? 2'b01 ： (b==1'b0) ? 2'b10 ： 2'b11 ;
```

**换行（推荐）**

```verilog
**wire** [1:0] results ;
**assign**    results = (a == 1'b0) ? 2'b01 ：
      (b==1'b0) ? 2'b10 ：
        2'b11 ;
```

### 注释

Verilog 中有 2 种注释方式:

用 **`//`** 进行单行注释：

```verilog
reg [3:0] counter ;  // A definition of counter register
```

用 **`\*`** 与 **`*/`** 进行跨行注释:

```verilog
wire [11:0]  addr ;
/* 
Next are notes with multiple lines.
Codes here cannot be compiled.
*/
assign   addr = 12'b0 ;
```

### 条件语句

条件（if）语句用于控制执行语句要根据条件判断来确定是否执行。

条件语句用关键字 if 和 else 来声明，条件表达式必须在圆括号中。

```verilog
if (condition1)       true_statement1 ;
else if (condition2)        true_statement2 ;
else if (condition3)        true_statement3 ;
else                      default_statement ;
```

- if 语句执行时，如果 condition1 为真，则执行 true_statement1 ；如果 condition1 为假，condition2 为真，则执行 true_statement2；依次类推。
- else if 与 else 结构可以省略，即可以只有一个 if 条件判断和一组执行语句 ture_statement1 就可以构成一个执行过程。
- else if 可以叠加多个，不仅限于 1 或 2 个。
- ture_statement1 等执行语句可以是一条语句，也可以是多条。如果是多条执行语句，则需要用 begin 与 end 关键字进行说明。

### case 语句

<!--rehype:wrap-class=col-span-2-->

case 语句格式如下：

```verilog
case(case_expr)
    condition1     :             true_statement1 ;
    condition2     :             true_statement2 ;
    ……
    default        :             default_statement ;
endcase
```

case 语句执行时，如果 condition1 为真，则执行 true_statement1 ; 如果 condition1 为假，condition2 为真，则执行 true_statement2；依次类推。如果各个 condition 都不为真，则执行 default_statement 语句。

default 语句是可选的，且在一个 case 语句中不能有多个 default 语句。

条件选项可以有多个，不仅限于 condition1、condition2 等，而且这些条件选项不要求互斥。虽然这些条件选项是并发比较的，但执行效果是谁在前且条件为真谁被执行。

ture_statement1 等执行语句可以是一条语句，也可以是多条。如果是多条执行语句，则需要用 begin 与 end 关键字进行说明。

### while 循环

while 循环语法格式如下：

```verilog
while (condition) begin
    …
    …
    …
end
```

while 循环中止条件为 condition 为假。

如果开始执行到 while 循环时 condition 已经为假，那么循环语句一次也不会执行。

当然，执行语句只有一条时，关键字 begin 与 end 可以省略。

### for 循环

for 循环语法格式如下：

```verilog
for(initial_assignment; condition ; step_assignment)  begin
    …
    …
    …
end
```

initial_assignment 为初始条件。condition 为终止条件，condition 为假时，立即跳出循环。step_assignment 为改变控制变量的过程赋值语句，通常为增加或减少循环变量计数。一般来说，因为初始条件和自加操作等过程都已经包含在 for 循环中，所以 for 循环写法比 while 更为紧凑，但也不是所有的情况下都能使用 for 循环来代替 while 循环。

### repeat 循环

repeat 循环语法格式如下：

```verilog
repeat (loop_times) begin
    …
    …
    …
end
```

repeat 的功能是执行固定次数的循环，它不能像 while 循环那样用一个逻辑表达式来确定循环是否继续执行。repeat 循环的次数必须是一个常量、变量或信号。如果循环次数是变量信号，则循环次数是开始执行 repeat 循环时变量信号的值。即便执行期间，循环次数代表的变量信号值发生了变化，repeat 执行次数也不会改变。

### forever 循环

forever 循环语法格式如下：

```verilog
forever begin
    …
end
```

forever 语句表示永久循环，不包含任何条件表达式，一旦执行便无限的执行下去，系统函数 $finish 可退出 forever。

forever 相当于 while(1) 。

通常，forever 循环是和时序控制结构配合使用的。

### 函数

<!--rehype:wrap-class=col-span-3-->

函数只能在模块中定义，位置任意，并在模块的任何地方引用，作用范围也局限于此模块。函数主要有以下几个特点：

- 1）不含有任何延迟、时序或时序控制逻辑
- 2）至少有一个输入变量
- 3）只有一个返回值，且没有输出
- 4）不含有非阻塞赋值语句
- 5）函数可以调用其他函数，但是不能调用任务

Verilog 函数声明格式如下：

```verilog
function [range-1:0]     function_id ;
input_declaration ;
 other_declaration ;
procedural_statement ;
endfunction
```

函数在声明时，会隐式的声明一个宽度为 range、 名字为 function_id 的寄存器变量，函数的返回值通过这个变量进行传递。当该寄存器变量没有指定位宽时，默认位宽为 1。

函数通过指明函数名与输入变量进行调用。函数结束时，返回值被传递到调用处。

函数调用格式如下：

```verilog
function_id(input1, input2, …);
```

## 代码示例

### AES

<!--rehype:wrap-class=col-span-2 row-span-3-->

```verilog
/////////////////////////////
//        SubBytes         //
/////////////////////////////
module SubBytes (x, y);
  input  [31:0] x;
  output [31:0] y;

  function [7:0] S;
  input    [7:0] x;
    case (x)
        0:S= 99;   1:S=124;   2:S=119;   3:S=123;   4:S=242;   5:S=107;   6:S=111;   7:S=197;
        8:S= 48;   9:S=  1;  10:S=103;  11:S= 43;  12:S=254;  13:S=215;  14:S=171;  15:S=118;
       16:S=202;  17:S=130;  18:S=201;  19:S=125;  20:S=250;  21:S= 89;  22:S= 71;  23:S=240;
       24:S=173;  25:S=212;  26:S=162;  27:S=175;  28:S=156;  29:S=164;  30:S=114;  31:S=192;
       32:S=183;  33:S=253;  34:S=147;  35:S= 38;  36:S= 54;  37:S= 63;  38:S=247;  39:S=204;
       40:S= 52;  41:S=165;  42:S=229;  43:S=241;  44:S=113;  45:S=216;  46:S= 49;  47:S= 21;
       48:S=  4;  49:S=199;  50:S= 35;  51:S=195;  52:S= 24;  53:S=150;  54:S=  5;  55:S=154;
       56:S=  7;  57:S= 18;  58:S=128;  59:S=226;  60:S=235;  61:S= 39;  62:S=178;  63:S=117;
       64:S=  9;  65:S=131;  66:S= 44;  67:S= 26;  68:S= 27;  69:S=110;  70:S= 90;  71:S=160;
       72:S= 82;  73:S= 59;  74:S=214;  75:S=179;  76:S= 41;  77:S=227;  78:S= 47;  79:S=132;
       80:S= 83;  81:S=209;  82:S=  0;  83:S=237;  84:S= 32;  85:S=252;  86:S=177;  87:S= 91;
       88:S=106;  89:S=203;  90:S=190;  91:S= 57;  92:S= 74;  93:S= 76;  94:S= 88;  95:S=207;
       96:S=208;  97:S=239;  98:S=170;  99:S=251; 100:S= 67; 101:S= 77; 102:S= 51; 103:S=133;
      104:S= 69; 105:S=249; 106:S=  2; 107:S=127; 108:S= 80; 109:S= 60; 110:S=159; 111:S=168;
      112:S= 81; 113:S=163; 114:S= 64; 115:S=143; 116:S=146; 117:S=157; 118:S= 56; 119:S=245;
      120:S=188; 121:S=182; 122:S=218; 123:S= 33; 124:S= 16; 125:S=255; 126:S=243; 127:S=210;
      128:S=205; 129:S= 12; 130:S= 19; 131:S=236; 132:S= 95; 133:S=151; 134:S= 68; 135:S= 23;
      136:S=196; 137:S=167; 138:S=126; 139:S= 61; 140:S=100; 141:S= 93; 142:S= 25; 143:S=115;
      144:S= 96; 145:S=129; 146:S= 79; 147:S=220; 148:S= 34; 149:S= 42; 150:S=144; 151:S=136;
      152:S= 70; 153:S=238; 154:S=184; 155:S= 20; 156:S=222; 157:S= 94; 158:S= 11; 159:S=219;
      160:S=224; 161:S= 50; 162:S= 58; 163:S= 10; 164:S= 73; 165:S=  6; 166:S= 36; 167:S= 92;
      168:S=194; 169:S=211; 170:S=172; 171:S= 98; 172:S=145; 173:S=149; 174:S=228; 175:S=121;
      176:S=231; 177:S=200; 178:S= 55; 179:S=109; 180:S=141; 181:S=213; 182:S= 78; 183:S=169;
      184:S=108; 185:S= 86; 186:S=244; 187:S=234; 188:S=101; 189:S=122; 190:S=174; 191:S=  8;
      192:S=186; 193:S=120; 194:S= 37; 195:S= 46; 196:S= 28; 197:S=166; 198:S=180; 199:S=198;
      200:S=232; 201:S=221; 202:S=116; 203:S= 31; 204:S= 75; 205:S=189; 206:S=139; 207:S=138;
      208:S=112; 209:S= 62; 210:S=181; 211:S=102; 212:S= 72; 213:S=  3; 214:S=246; 215:S= 14;
      216:S= 97; 217:S= 53; 218:S= 87; 219:S=185; 220:S=134; 221:S=193; 222:S= 29; 223:S=158;
      224:S=225; 225:S=248; 226:S=152; 227:S= 17; 228:S=105; 229:S=217; 230:S=142; 231:S=148;
      232:S=155; 233:S= 30; 234:S=135; 235:S=233; 236:S=206; 237:S= 85; 238:S= 40; 239:S=223;
      240:S=140; 241:S=161; 242:S=137; 243:S= 13; 244:S=191; 245:S=230; 246:S= 66; 247:S=104;
      248:S= 65; 249:S=153; 250:S= 45; 251:S= 15; 252:S=176; 253:S= 84; 254:S=187; 255:S= 22;
    endcase
  endfunction

  assign y = {S(x[31:24]), S(x[23:16]), S(x[15: 8]), S(x[ 7: 0])};
endmodule


/////////////////////////////
//      InvSubBytes        //
/////////////////////////////
module InvSubBytes (x, y);
  input  [31:0] x;
  output [31:0] y;

  function [7:0] S;
  input    [7:0] x;
    case (x)
        0:S= 82;   1:S=  9;   2:S=106;   3:S=213;   4:S= 48;   5:S= 54;   6:S=165;   7:S= 56;
        8:S=191;   9:S= 64;  10:S=163;  11:S=158;  12:S=129;  13:S=243;  14:S=215;  15:S=251;
       16:S=124;  17:S=227;  18:S= 57;  19:S=130;  20:S=155;  21:S= 47;  22:S=255;  23:S=135;
       24:S= 52;  25:S=142;  26:S= 67;  27:S= 68;  28:S=196;  29:S=222;  30:S=233;  31:S=203;
       32:S= 84;  33:S=123;  34:S=148;  35:S= 50;  36:S=166;  37:S=194;  38:S= 35;  39:S= 61;
       40:S=238;  41:S= 76;  42:S=149;  43:S= 11;  44:S= 66;  45:S=250;  46:S=195;  47:S= 78;
       48:S=  8;  49:S= 46;  50:S=161;  51:S=102;  52:S= 40;  53:S=217;  54:S= 36;  55:S=178;
       56:S=118;  57:S= 91;  58:S=162;  59:S= 73;  60:S=109;  61:S=139;  62:S=209;  63:S= 37;
       64:S=114;  65:S=248;  66:S=246;  67:S=100;  68:S=134;  69:S=104;  70:S=152;  71:S= 22;
       72:S=212;  73:S=164;  74:S= 92;  75:S=204;  76:S= 93;  77:S=101;  78:S=182;  79:S=146;
       80:S=108;  81:S=112;  82:S= 72;  83:S= 80;  84:S=253;  85:S=237;  86:S=185;  87:S=218;
       88:S= 94;  89:S= 21;  90:S= 70;  91:S= 87;  92:S=167;  93:S=141;  94:S=157;  95:S=132;
       96:S=144;  97:S=216;  98:S=171;  99:S=  0; 100:S=140; 101:S=188; 102:S=211; 103:S= 10;
      104:S=247; 105:S=228; 106:S= 88; 107:S=  5; 108:S=184; 109:S=179; 110:S= 69; 111:S=  6;
      112:S=208; 113:S= 44; 114:S= 30; 115:S=143; 116:S=202; 117:S= 63; 118:S= 15; 119:S=  2;
      120:S=193; 121:S=175; 122:S=189; 123:S=  3; 124:S=  1; 125:S= 19; 126:S=138; 127:S=107;
      128:S= 58; 129:S=145; 130:S= 17; 131:S= 65; 132:S= 79; 133:S=103; 134:S=220; 135:S=234;
      136:S=151; 137:S=242; 138:S=207; 139:S=206; 140:S=240; 141:S=180; 142:S=230; 143:S=115;
      144:S=150; 145:S=172; 146:S=116; 147:S= 34; 148:S=231; 149:S=173; 150:S= 53; 151:S=133;
      152:S=226; 153:S=249; 154:S= 55; 155:S=232; 156:S= 28; 157:S=117; 158:S=223; 159:S=110;
      160:S= 71; 161:S=241; 162:S= 26; 163:S=113; 164:S= 29; 165:S= 41; 166:S=197; 167:S=137;
      168:S=111; 169:S=183; 170:S= 98; 171:S= 14; 172:S=170; 173:S= 24; 174:S=190; 175:S= 27;
      176:S=252; 177:S= 86; 178:S= 62; 179:S= 75; 180:S=198; 181:S=210; 182:S=121; 183:S= 32;
      184:S=154; 185:S=219; 186:S=192; 187:S=254; 188:S=120; 189:S=205; 190:S= 90; 191:S=244;
      192:S= 31; 193:S=221; 194:S=168; 195:S= 51; 196:S=136; 197:S=  7; 198:S=199; 199:S= 49;
      200:S=177; 201:S= 18; 202:S= 16; 203:S= 89; 204:S= 39; 205:S=128; 206:S=236; 207:S= 95;
      208:S= 96; 209:S= 81; 210:S=127; 211:S=169; 212:S= 25; 213:S=181; 214:S= 74; 215:S= 13;
      216:S= 45; 217:S=229; 218:S=122; 219:S=159; 220:S=147; 221:S=201; 222:S=156; 223:S=239;
      224:S=160; 225:S=224; 226:S= 59; 227:S= 77; 228:S=174; 229:S= 42; 230:S=245; 231:S=176;
      232:S=200; 233:S=235; 234:S=187; 235:S= 60; 236:S=131; 237:S= 83; 238:S=153; 239:S= 97;
      240:S= 23; 241:S= 43; 242:S=  4; 243:S=126; 244:S=186; 245:S=119; 246:S=214; 247:S= 38;
      248:S=225; 249:S=105; 250:S= 20; 251:S= 99; 252:S= 85; 253:S= 33; 254:S= 12; 255:S=125; 
    endcase
  endfunction

  assign y = {S(x[31:24]), S(x[23:16]), S(x[15: 8]), S(x[ 7: 0])};
endmodule


/////////////////////////////
//       MixColumns        //
/////////////////////////////
module MixColumns(x, y);
  input  [31:0]  x;
  output [31:0]  y;

  wire [7:0] a3, a2, a1, a0, b3, b2, b1, b0;

  assign a3 = x[31:24];
  assign a2 = x[23:16];
  assign a1 = x[15: 8];
  assign a0 = x[ 7: 0];

  assign b3 = a3 ^ a2;
  assign b2 = a2 ^ a1;
  assign b1 = a1 ^ a0;
  assign b0 = a0 ^ a3;

  assign y = {a2[7] ^ b1[7] ^ b3[6],
              a2[6] ^ b1[6] ^ b3[5],
              a2[5] ^ b1[5] ^ b3[4],
              a2[4] ^ b1[4] ^ b3[3] ^ b3[7],
              a2[3] ^ b1[3] ^ b3[2] ^ b3[7],
              a2[2] ^ b1[2] ^ b3[1],
              a2[1] ^ b1[1] ^ b3[0] ^ b3[7],
              a2[0] ^ b1[0] ^ b3[7],
              a3[7] ^ b1[7] ^ b2[6],
              a3[6] ^ b1[6] ^ b2[5],
              a3[5] ^ b1[5] ^ b2[4],
              a3[4] ^ b1[4] ^ b2[3] ^ b2[7],
              a3[3] ^ b1[3] ^ b2[2] ^ b2[7],
              a3[2] ^ b1[2] ^ b2[1],
              a3[1] ^ b1[1] ^ b2[0] ^ b2[7],
              a3[0] ^ b1[0] ^ b2[7],
              a0[7] ^ b3[7] ^ b1[6],
              a0[6] ^ b3[6] ^ b1[5],
              a0[5] ^ b3[5] ^ b1[4],
              a0[4] ^ b3[4] ^ b1[3] ^ b1[7],
              a0[3] ^ b3[3] ^ b1[2] ^ b1[7],
              a0[2] ^ b3[2] ^ b1[1],
              a0[1] ^ b3[1] ^ b1[0] ^ b1[7],
              a0[0] ^ b3[0] ^ b1[7],
              a1[7] ^ b3[7] ^ b0[6],
              a1[6] ^ b3[6] ^ b0[5],
              a1[5] ^ b3[5] ^ b0[4],
              a1[4] ^ b3[4] ^ b0[3] ^ b0[7],
              a1[3] ^ b3[3] ^ b0[2] ^ b0[7],
              a1[2] ^ b3[2] ^ b0[1],
              a1[1] ^ b3[1] ^ b0[0] ^ b0[7],
              a1[0] ^ b3[0] ^ b0[7]};
endmodule


/////////////////////////////
//     InvMixColumns       //
/////////////////////////////
module InvMixColumns(x, y);
  input  [31:0]  x;
  output [31:0]  y;

  wire [7:0] a3, a2, a1, a0, b3, b2, b1, b0;
  wire [7:0] c3, c2, c1, c0, d3, d2, d1, d0;

  assign a3 = x[31:24];
  assign a2 = x[23:16];
  assign a1 = x[15: 8];
  assign a0 = x[ 7: 0];

  assign b3 = a3 ^ a2;
  assign b2 = a2 ^ a1;
  assign b1 = a1 ^ a0;
  assign b0 = a0 ^ a3;

  assign c3 = {a2[7] ^ b1[7] ^ b3[6],
               a2[6] ^ b1[6] ^ b3[5],
               a2[5] ^ b1[5] ^ b3[4],
               a2[4] ^ b1[4] ^ b3[3] ^ b3[7],
               a2[3] ^ b1[3] ^ b3[2] ^ b3[7],
               a2[2] ^ b1[2] ^ b3[1],
               a2[1] ^ b1[1] ^ b3[0] ^ b3[7],
               a2[0] ^ b1[0] ^ b3[7]};
  assign c2 = {a3[7] ^ b1[7] ^ b2[6],
               a3[6] ^ b1[6] ^ b2[5],
               a3[5] ^ b1[5] ^ b2[4],
               a3[4] ^ b1[4] ^ b2[3] ^ b2[7],
               a3[3] ^ b1[3] ^ b2[2] ^ b2[7],
               a3[2] ^ b1[2] ^ b2[1],
               a3[1] ^ b1[1] ^ b2[0] ^ b2[7],
               a3[0] ^ b1[0] ^ b2[7]};
  assign c1 = {a0[7] ^ b3[7] ^ b1[6],
               a0[6] ^ b3[6] ^ b1[5],
               a0[5] ^ b3[5] ^ b1[4],
               a0[4] ^ b3[4] ^ b1[3] ^ b1[7],
               a0[3] ^ b3[3] ^ b1[2] ^ b1[7],
               a0[2] ^ b3[2] ^ b1[1],
               a0[1] ^ b3[1] ^ b1[0] ^ b1[7],
               a0[0] ^ b3[0] ^ b1[7]};
  assign c0 = {a1[7] ^ b3[7] ^ b0[6],
               a1[6] ^ b3[6] ^ b0[5],
               a1[5] ^ b3[5] ^ b0[4],
               a1[4] ^ b3[4] ^ b0[3] ^ b0[7],
               a1[3] ^ b3[3] ^ b0[2] ^ b0[7],
               a1[2] ^ b3[2] ^ b0[1],
               a1[1] ^ b3[1] ^ b0[0] ^ b0[7],
               a1[0] ^ b3[0] ^ b0[7]};
  assign d3 = {c3[5], c3[4], c3[3] ^ c3[7], c3[2] ^ c3[7] ^ c3[6],
               c3[1] ^ c3[6], c3[0] ^ c3[7], c3[7] ^ c3[6], c3[6]};
  assign d2 = {c2[5], c2[4], c2[3] ^ c2[7], c2[2] ^ c2[7] ^ c2[6],
               c2[1] ^ c2[6], c2[0] ^ c2[7], c2[7] ^ c2[6], c2[6]};
  assign d1 = {c1[5], c1[4], c1[3] ^ c1[7], c1[2] ^ c1[7] ^ c1[6],
               c1[1] ^ c1[6], c1[0] ^ c1[7], c1[7] ^ c1[6], c1[6]};
  assign d0 = {c0[5], c0[4], c0[3] ^ c0[7], c0[2] ^ c0[7] ^ c0[6],
               c0[1] ^ c0[6], c0[0] ^ c0[7], c0[7] ^ c0[6], c0[6]};
  assign y  = {d3 ^ d1 ^ c3, d2 ^ d0 ^ c2, d3 ^ d1 ^ c1, d2 ^ d0 ^ c0};
endmodule


/////////////////////////////
//     Encryotion Core     //
/////////////////////////////
module EncCore(di, ki, Rrg, do, ko);   										
  //参数依次为128位明文、轮密钥、当前加密轮数、下一轮状态矩阵、下一轮轮密钥
  input  [127:0] di;
  input  [127:0] ki;
  input  [9:0]   Rrg;
  output [127:0] do;
  output [127:0] ko;

  wire   [127:0] sb, sr, mx;//中间值，依次保存状态矩阵字节代换、行移位、列混合后的结果
  wire   [31:0]  so;//中间值，保存密钥扩展中函数T行移位与字节代换后的结果

  SubBytes SB3 (di[127:96], sb[127:96]);
  SubBytes SB2 (di[ 95:64], sb[ 95:64]);
  SubBytes SB1 (di[ 63:32], sb[ 63:32]);
  SubBytes SB0 (di[ 31: 0], sb[ 31: 0]);

  assign sr = {sb[127:120], sb[ 87: 80], sb[ 47: 40], sb[  7:  0],
               sb[ 95: 88], sb[ 55: 48], sb[ 15:  8], sb[103: 96],
               sb[ 63: 56], sb[ 23: 16], sb[111:104], sb[ 71: 64],
               sb[ 31: 24], sb[119:112], sb[ 79: 72], sb[ 39: 32]};

  MixColumns MX3 (sr[127:96], mx[127:96]);
  MixColumns MX2 (sr[ 95:64], mx[ 95:64]);
  MixColumns MX1 (sr[ 63:32], mx[ 63:32]);
  MixColumns MX0 (sr[ 31: 0], mx[ 31: 0]);

  assign do = ((Rrg[0] == 1)? sr: mx) ^ ki;//判断是否为第10轮，若是，则跳过列混合使用sr轮密钥加，否则使用mx轮密钥加
  //以上过程完成一轮加密获得下一轮状态矩阵do

  function [7:0] rcon;//生成密钥扩展中的T函数中的rcon[j]
  input [9:0] x;//x对应当前加密轮数
    casex (x)
      10'bxxxxxxxxx1: rcon = 8'h01;
      10'bxxxxxxxx1x: rcon = 8'h02;
      10'bxxxxxxx1xx: rcon = 8'h04;
      10'bxxxxxx1xxx: rcon = 8'h08;
      10'bxxxxx1xxxx: rcon = 8'h10;
      10'bxxxx1xxxxx: rcon = 8'h20;
      10'bxxx1xxxxxx: rcon = 8'h40;
      10'bxx1xxxxxxx: rcon = 8'h80;
      10'bx1xxxxxxxx: rcon = 8'h1b;
      10'b1xxxxxxxxx: rcon = 8'h36;
    endcase
  endfunction

  SubBytes SBK ({ki[23:16], ki[15:8], ki[7:0], ki[31:24]}, so);
  //字循环后S盒字节代换，结果保存在so中

  assign ko[127:96] = ki[127:96] ^ {so[31:24] ^ rcon(Rrg), so[23: 0]};//对于W[4i]的扩展
  assign ko[ 95:64] = ki[ 95:64] ^ ko[127:96];
  assign ko[ 63:32] = ki[ 63:32] ^ ko[ 95:64];
  assign ko[ 31: 0] = ki[ 31: 0] ^ ko[ 63:32];
endmodule
	//以上过程生成下一轮加密的轮密钥


/////////////////////////////
//     Decryotion Core     //
/////////////////////////////
module DecCore(di, ki, Rrg, do, ko);
  input  [127:0] di;
  input  [127:0] ki;
  input  [9:0]   Rrg;
  output [127:0] do;
  output [127:0] ko;

  wire   [127:0] sb, sr, mx, dx;
  wire   [31:0]  so;

  InvMixColumns MX3 (di[127:96], mx[127:96]);
  InvMixColumns MX2 (di[ 95:64], mx[ 95:64]);
  InvMixColumns MX1 (di[ 63:32], mx[ 63:32]);
  InvMixColumns MX0 (di[ 31: 0], mx[ 31: 0]);

  assign dx = (Rrg[8] == 1)? di: mx;
  assign sr = {dx[127:120], dx[ 23: 16], dx[ 47: 40], dx[ 71: 64],
               dx[ 95: 88], dx[119:112], dx[ 15:  8], dx[ 39: 32],
               dx[ 63: 56], dx[ 87: 80], dx[111:104], dx[  7:  0],
               dx[ 31: 24], dx[ 55: 48], dx[ 79: 72], dx[103: 96]};

  InvSubBytes SB3 (sr[127:96], sb[127:96]);
  InvSubBytes SB2 (sr[ 95:64], sb[ 95:64]);
  InvSubBytes SB1 (sr[ 63:32], sb[ 63:32]);
  InvSubBytes SB0 (sr[ 31: 0], sb[ 31: 0]);

  assign do = sb ^ ki;

  function [7:0] rcon;
  input [9:0] x;
    casex (x)
      10'bxxxxxxxxx1: rcon = 8'h01;
      10'bxxxxxxxx1x: rcon = 8'h02;
      10'bxxxxxxx1xx: rcon = 8'h04;
      10'bxxxxxx1xxx: rcon = 8'h08;
      10'bxxxxx1xxxx: rcon = 8'h10;
      10'bxxxx1xxxxx: rcon = 8'h20;
      10'bxxx1xxxxxx: rcon = 8'h40;
      10'bxx1xxxxxxx: rcon = 8'h80;
      10'bx1xxxxxxxx: rcon = 8'h1b;
      10'b1xxxxxxxxx: rcon = 8'h36;
    endcase
  endfunction

  SubBytes SBK ({ko[23:16], ko[15:8], ko[7:0], ko[31:24]}, so);
  assign ko[127:96] = ki[127:96] ^ {so[31:24] ^ rcon(Rrg), so[23: 0]};
  assign ko[ 95:64] = ki[ 95:64] ^ ki[127:96];
  assign ko[ 63:32] = ki[ 63:32] ^ ki[ 95:64];
  assign ko[ 31: 0] = ki[ 31: 0] ^ ki[ 63:32];
endmodule


/////////////////////////////
//   AES for encryption    //
/////////////////////////////
module AES_ENC(Din, Key, Dout, Drdy, Krdy, RSTn, EN, CLK, BSY, Dvld);
  input  [127:0] Din;  // Data input
  input  [127:0] Key;  // Key input
  output [127:0] Dout; // Data output
  input  Drdy;         // Data input ready
  input  Krdy;         // Key input ready
  input  RSTn;         // Reset (Low active)
  input  EN;           // AES circuit enable
  input  CLK;          // System clock
  output BSY;          // Busy signal
  output Dvld;         // Data output valid

  reg  [127:0] Drg;    // Data register
  reg  [127:0] Krg;    // Key register
  reg  [127:0] KrgX;   // Temporary key Register
  reg  [9:0]   Rrg;    // Round counter
  reg  Dvldrg, BSYrg;
  wire [127:0] Dnext, Knext;

  EncCore EC (Drg, KrgX, Rrg, Dnext, Knext);

  assign Dvld = Dvldrg;
  assign Dout = Drg;
  assign BSY  = BSYrg;

  always @(posedge CLK) begin
    if (RSTn == 0) begin
      Rrg    <= 10'b0000000001;
      Dvldrg <= 0;
      BSYrg  <= 0;
    end
    else if (EN == 1) begin
       if (BSYrg == 0) begin
        if (Krdy == 1) begin
          Krg    <= Key;
          KrgX   <= Key;
          Dvldrg <= 0;
        end
        else if (Drdy == 1) begin
          Rrg    <= {Rrg[8:0], Rrg[9]};
          KrgX   <= Knext;
          Drg    <= Din ^ Krg;
          Dvldrg <= 0;
          BSYrg  <= 1;
        end
      end
      else begin
        Drg <= Dnext;
        if (Rrg[0] == 1) begin
          KrgX   <= Krg;
          Dvldrg <= 1;
          BSYrg  <= 0;
        end
        else begin
          Rrg    <= {Rrg[8:0], Rrg[9]};
          KrgX   <= Knext;
        end
      end
    end
  end
endmodule


/////////////////////////////
//   AES for decryption    //
/////////////////////////////
module AES_DEC(Din, Key, Dout, Drdy, Krdy, RSTn, EN, CLK, BSY, Dvld);
  input  [127:0] Din;  // Data input
  input  [127:0] Key;  // Key input
  output [127:0] Dout; // Data output
  input  Drdy;         // Data input ready
  input  Krdy;         // Key input ready
  input  RSTn;         // Reset (Low active)
  input  EN;           // AES circuit enable
  input  CLK;          // System clock
  output BSY;          // Busy signal
  output Dvld;         // Data output valid

  reg  [127:0] Drg;    // Data register
  reg  [127:0] Krg;    // Key register
  reg  [127:0] KrgX;   // Temporary key Register
  reg  [9:0]   Rrg;    // Round counter
  reg  Dvldrg, BSYrg;
  wire [127:0] Dnext, Knext;

  DecCore DC (Drg, KrgX, Rrg, Dnext, Knext);

  assign Dvld = Dvldrg;
  assign Dout = Drg;
  assign BSY  = BSYrg;

  always @(posedge CLK) begin
    if (RSTn == 0) begin
      Rrg    <= 10'b1000000000;
      Dvldrg <= 0;
      BSYrg  <= 0;
    end
    else if (EN == 1) begin
      if (BSYrg == 0) begin
        if (Krdy == 1) begin
          Krg    <= Key;
          KrgX   <= Key;
          Dvldrg <= 0;
        end
        else if (Drdy == 1) begin
          KrgX   <= Knext;
          Drg    <= Din ^ Krg;
          Rrg <= {Rrg[0], Rrg[9:1]};
          Dvldrg <= 0;
          BSYrg  <= 1;
        end
      end
      else begin
        Drg <= Dnext;
        if (Rrg[9] == 1) begin
          KrgX   <= Krg;
          Dvldrg <= 1;
          BSYrg  <= 0;
        end
        else begin
          Rrg    <= {Rrg[0], Rrg[9:1]};
          KrgX   <= Knext;
        end
      end
    end
  end
endmodule
```

### A51

```verilog
module LFSR(key,key_next);
  input  [63:0] key;         // 输入原始密钥
  output [63:0] key_next;    // 输出下一个密钥

  wire   [18:0]x;            // lfsr1
  wire   [21:0]y;            // lfsr2
  wire   [22:0]z;            // lfsr3
  wire   [18:0]x_next;
  wire   [21:0]y_next;
  wire   [22:0]z_next;
  wire   majority;

  assign x=key[18:0];        // 低位是 x
  assign y=key[40:19];
  assign z=key[63:41];       // 高位是 z
  
  function [0:0] maj(input x,y,z);
    maj = x&y | x&z | y&z;   // 计算多数位
  endfunction

  function [18:0] lfsr1(input [19:0]in);
  begin
    case(in[19])
      0:lfsr1={in[17:0], in[18]^in[17]^in[16]^in[13]};   // 如果最高位为0，进行位运算
      1:lfsr1=in[18:0];      // 如果最高位为1，保持不变
    endcase
  end
  endfunction

  function [21:0] lfsr2(input [22:0]in);
  begin
    case(in[22])
      0:lfsr2={in[20:0], in[21]^in[20]};   // 如果最高位为0，进行位运算
      1:lfsr2=in[21:0];      // 如果最高位为1，保持不变
    endcase
  end
  endfunction

  function [22:0] lfsr3(input [23:0]in);
  begin
    case(in[23])
      0:lfsr3={in[21:0], in[22]^in[21]^in[20]^in[7]};   // 如果最高位为0，进行位运算
      1:lfsr3=in[22:0];      // 如果最高位为1，保持不变
    endcase
  end
  endfunction
  
  assign majority = maj(x[8],y[10],z[10]);   // 计算多数位
  assign x_next = lfsr1({majority^x[8], x[18:0]});  // 计算下一个 x
  assign y_next = lfsr2({majority^y[10], y[21:0]});  // 计算下一个 y
  assign z_next = lfsr3({majority^z[10], z[22:0]});  // 计算下一个 z
  assign key_next = {z_next,y_next,x_next};         // 组合下一个密钥，低位是 x，高位是 z
endmodule

module A51(key,plain,cipher,clk,krdy);
input  [63:0]key;            // 原始密钥
input  plain;                // 明文流
output cipher;               // 密文流
input  clk;                  // 系统时钟
input  krdy;                 // 原始密钥是否就绪

reg [63:0] kpre;             // 密钥寄存器
wire[63:0] knext;            // 下一个密钥
reg k;                       // 密文流的密钥

LFSR lfsr(kpre,knext);       // 调用 LFSR 模块

assign cipher=k^plain;       // 计算密文流的密钥

always @(posedge clk) begin
  if(krdy==1) begin           // 如果原始密钥已经就绪，则刷新密钥寄存器
    kpre <=key;
  end
  else begin                  // 如果原始密钥未就绪，则使用下一个密钥
    k = knext[18]^knext[40]^knext[63];   // 计算下一个密文流的密钥
    kpre <= knext;
  end
end
endmodule

```

### SHA256

```verilog
`include "sha_engine.v"
module sha256(input_data,input_valid,input_ready,last_word,clk,rst,output_valid,hash_data);

input [31:0]input_data;
input input_valid;
input last_word;
input clk,rst;

output reg input_ready;
output wire [255:0]hash_data;
output wire output_valid;

wire [6:0]block_counter;
reg [4:0] length_counter;
reg [63:0]size;
reg [1:0]special;
reg [31:0]output_data;
reg D;
reg last_word_delayed;
reg [1:0]last_next;


always @(negedge rst)
begin
last_next = 2'b00;
input_ready <= 1'b1;
length_counter <= 5'd31;
special <= 2'd0;
size <= 64'hffffffffffffffe0;
D <= 1'b1;
last_word_delayed <= 1'b0;
end


sha_engine Eng(output_data,clk,block_counter,rst,output_valid,hash_data,last_word,last_next);

always @(posedge clk)
begin

	if(input_valid == 1'b1 && input_ready == 1'b1)
	begin
		if(length_counter == 5'd14)
			input_ready <= 1'b0;

		length_counter = length_counter + 1;
	end
	
end

always @(posedge clk)
begin
	if(last_word == 1'b1)
		last_word_delayed <= 1'b1;
end

always @(posedge last_word_delayed)
begin
	if(length_counter<=13)
		special <= 2'b01;
	else
		special <= 2'b11;
end

always @(posedge clk)
begin
	case(special)
		2'b00 : begin
			output_data <= input_data;
			last_next <= 2'b00;
			end
		2'b01 : begin
			last_next <= 2'b01;

			if(length_counter<15)
			begin
				if(length_counter == 1 && size == 512)
					output_data <= {1'b1,{31{1'b0}}};
				else 
				begin
				D <= 1'b0;
				output_data <= {D,{31{1'b0}}};
				end
			end
			else if(length_counter == 15)
			begin
				output_data <= size[63:32];
				length_counter <= length_counter + 1;
			end
			else
				output_data <= size[31:0];
			end
		2'b11 : begin
				last_next <= 2'b10;
				D <= 1'b0;
				output_data <= {D,{31{1'b0}}};

			end
	endcase
end

always @(posedge clk)
begin
	if(block_counter == 64)
	begin
		length_counter <= 5'd31;
		input_ready <= 1'b1;
	end
	if(block_counter == 65)
	begin
		if(special == 3)
			special <= 2'b01;
		else
			special <= 2'd0;
	end
end

always @(posedge clk)
begin
	if(input_ready == 1 && special == 0)
		size <=  size + 32;
end

always @(posedge last_word)
begin
	if(length_counter == 15)
		size <= size + 32;
end
endmodule
```

### Montgomery

```verilog
`timescale 1ns / 1ps


module mont_mult_modif(
	x, y,
	clk, reset, start,
	z,
	done1
    );
parameter m =192'hfffffffffffffffffffffffffffffffeffffffffffffffff,
k = 192, logk = 8, zero = { logk {1'b0}},
minus_m = {1'b0,192'h000000000000000000000000000000010000000000000001},
delay = 8'b01100000, COUNT = 8'b10111111; //(k-1,logk)

parameter S0 = 3'd0, S1 = 3'd1, S2 = 3'd2, S3 = 3'd3, S4 = 3'd4;

input [k-1:0] x;
input [k-1:0] y;
input clk, reset, start;
output [k-1:0] z;
output done1;

reg [logk-1:0] count;
reg [logk-1:0] timer_state;
reg [k:0] pc, psa;
reg [k-1:0] int_x;
wire equal_zero, time_out;
wire [k:0] y_by_xi, half_ac, half_as, half_bc, half_bs, next_pc, next_psa, p, p_minus_m;
wire [k+1:0] ac, as, bc, bs, long_m;
wire xi;
reg load, ce_p, load_timer;
reg [2:0] current_state;
reg [2:0] next_state;
reg done;

assign done1 = done;
genvar i;
generate for(i=0;i<k;i=i+1) 
	begin:and_gates
		and a(y_by_xi[i],y[i],xi);
	end
endgenerate

assign y_by_xi[k] = 1'b0;
generate for(i=0;i<=k;i=i+1) 
	begin:first_csa
		xor x(as[i],pc[i],psa[i],y_by_xi[i]);
		wire w1,w2,w3;
		and a1(w1,pc[i],psa[i]);
		and a2(w2,pc[i],y_by_xi[i]);
		and a3(w3,psa[i],y_by_xi[i]);
		or o(ac[i+1],w1,w2,w3);
	end
endgenerate
assign ac[0] = 1'b0, as[k+1] = 1'b0, long_m = {{2'b00},m};
generate for(i=0;i<=k;i=i+1) 
	begin:second_csa
		xor x(bs[i],ac[i],as[i],long_m[i]);
		wire w1,w2,w3;
		and a1(w1,ac[i],as[i]);
		and a2(w2,ac[i],long_m[i]);
		and a3(w3,as[i],long_m[i]);
		or o(bc[i+1],w1,w2,w3);
	end
endgenerate

assign bc[0] = 1'b0, bs[k+1] = ac[k+1],
	half_as = as[k+1:1], half_ac = ac[k+1:1],
	half_bs = bs[k+1:1], half_bc = bc[k+1:1];

assign next_pc = (as[0]==1'b0)? half_ac:half_bc;
assign next_psa = (as[0]==1'b0)? half_as:half_bs;

always@(posedge(clk)) 
	begin:parallel_register
		if (load==1'b1) begin
			pc = { k+1 {1'b0} }; psa = { k+1 {1'b0} }; end
		else if (ce_p==1'b1) begin
			pc = next_pc; psa = next_psa; end
	end 
assign equal_zero = (count==zero)? 1'b1:1'b0;
assign p = psa + pc, p_minus_m = p + minus_m;
assign z = (p_minus_m[k]==1'b0)? p[k-1:0]:p_minus_m[k-1:0];

always@(posedge(clk))
	begin:shift_register
		integer i;
		if (load==1'b1) int_x = x; 
		else if (ce_p==1'b1) begin
			for(i=0;i<=k-2;i=i+1) int_x[i] = int_x[i+1];
			int_x[k-1] = 1'b0; end
	end 
assign xi = int_x[0];

always@(posedge(clk)) 
	begin:counter
		if (load==1'b1) count <= COUNT;  
		else if (ce_p==1'b1) count <= count - 1'b1;
	end 
	
	always@(clk, current_state) begin
			case(current_state) 
				S0: begin ce_p = 1'b0; load = 1'b0; load_timer = 1'b1; done = 1'b1; end
				S1: begin ce_p = 1'b0; load = 1'b0; load_timer = 1'b1; done = 1'b1; end
				S2: begin ce_p = 1'b0; load = 1'b1; load_timer = 1'b1; done = 1'b0; end
				S3: begin ce_p = 1'b1; load = 1'b0; load_timer = 1'b1; done = 1'b0; end
				S4: begin ce_p = 1'b0; load = 1'b0; load_timer = 1'b0; done = 1'b0; end
				default: begin ce_p = 1'b0; load = 1'b0; load_timer = 1'b1; done = 1'b1; end
			endcase
	end
	
	always@(posedge clk) begin	
		if(reset) current_state = S0;
		else current_state = next_state;
	end
	
	always@(*) begin
		next_state = current_state;
		if (reset==1'b1) next_state = S0; 
		else if (clk==1'b1) begin
			case(next_state)
				S0: if(start==1'b0) next_state = S1;
				S1: if(start==1'b1) next_state = S2;
				S2: next_state = S3;
				S3: if(equal_zero==1'b1) next_state = S4;
				S4: if(time_out==1'b1) next_state = S0;
				default: next_state = S0;
			endcase
		end
	end

always@(posedge clk)
	begin:timer
		if (clk==1'b1) begin
			if (load_timer==1'b1) timer_state = delay; 
			else timer_state = timer_state - 1'b1;
		end
	end
	
assign time_out = (timer_state==zero)? 1'b1:1'b0;
endmodule

```

### Grain_128

```verilog
module h_x
(
    input s8,
    input s13,
    input s20,
    input s42,
    input s60,
    input s79,
    input s95,
    input b12,
    input b95,
    output ho
);

// 通过一系列逻辑操作计算输出ho
assign ho = (b12 & s8) ^ (s13 & s20) ^ (b95 & s42) ^ (s60 & s79) ^ (b12 & s95 & b95);

endmodule

module feedback_linear
(
    input s0,
    input s7,
    input s38,
    input s70,
    input s81,
    input s96,
    output lfb
);

// 通过异或逻辑操作计算输出lfb
assign lfb = s0 ^ s7 ^ s38 ^ s70 ^ s81 ^ s96;

endmodule

module feedback_non_linear
(
    input b0,
    input b26,
    input b56,
    input b91,
    input b96,
    input b3,
    input b67,
    input b11,
    input b13,
    input b17,
    input b18,
    input b27,
    input b59,
    input b40,
    input b48,
    input b61,
    input b65,
    input b68,
    input b84,
    output nfb
);

// 通过一系列异或和与逻辑操作计算输出nfb
assign nfb = b0 ^ b26 ^ b56 ^ b91 ^ b96 ^ (b3 & b67) ^ (b11 & b13) ^ (b17 & b18) ^ (b27 & b59) ^ (b40 & b48) ^ (b61 & b65) ^ (b68 & b84); 

endmodule


module grain128 
(
    input clk,
    input rst,
    input [127:0] key,
    input [95:0] iv,
    input gen,
    output reg rdy,
    output z,
    output [31:0] tag
);

localparam INIT = 0;
localparam RDY  = 1;

reg [127:0] s;            //LFSR 线性反馈移位寄存器
reg [127:0] b;            //NFSR 非线性反馈移位寄存器
reg curr_state = INIT;
reg next_state = INIT;
reg [7:0] cnt  = 0;       //Counter 计数器
reg m          = 1;       //Mode 模式选择

wire f,g,h;               //用于存储反馈模块的输出
wire nfb, lfb;            //用于存储非线性反馈和线性反馈的输出
wire mo;                  //MUX out 多路选择器输出
wire y;                   //y
wire [127:0] key_in;      //Flipped key 翻转后的密钥
wire [95:0] iv_in;        //Flipped iv 翻转后的初始化向量

// 实例化反馈模块
feedback_linear FB_L (s[0], s[7], s[38], s[70], s[81], s[96], f);
feedback_non_linear FB_N (b[0], b[26], b[56], b[91], b[96], b[3], b[67], b[11], b[13], b[17], b[18], b[27], b[59], b[40], b[48], b[61], b[65], b[68], b[84], g);
h_x HX (s[8], s[13], s[20], s[42], s[60], s[79], s[95], b[12], b[95], h);

// 计算mo
assign mo  = m & y;
// 计算lfb和nfb
assign lfb = f ^ mo;
assign nfb = g ^ mo ^ s[0];
// 计算y
assign y   = h ^ s[93] ^ b[2] ^ b[15] ^ b[36] ^ b[45] ^ b[64] ^ b[73] ^ b[89];
// 输出z等于y
assign z   = y;

assign tag = 0;

// 生成翻转后的初始化向量
generate
    genvar i;
    for (i = 0; i < 96; i = i + 1) begin
        assign iv_in[i] = iv[95-i];
    end
endgenerate

// 生成翻转后的密钥
generate
    genvar j;
    for(j = 0; j < 128; j = j + 1) begin
        assign key_in[j] = key[127-j];
    end
endgenerate

always @(posedge clk) begin
    if (rst) begin
        curr_state <= INIT;
        s          <= {{32{1'b1}},iv_in};
        b          <= key_in;
        cnt        <= 0;
    end
    else begin
        curr_state <= next_state;
    end
    
    case (curr_state)
        INIT : begin
            m   <= 1;
            rdy <= 0;
            if (cnt == 255) begin
                next_state <= RDY;
            end
            else begin
                next_state <= INIT;
                cnt        <= cnt + 1;
            end
            s = {lfb, s[127:1]};
            b = {nfb, b[127:1]};
        end
        RDY : begin
            rdy <= 1;
            m   <= 0;

            if (gen && rdy) begin
                s = {lfb, s[127:1]};
                b = {nfb, b[127:1]};
            end
            next_state <= RDY;
        end
    endcase
end
endmodule

```

