> 上板实验: 实现一个带有逻辑运算的简单ALU

设计一个能实现如下功能的4位带符号位的 **补码** ALU：

| 功能选择 | 功能     | 操作                            |
| -------- | -------- | ------------------------------- |
| 000      | 加法     | A+B                             |
| 001      | 减法     | A-B                             |
| 010      | 取反     | Not A                           |
| 011      | 与       | A and B                         |
| 100      | 或       | A or B                          |
| 101      | 异或     | A xor B                         |
| 110      | 比较大小 | `If A<B then out=1; else out=0;`  |
| 111      | 判断相等 | `If A==B then out=1; else out=0; `|


ALU进行加减运算时，需要能够判断结果是否为0，是否溢出，是否有进位等。这里，输入的操作数A和B都已经是补码。比较大小请按带符号数的方式设置。

✅ 完成ALU实现

![](实验三%20加法器与ALU.assets/image-20231115212155919.png)

```verilog
module alu(
  input signed [3:0] a,
  input signed [3:0] b,
  input [2:0] op,
  output reg signed [3:0] out,
  output reg overflow,
  output reg carry,
  output reg zero
);

reg [3:0] t_add_Cin;

always @(*) begin
  out = 4'b0000;
  overflow = 1'b0;
  carry = 1'b0;
  zero = 1'b0;
  t_add_Cin = 4'b0000;

  if (op[2:1] == 2'b000) begin
    t_add_Cin =( {4{op[0]}}^b )+ {3'b000, op[0]};
    { carry, out } = a + t_add_Cin;
    overflow = (a[3] == t_add_Cin[3]) && (out[3] != a[3]);
  end else if (op == 3'b010) begin
    out = ~a;
  end else if (op == 3'b011) begin
    out = a & b;
  end else if (op == 3'b100) begin
    out = a | b;
  end else if (op == 3'b101) begin
    out = a ^ b;
  end else if (op == 3'b110) begin
    out = a < b ? 4'b0001 : 4'b0000;
  end else if (op == 3'b111) begin
    out = a == b ? 4'b0001 : 4'b0000;
  end

  if (out == 4'b0000) begin
    zero = 1'b1;
  end else begin
    zero = 1'b0;
  end

end

endmodule
```
