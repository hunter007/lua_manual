函数定义
=======

函数定义的语法是：

	functiondef ::= function funcbody
	funcbody ::= '(' [parlist] ')' block end

也可以简化成以下形式：

	stat ::= function funcname funcbody
	stat ::= local function Name funcbody
	funcname ::= Name {'.' Name} [':' Name]

语句

     function f () body end

也等效于：

     f = function () body end

语句

     function t.a.b.c.f () body end

也等效于：

     t.a.b.c.f = function () body end

语句

     local function f () body end

也等效于：

     local f; f = function () body end

但不等效于：

     local f = function () body end

(This only makes a difference when the body of the function contains references to f.)

函数定义就是一个可执行的表达式，它的值是幻术类型。当Lua预编译一个chunk时，所有函数体也会预编译。因此，无论何时，Lua执行一个函数定义，就会简历这个函数，函数实例(或者是闭包)就是这个表达式的最终值。

Parameters act as local variables that are initialized with the argument values:

	parlist ::= namelist [‘,’ ‘...’] | ‘...’

调用函数的时候，实参列表列表会和形参列表长度进行匹配，除非这个函数是可变参数函数（最后一个参数是`...`）。
When a function is called, the list of arguments is adjusted to the length of the list of parameters, unless the function is a vararg function, which is indicated by three dots ('...') at the end of its parameter list. A vararg function does not adjust its argument list; instead, it collects all extra arguments and supplies them to the function through a vararg expression, which is also written as three dots. The value of this expression is a list of all actual extra arguments, similar to a function with multiple results. If a vararg expression is used inside another expression or in the middle of a list of expressions, then its return list is adjusted to one element. If the expression is used as the last element of a list of expressions, then no adjustment is made (unless that last expression is enclosed in parentheses).

作为例子，考虑以下定义：

```lua
     function f(a, b) end
     function g(a, b, ...) end
     function r() return 1,2,3 end
```

Then, we have the following mapping from arguments to parameters and to the vararg expression:

     CALL            PARAMETERS

     f(3)             a=3, b=nil
     f(3, 4)          a=3, b=4
     f(3, 4, 5)       a=3, b=4
     f(r(), 10)       a=1, b=10
     f(r())           a=1, b=2

     g(3)             a=3, b=nil, ... -->  (nothing)
     g(3, 4)          a=3, b=4,   ... -->  (nothing)
     g(3, 4, 5, 8)    a=3, b=4,   ... -->  5  8
     g(5, r())        a=5, b=1,   ... -->  2  3

用`return`语句返回结果([§3.3.4](../3_3_statements/3_3_4_control_structures.md))。如果到达函数的结尾，没有发现`return`语句，那么这个函数不返回结果。


一个函数可以返回的值得个数与依赖的系统有关。这个限制可以超过1000。

冒号语法用来定义一个方法，也就是说，函数会收到一个隐式的`self`参数：

```lua
     function t.a.b.c:f (params) body end
```

是以下代码的语法糖：

```lua
     t.a.b.c.f = function (self, params) body end
```