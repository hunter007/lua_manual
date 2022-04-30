语句块
======

一个语句块就是一些语句，它们按顺序执行：

	block ::= {stat}

Lua支持空语句，用分号分割多行语句，开始一个新的语句块，或者连续写两个分号：

	stat ::= ‘;’

Function calls and assignments can start with an open parenthesis. This possibility leads to an ambiguity in Lua's grammar. Consider the following fragment:
函数调用和赋值

```lua
     a = b + c
     (print or io.write)('done')
```

可以用两种方式看待这种语法：

```lua
     a = b + c(print or io.write)('done')

     a = b + c; (print or io.write)('done')
```

The current parser always sees such constructions in the first way, interpreting the open parenthesis as the start of the arguments to a call. To avoid this ambiguity, it is a good practice to always precede with a semicolon statements that start with a parenthesis:

     ;(print or io.write)('done')

A block can be explicitly delimited to produce a single statement:
语句块可以显示地用一个语句结束

	stat ::= do block end

Explicit blocks are useful to control the scope of variable declarations. Explicit blocks are also sometimes used to add a return statement in the middle of another block (see §3.3.4).

显式的语句块是有用的：控制变量的作用域范围。 有时候，显式的语句块还可以用来在另一个语句块中间添加返回语句([§3.3.4](./3_3_4_control_structures.md))
