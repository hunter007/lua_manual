赋值
====

Lua支持多重赋值，因此赋值语法可以在左侧定义多个变量，在右侧定义多个表达式。两边用逗号分隔：
```lua
	stat ::= varlist '=' explist
	varlist ::= var {',' var}
	explist ::= exp {',' exp}
```
表达式在 [§3.4](../3_4_expressions/README.md) 讨论。


赋值之前，值列表会适配变量列表的长度。如果比需要的多，多余的值将被丢弃。如果比需要的值少，会用`nil`补充。如果表达式列表有函数调用结束，那么这个函数调用返回的所有值都会放入值列表，在适配两边之前。(except when the call is enclosed in parentheses; see §3.4).

赋值语句先执行所有的表达式，然后进行赋值，因此以下代码

```lua
     i = 3
     i, a[i] = i+1, 20
```

将赋值`a[3]`为20，不影响`a[4]`，因为`a[i]`中的`i`在赋值之前计算（也就是3）。类似地，以下代码：

```lua
     x, y = y, x
```

交换 `x` 和 `y` 的值

```lua
     x, y, z = y, z, x
```

顺序交换`x`、`y`和`z`的值。

为全局变量赋值`x = val` 等于为`_ENV.x = val` ((§2.2)[../../ch2/2_2_environments_and_the_global_environment.md])

为表字段和全局变量(实际上也是表字段)赋值，可以通过`元表`改变（[§2.4](../../ch2/2_4_metatables_and_metamethods.md)）
