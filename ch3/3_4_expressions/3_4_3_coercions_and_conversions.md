类型转换
======

Lua提供了运行时的一些类型和表示的自动转换。位操作总是将浮点数转换成整数。求幂和浮点除法总是转换整数为浮点数。其他算术操作在整数和浮点数计算时，总是转换整数为浮点数，这些事一般规则。C API在需要的时候也会把整数转换成浮点数，或者反过来。另外，字符串连接也接收数字作为参数。

在需要数字的地方，Lua也会将字符串转成数字。

In a conversion from integer to float, if the integer value has an exact representation as a float, that is the result. Otherwise, the conversion gets the nearest higher or the nearest lower representable value. This kind of conversion never fails.

将整数转成浮点数的时候，如果整数值可以用精确的浮点数表示，那就是最后结果。

浮点数转成整数的时候，会检查浮点数是不是可以由足够的整数表示(浮点数由整数部分，并且在合法范围中)。如果检查成功，这个表示就是最后结果，否则，转换失败。

The conversion from strings to numbers goes as follows: First, the string is converted to an integer or a float, following its syntax and the rules of the Lua lexer. (The string may have also leading and trailing spaces and a sign.) Then, the resulting number (float or integer) is converted to the type (float or integer) required by the context (e.g., the operation that forced the conversion).

All conversions from strings to numbers accept both a dot and the current locale mark as the radix character. (The Lua lexer, however, accepts only a dot.)

The conversion from numbers to strings uses a non-specified human-readable format. For complete control over how numbers are converted to strings, use the format function from the string library (see string.format).
