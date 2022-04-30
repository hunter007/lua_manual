值和类型
=======

Lua is a dynamically typed language. This means that variables do not have types; only values do. There are no type definitions in the language. All values carry their own type.
Lua 是动态类型的语言。这意味着变量不需要指定类型，只有值是有类型的。语言中没有类型定义。所有值携带有它们的类型信息。

All values in Lua are first-class values. This means that all values can be stored in variables, passed as arguments to other functions, and returned as results.
Lua 中所有的值都是一类值。这意味着所有的值可以存储到变量中，作为参数传给函数，或者作为结果从函数中返回。

There are eight basic types in Lua: nil, boolean, number, string, function, userdata, thread, and table. The type nil has one single value, nil, whose main property is to be different from any other value; it usually represents the absence of a useful value. The type boolean has two values, false and true. Both nil and false make a condition false; any other value makes it true. The type number represents both integer numbers and real (floating-point) numbers. The type string represents immutable sequences of bytes. Lua is 8-bit clean: strings can contain any 8-bit value, including embedded zeros ('\0'). Lua is also encoding-agnostic; it makes no assumptions about the contents of a string.
Lua 有`8`种基本类型：`nil`, `boolean`, `number`, `string`, `function`, `userdata`, `thread`和`table`。类型`nil`只有一个值：`nil`，它通常表示缺少一个有意义的值。`boolean`有两个值：`false`和`true`.`nil`和`false`都表示`条件 false`，任何其他值都表示`条件为 true`。`number`代表整数和实数（浮点数）。`string`代表不可修改的字节序列。Lua is 8-bit clean：字符串可以包含任何 8bit 的值，包括字符'\0'。Lua 也是不区分编码的，Lua 不假设字符串的任何字符编码。

The type number uses two internal representations, or two subtypes, one called integer and the other called float. Lua has explicit rules about when each representation is used, but it also converts between them automatically as needed (see §3.4.3). Therefore, the programmer may choose to mostly ignore the difference between integers and floats or to assume complete control over the representation of each number. Standard Lua uses 64-bit integers and double-precision (64-bit) floats, but you can also compile Lua so that it uses 32-bit integers and/or single-precision (32-bit) floats. The option with 32 bits for both integers and floats is particularly attractive for small machines and embedded systems. (See macro LUA_32BITS in file luaconf.h.)
`number`有两种内部表示，或者叫两种子类型，一个是 `integer`，一个是`float`。关于什么时候用哪种表示，Lua 有明确的规则，但也可以在需要的时候，自动地进行相互转换（参考[§3.4.3](../ch3/3_4_expressions/3_4_3_coercions_and_conversions.md)）Therefore, the programmer may choose to mostly ignore the difference between integers and floats or to assume complete control over the representation of each number.标准 Lua 使用64位正数和双精度(64位)浮点数，但我们也可以将 Lua 编译为使用 32位整数或单精度浮点数(32位)。这个开关对小机器和嵌入式系统相当有吸引力(luaconf.h 中有个宏：LUA_32BITS)


Lua can call (and manipulate) functions written in Lua and functions written in C (see §3.4.10). Both are represented by the type function.
Lua 可以调用/操作用Lua写的或者 C 写的函数([§3.4.10 函数调用](../ch3/3_4_expressions/3_4_10_function_calls.md))，两者都通过`类型函数(type function)`表示。

The type userdata is provided to allow arbitrary C data to be stored in Lua variables. A userdata value represents a block of raw memory. There are two kinds of userdata: full userdata, which is an object with a block of memory managed by Lua, and light userdata, which is simply a C pointer value. Userdata has no predefined operations in Lua, except assignment and identity test. By using metatables, the programmer can define operations for full userdata values (see §2.4). Userdata values cannot be created or modified in Lua, only through the C API. This guarantees the integrity of data owned by the host program.
Lua 提供`userdata`用来将任意的 C 数据存储到 Lua 变量中。`userdata`表示一个原始内存块。有两种 `userdata`: `full userdata`代表一个由 Lua 管理的内存块的对象，`leght userdata`表示一个 C 指针值。Lua没有为userdata预定义什么操作，只能通过 C API。这保证了主程序对数据的完整所有。

