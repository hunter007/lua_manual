函数调用
=======

Lua中函数调用的语法：

	functioncall ::= prefixexp args

在函数调用中，首先执行`prefixexp`和`args`。如果`prefixexp`是一个函数类型，才调用这个函数。否则，调用元方法`__call`，`prefixexp`作为第一个参数传入元方法，`args`作为后续参数传入（[§2.4](../../ch2/2_4_metatables_and_metamethods.md)）。


这个形式：

	functioncall ::= prefixexp ‘:’ Name args

用来调用方法。`v:name(args)` 是 `v.name(v,args)`的语法糖, 但`v`仅传入一次。

参数有多种形式：

	args ::= ‘(’ [explist] ‘)’
	args ::= tableconstructor
	args ::= LiteralString

所有参数在执行函数调用之前被执行。`f{fields}`的调用形式也是`f({fields})`的语法糖。就是说，参数列表是一个新表。`f'string'`或者`f"string"`或者`f[[string]]`是`f('string')`的语法糖，就是说，参数列表是单个自卖能量字符串。

A call of the form return functioncall is called a tail call. Lua implements proper tail calls (or proper tail recursion): in a tail call, the called function reuses the stack entry of the calling function. Therefore, there is no limit on the number of nested tail calls that a program can execute. However, a tail call erases any debug information about the calling function. Note that a tail call only happens with a particular syntax, where the return has one single function call as argument; this syntax makes the calling function return exactly the returns of the called function. So, none of the following examples are tail calls:

```lua
     return (f(x))        -- results adjusted to 1
     return 2 * f(x)
     return x, f(x)       -- additional results
     f(x); return         -- results discarded
     return x or f(x)     -- results adjusted to 1
```