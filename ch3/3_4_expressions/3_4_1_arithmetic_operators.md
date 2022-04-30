算术操作符
========

Lua支持以下算术操作符：

`+`: 加法

`-`: 减法

`*`: 乘法

`/`: 浮点除法

`//`: 整除

`%`: 取模

`^`: 求幂

`-`: 负号

除了求幂和浮点除法，算术操作符按以下方式工作：如果两边都是整数，操作按整数计算，结果也是整数。否则，如果两边都是数字或字符串(可以转换成数字)，它们将被视为浮点数，操作符按IEEE754标准执行，结果也是浮点数。

求幂和浮点除法总是将操作数转成浮点数，结果也总是浮点数。求幂使用 ISO C函数`pow`，所以对非整数操作数也是支持的。

整除 `//` 结果朝着负无限大取整，也就是地板除法。

取模定义为除法的余数，朝着负无限大取整。

In case of overflows in integer arithmetic, all operations wrap around, according to the usual rules of two-complement arithmetic. (In other words, they return the unique representable integer that is equal modulo 264 to the mathematical result.)
