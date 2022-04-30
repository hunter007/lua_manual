字符串连接
========

Lua中字符串连接操作用两个点号表示：`..`。如果两个操作数是字符串或者数字，它们被转换成字符串，根据[§3.4.3](./3_4_3_coercions_and_conversions.md)描述的规则。否则，调用`__concat`元方法([§2.4](../../ch2/2_4_metatables_and_metamethods.md))
