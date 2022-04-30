环境和全局环境
============

As will be discussed in §3.2 and §3.3.3, any reference to a free name (that is, a name not bound to any declaration) var is syntactically translated to _ENV.var. Moreover, every chunk is compiled in the scope of an external local variable named _ENV (see §3.3.2), so _ENV itself is never a free name in a chunk.

就像将在 §3.2 和 §3.3.3中讨论的，对一个自由名称(没有绑定任何声明的名称) `var` 的引用，在句法上，将被解释成 `_ENV.var`。进一步，每个代码块都编译到一个外部本地变量的作用域中，叫做`_ENV`((§3.3.2)[./../ch3/3_3_statements/3_3_2_chunks.md])，所以`_ENV`自身绝不是一个自由变量。

Despite the existence of this external _ENV variable and the translation of free names, _ENV is a completely regular name. In particular, you can define new variables and parameters with that name. Each reference to a free name uses the _ENV that is visible at that point in the program, following the usual visibility rules of Lua (see §3.5).

Any table used as the value of _ENV is called an environment.

任何用作`_ENV`的值的表，又叫做`一个环境`。

Lua keeps a distinguished environment called the global environment. This value is kept at a special index in the C registry (see §4.5). In Lua, the global variable _G is initialized with this same value. (_G is never used internally.)

Lua 保持着重要的环境，叫做`全局环境`。这个值在 C registry([§4.5]())中有个特定索引。Lua 中，全局变量`_G`用这个值初始化。(`_G`绝不会在内部使用)

When Lua loads a chunk, the default value for its _ENV upvalue is the global environment (see load). Therefore, by default, free names in Lua code refer to entries in the global environment (and, therefore, they are also called global variables). Moreover, all standard libraries are loaded in the global environment and some functions there operate on that environment. You can use load (or loadfile) to load a chunk with a different environment. (In C, you have to load the chunk and then change the value of its first upvalue.)

当 Lua 加载一个代码块，`_ENV`的默认值就是这个全局环境(查看 Load)。因此，默认情况下，Lua 代码中的自由名字引用全局环境中的实体（因此，他们也叫全局变量）。更进一步，所有标准库被加载到全局环境，在全局环境中执行的函数。我们可以用`load（或者 loadfile）`加载一个块到不同的环境中。(In C, you have to load the chunk and then change the value of its first upvalue.)
