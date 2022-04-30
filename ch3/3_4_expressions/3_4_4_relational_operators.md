关系操作符
========

Lua支持的关系操作符：

`==`: 相等

`~=`: 不等

`<`: 小于

`>`: 大于

`<=`: 小于等于

`>=`: 大于等于

这些操作符的结果总是`false`或者`true`。

相等(==)先比较操作数的类型。如果两个操作数的类型不同，那结果就是`false`；否则，比较两个操作数。字符串按字典的顺序比较。如果表示相等的数值，两个数字就是相等的。

表、userdata、线程通过引用比较：只有它们是同一个对象，两个对象才认为是相等的。每当我们创建新对象(表、userdata、线程)，这个新对象和之前的所有对象都不相等。闭包总是和它自己相等。Closures with any detectable difference (different behavior, different definition) are always different. Closures created at different times but with no detectable differences may be classified as equal or not (depending on internal caching details).

You can change the way that Lua compares tables and userdata by using the "eq" metamethod (see §2.4).

想扥拿给你比较不会转换字符串为数字，或者数字转换成字符串。因此，`"0"==0`结果是false，`t[0]`和`t["0"]`在表中表示不同的值。

操作符`~=`是`==`的反操作。

The order operators work as follows. If both arguments are numbers, then they are compared according to their mathematical values (regardless of their subtypes). Otherwise, if both arguments are strings, then their values are compared according to the current locale. Otherwise, Lua tries to call the "lt" or the "le" metamethod (see §2.4). A comparison a > b is translated to b < a and a >= b is translated to b <= a.

Following the IEEE 754 standard, NaN is considered neither smaller than, nor equal to, nor greater than any value (including itself).
