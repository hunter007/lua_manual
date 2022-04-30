位操作符
=======

Lua支持的位操作符：

`&`: 按位与

`|`: 按位或

`~`: 按位异或

`>>`: 右移

`<<`: 左移

`~`: 一元按位取反

所有位操作符视操作数为整数[§3.4.3](./3_4_3_coercions_and_conversions.md)，在整个整数上执行位操作，结果也是整数。

右移和左移都会用0填充空位。Negative displacements shift to the other direction; displacements with absolute values equal to or higher than the number of bits in an integer result in zero (as all bits are shifted out).
