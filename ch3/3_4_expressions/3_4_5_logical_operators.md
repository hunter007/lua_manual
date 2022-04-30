逻辑操作符
========

Lua中的逻辑操作符是`and`、`or`和`not`，和Python一模一样。和控制结构一样([§3.3.4](../3_3_statements/3_3_4_control_structures.md))。所有逻辑操作符视`false`和`nil`为false，视其他值为true。


The negation operator not always returns false or true. The conjunction operator and returns its first argument if this value is false or nil; otherwise, and returns its second argument. The disjunction operator or returns its first argument if this value is different from nil and false; otherwise, or returns its second argument. Both and and or use short-circuit evaluation; that is, the second operand is evaluated only if necessary. Here are some examples:

```lua
     10 or 20            --> 10
     10 or error()       --> 10
     nil or "a"          --> "a"
     nil and 10          --> nil
     false and error()   --> false
     false and nil       --> false
     false or nil        --> nil
     10 and 20           --> 20
```

(在本手册中，`-->`表示表达式执行后的结果)
