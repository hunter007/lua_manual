控制结构
=======

控制结构 `if`、`while`和 `repeat`有常见的意义和类似的语法：

	stat ::= while exp do block end
	stat ::= repeat block until exp
	stat ::= if exp then block {elseif exp then block} [else block] end

Lua也支持`for`语句，有两种形式([§3.3.5](./3_3_5_for_statement.md))

控制结构的条件表达式可以返回任何值。`false`和`nil`都被视为false。所有其他值都被视为true（数字0和空腹穿也是true）。

在`repeat-until`语句中，内部快不以`until`结束，而是在条件之后。所以，条件语句可以引用声明在内部循环中的本地变量。


`goto`语句将程序控制跳转到一个标签处。语法上看，Lua中的标签也被视为语句：

	stat ::= goto Name
	stat ::= label
	label ::= '::' Name '::'

A label is visible in the entire block where it is defined, except inside nested blocks where a label with the same name is defined and inside nested functions. A goto may jump to any visible label as long as it does not enter into the scope of a local variable.

标签在整个定义它的语句块中都是可见的，

标签和空语句被void参数调用，因为他们没有要执行的动作。


`break`终止`while`、`repeat` 或 `for`的执行，略过剩下的语句：

	stat ::= break

一个`break`仅仅结束最内层的循环。


`return` 用于从函数中或者`编译块(chunk)`（一个匿名函数）中返回值。函数可以返回多个值，所以`return`的语法是：

	stat ::= return [explist] [';']

`return`只能写在一个块的最后一行。如果必须在语句块中间返回，那么可以写一个显式的内部语句块，只在这个块里执行`return`，因为`return`是这个内部块的最后一行语句了。
