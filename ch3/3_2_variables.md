变量
====

变量是存储值的地方。Lua有三种变量：全局变量、本地变量和表字段。

标识符名称可以反映出是全局变量还是本地变量（或者函数的形式参数，一种特别的本地变量）：

	var ::= Name

`Name`是一个有效的标识符。

任何变量名被假设为全局的，除非显式地声明为本地的（[3.3.7](./3_3_statements/3_3_7_local_declarations.md)) 本地变量是在语法作用域的：本地变量可以自由地被所定义的函数访问（[3.5](./3_5_visibility_rules.md)）

第一次给变量赋值之前，它的值是`nil`。

方括号`[]`被用来作为表的索引：

	var ::= prefixexp '[' exp ']'

The meaning of accesses to table fields can be changed via metatables (see §2.4).

语法`var.Name`是`var["Name"]`的一个语法糖：

	var ::= prefixexp '.' Name

An access to a global variable x is equivalent to _ENV.x. Due to the way that chunks are compiled, _ENV is never a global name (see §2.2).

访问全局变量`x`，相当于访问`_ENV.x`。由于chunk的编译方式，`_ENV`绝对不是一个全局名称。([2.2](../ch2/2_2_environments_and_the_global_environment.md))
