长度操作符
========

长度操作符用一元前缀操作符`#`表示。

字符串的长度是指它的字节数（当每个字符都是一个字节时字符串的长度）。

The length operator applied on a table returns a border in that table. A border in a table t is any natural number that satisfies the following condition:

     (border == 0 or t[border] ~= nil) and t[border + 1] == nil
     
In words, a border is any (natural) index in a table where a non-nil value is followed by a nil value (or zero, when index 1 is nil).

A table with exactly one border is called a sequence. For instance, the table {10, 20, 30, 40, 50} is a sequence, as it has only one border (5). The table {10, 20, 30, nil, 50} has two borders (3 and 5), and therefore it is not a sequence. The table {nil, 20, 30, nil, nil, 60, nil} has three borders (0, 3, and 6), so it is not a sequence, too. The table {} is a sequence with border 0. Note that non-natural keys do not interfere with whether a table is a sequence.

When t is a sequence, #t returns its only border, which corresponds to the intuitive notion of the length of the sequence. When t is not a sequence, #t can return any of its borders. (The exact one depends on details of the internal representation of the table, which in turn can depend on how the table was populated and the memory addresses of its non-numeric keys.)

The computation of the length of a table has a guaranteed worst time of O(log n), where n is the largest natural key in the table.

A program can modify the behavior of the length operator for any value but strings through the __len metamethod (see §2.4).
