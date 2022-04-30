辅助库
======

*辅助库*提供了几个方便使用的函数。基本API已经提供了C和Lua交互的原子函数，辅助库为一些常见任务提供了高层函数。

辅助库中的所有函数和类型定义在头文件`lauxlib.h`中，以`luaL_`为前缀。

辅助库中的所有函数都简历在基本API之上。所以辅助库中的函数都可以由基本API中的函数替代，辅助库函数的使用，使得我们代码更加便捷。

Several functions in the auxiliary library use internally some extra stack slots. When a function in the auxiliary library uses less than five slots, it does not check the stack size; it simply assumes that there are enough slots.


辅助库中的几个函数用来检查C函数参数。由于错误消息被参数格式化（如"bad argument #1"），我们不应该在其它栈值上使用它们。

以`luaL_check*`开始的函数的调用，如果检查失败，总是抛出错误。
