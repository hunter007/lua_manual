C闭包
=====

创建C函数时，可以给它关联一些值。也就是，创建了一个C闭包(lua_pushcclosure)。这些值被叫做`upvalue`，当调用这个函数时，函数可以访问它们。


Whenever a C function is called, its upvalues are located at specific pseudo-indices. These pseudo-indices are produced by the macro lua_upvalueindex. The first upvalue associated with a function is at index lua_upvalueindex(1), and so on. Any access to lua_upvalueindex(n), where n is greater than the number of upvalues of the current function (but not greater than 256, which is one plus the maximum number of upvalues in a closure), produces an acceptable but invalid index.
