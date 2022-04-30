表达式
===========

Lua中的基本表达式如下：

	exp ::= prefixexp
	exp ::= nil | false | true
	exp ::= Numeral
	exp ::= LiteralString
	exp ::= functiondef
	exp ::= tableconstructor
	exp ::= '...'
	exp ::= exp binop exp
	exp ::= unop exp
	prefixexp ::= var | functioncall | '(' exp ')'

数字和字符串字面量在[§3.1](../3_1_lexical_conventions.md)描述。变量在[§3.2](../3_2_variables.md)中描述。函数定义在[§3.4.11](./3_4_11_function_definitions.md)中解释。函数调用在[§3.4.10](./3_4_10_function_calls.md)中说明。表构造器在[§3.4.9](./3_4_9_table_constructors.md)中描述。Vararg expressions(三个点号"...")仅能直接用在vararg function中，这在[§3.4.11](./3_4_11_function_definitions.md)描述。

二元操作符包括算术操作符([§3.4.1](./3_4_1_arithmetic_operators.md))，位操作符([§3.4.2](./3_4_2_bitwise_operators.md))，关系操作符([§3.4.4](3_4_4_relational_operators.md))，逻辑操作符([§3.4.5](3_4_5_logical_operators.md))，关联操作符([§3.4.6](3_4_6_concatenation.md))。
一元(Unary)操作符包括负号([§3.4.1](./3_4_1_arithmetic_operators.md))，按位取反([§3.4.2](./3_4_2_bitwise_operators.md))，逻辑取反([§3.4.5](3_4_5_logical_operators.md))和长度操作符([§3.4.7](3_4_7_the_length_operator.md))。

Both function calls and vararg expressions can result in multiple values. If a function call is used as a statement (see §3.3.6), then its return list is adjusted to zero elements, thus discarding all returned values. If an expression is used as the last (or the only) element of a list of expressions, then no adjustment is made (unless the expression is enclosed in parentheses). In all other contexts, Lua adjusts the result list to one element, either discarding all values except the first one or adding a single nil if there are no values.

函数调用和 vararg expressions 都可以产生多个值。如果函数调用被当做语句（[§3.3.6](../3_3_statements/3_3_6_function_calls_as_statements.md)），它的返回值列表将被视为0个元素，因此丢弃所有返回值。

以下是一些例子：

     f()                -- 被视为0个返回值。
     g(f(), x)          -- f() 被视为1个返回值
     g(x, f())          -- f()的所有返回值传入g中
     a,b,c = f(), x     -- f() 被视为1个返回值，c被初始化为nil
     a,b = ...          -- a gets the first vararg argument, b gets
                        -- the second (both a and b can get nil if there
                        -- is no corresponding vararg argument)

     a,b,c = x, f()     -- f() is adjusted to 2 results
     a,b,c = f()        -- f() is adjusted to 3 results
     return f()         -- returns all results from f()
     return ...         -- returns all received vararg arguments
     return x,y,f()     -- returns x, y, and all results from f()
     {f()}              -- creates a list with all results from f()
     {...}              -- creates a list with all vararg arguments
     {f(), nil}         -- f() is adjusted to 1 result
     
Any expression enclosed in parentheses always results in only one value. Thus, (f(x,y,z)) is always a single value, even if f returns several values. (The value of (f(x,y,z)) is the first value returned by f or nil if f does not return any values.)
