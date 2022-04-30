与之前版本的兼容性
===============

这里我们列出了Lua版本从5.2迁移到5.3使存在的不兼容。我们可以通过编译选项避免一些不兼容(查看`luaconf.h`)。然而，所有这些兼容选项在未来都会被移除。

Lua versions can always change the C API in ways that do not imply source-code changes in a program, such as the numeric values for constants or the implementation of functions as macros. Therefore, you should not assume that binaries are compatible between different Lua versions. Always recompile clients of the Lua API when using a new version.

类似地，Lua版本总是改变预编译chunk的内部表示；所以预编译的chunk不能兼容不同的Lua版本。

官方分发版的标准库也可能在不同版本之间改变。
