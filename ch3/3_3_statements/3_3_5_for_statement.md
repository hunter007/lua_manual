for 语句
=======

`for`语句有两种形式：数字形式和Ibanez形式。

数字形式的循环重复代码块当控制变量通过一个算术表达式更新之后，形式如下：

	stat ::= for Name '=' exp ',' exp [',' exp] do block end

The block is repeated for name starting at the value of the first exp, until it passes the second exp by steps of the third exp. More precisely, a for statement like

     for v = e1, e2, e3 do block end

等效于：

     do
       local var, limit, step = tonumber(e1), tonumber(e2), tonumber(e3)
       if not (var and limit and step) then error() end
       var = var - step
       while true do
         var = var + step
         if (step >= 0 and var > limit) or (step < 0 and var < limit) then
           break
         end
         local v = var
         block
       end
     end
     
Note the following:

All three control expressions are evaluated only once, before the loop starts. They must all result in numbers.
var, limit, and step are invisible variables. The names shown here are for explanatory purposes only.
If the third expression (the step) is absent, then a step of 1 is used.
You can use break and goto to exit a for loop.
The loop variable v is local to the loop body. If you need its value after the loop, assign it to another variable before exiting the loop.
The generic for statement works over functions, called iterators. On each iteration, the iterator function is called to produce a new value, stopping when this new value is nil. The generic for loop has the following syntax:

	stat ::= for namelist in explist do block end
	namelist ::= Name {‘,’ Name}
A for statement like

     for var_1, ···, var_n in explist do block end
is equivalent to the code:

     do
       local f, s, var = explist
       while true do
         local var_1, ···, var_n = f(s, var)
         if var_1 == nil then break end
         var = var_1
         block
       end
     end
Note the following:

explist is evaluated only once. Its results are an iterator function, a state, and an initial value for the first iterator variable.
f, s, and var are invisible variables. The names are here for explanatory purposes only.
You can use break to exit a for loop.
The loop variables var_i are local to the loop; you cannot use their values after the for ends. If you need these values, then assign them to other variables before breaking or exiting the loop.