The type thread represents independent threads of execution and it is used to implement coroutines (see §2.6). Lua threads are not related to operating-system threads. Lua supports coroutines on all systems, even those that do not support threads natively.
`thread`代表独立的执行过程，被用来实现协程`coroutine`([§2.6](./2_6_coroutines.md)) Lua 线程和操作系统线程没有关联。Lua 在所有操作系统中支持协程，甚至那些原生就不支持线程的操作系统上。

The type table implements associative arrays, that is, arrays that can have as indices not only numbers, but any Lua value except nil and NaN. (Not a Number is a special value used to represent undefined or unrepresentable numerical results, such as 0/0.) Tables can be heterogeneous; that is, they can contain values of all types (except nil). Any key with value nil is not considered part of the table. Conversely, any key that is not part of a table has an associated value nil.
`table`实现了关联数组，就是说，数组不仅支持数字类型的索引，除了`nil`和`NaN`，任何 Lua 值都可以作为数组的索引.(NaN:Not a Number 是一个特殊值，表示未定义或者不可表示的数字结果，如0/0)。`table`可以是各种，也就是说，它可以包含各种类型的值，除了 nil。任何值为 nil 的 key 不被认为是 table 的一部分。反过来说，任何不是`table`一部分的 key 都关联了 nil。

Tables are the sole data-structuring mechanism in Lua; they can be used to represent ordinary arrays, lists, symbol tables, sets, records, graphs, trees, etc. To represent records, Lua uses the field name as an index. The language supports this representation by providing a.name as syntactic sugar for a["name"]. There are several convenient ways to create tables in Lua (see §3.4.9).
`table`是Lua 中唯一的数据结构机制。`table`可以用来表示普通数组、列表、符号表、集合、记录、图、树等等。要表示记录(record)，Lua 使用字段名作为索引。语言为了支持这个表示，提供了一个语法糖：用`a.name`表示`a["name"]`。有几种方法创建`table`([§3.4.9](../ch3/3_4_expressions/3_4_9_table_constructors.md))

Like indices, the values of table fields can be of any type. In particular, because functions are first-class values, table fields can contain functions. Thus tables can also carry methods (see §3.4.11).
和`table`的索引一样，表字段的值可以是任何类型。有一点特殊的，就是函数，Lua 中函数是一类值，所以表字段可以包含函数，因此表也可以携带方法([§3.4.11](../ch3/3_4_expressions/3_4_11_function_definitions.md))

The indexing of tables follows the definition of raw equality in the language. The expressions a[i] and a[j] denote the same table element if and only if i and j are raw equal (that is, equal without metamethods). In particular, floats with integral values are equal to their respective integers (e.g., 1.0 == 1). To avoid ambiguities, any float with integral value used as a key is converted to its respective integer. For instance, if you write a[2.0] = true, the actual key inserted into the table will be the integer 2. (On the other hand, 2 and "2" are different Lua values and therefore denote different table entries.)

The indexing of tables follows the definition of raw equality in the language。当且仅当 i 和 j 是原始相等的（没有metamethods的相等），表达式`a[i]`和`a[j]`代表相同的表元素。

Tables, functions, threads, and (full) userdata values are objects: variables do not actually contain these values, only references to them. Assignment, parameter passing, and function returns always manipulate references to such values; these operations do not imply any kind of copy.

`table`, `function`, `thread`和(full) `userdata`都是对象：实际上变量不包含这个值，仅仅引用他们。赋值、参数传递、函数返回，总是操作值的引用；这些操作不表示任何拷贝。

The library function type returns a string describing the type of a given value (see §6.1).

库函数`type`返回一个字符串，这个字符串表述了给定值的类型([§6.1](../ch6/6_1_basic_functions.md))
