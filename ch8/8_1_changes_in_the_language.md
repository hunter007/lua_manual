语言的改变
==========

- 5.2和5.3的主要变化是：数字的整数子类型的引入。尽管这个变化不影响正常的计算，有些计算还是会得到不同的结果（主要是某种计算溢出）。
- The main difference between Lua 5.2 and Lua 5.3 is the introduction of an integer subtype for numbers. Although this change should not affect "normal" computations, some computations (mainly those that involve some kind of overflow) can give different results.
You can fix these differences by forcing a number to be a float (in Lua 5.2 all numbers were float), in particular writing constants with an ending .0 or using x = x + 0.0 to convert a variable. (This recommendation is only for a quick fix for an occasional incompatibility; it is not a general guideline for good programming. For good programming, use floats where you need floats and integers where you need integers.)

- The conversion of a float to a string now adds a .0 suffix to the result if it looks like an integer. (For instance, the float 2.0 will be printed as 2.0, not as 2.) You should always use an explicit format when you need a specific format for numbers.
(Formally this is not an incompatibility, because Lua does not specify how numbers are formatted as strings, but some programs assumed a specific format.)

- The generational mode for the garbage collector was removed. (It was an experimental feature in Lua 5.2.)
