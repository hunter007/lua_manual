编译块(Chunks)
=============

Lua的编译单元也叫`编译块(chunk)`。语法上看，一个`编译块(chunk)`就是一个`语句块`：

	chunk ::= block

Lua 将`编译块(chunk)`看做一个有多个参数的匿名函数的函数体([§3.4.11](../3_4_11_function_definitions.md))，因此，`编译块(chunk)`可以定义本地变量，接收参数，返回值。匿名函数会被编译进外部本地变量`_ENV`（[§2.2](../../ch2/2_2_environments_and_the_global_environment.md)）。The resulting function always has _ENV as its only upvalue, 无论它是否会使用这个变量。


`编译块(chunk)`可以存储到文件中，或者宿主程序内的一个字符串。要运行`编译块(chunk)`，Lua首先载入它，然后将`编译块(chunk)`预编译为虚拟机的指令，然后用一个解释器运行编译后的代码。

`编译块(chunk)`也可以提前编译成二进制的形式；这可以参考程序`luac`和函数`string.dump`。源代码形式的程序和二进制形式的程序都是不能交互的；Lua会自动检测文件类型，做出相应的处理（查看`load`）。