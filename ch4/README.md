API应用程序编程接口
=================

本节描述Lua的C API。即是说，一个宿主程序可以和Lua交互的C函数集。所有的API函数和相关的类型、常量都声明在头文件`lua.h`中。

即使我们使用术语`function`，API中的某个实体也可以以macro的形式提供。
Even when we use the term "function", any facility in the API may be provided as a macro instead. Except where stated otherwise, all such macros use each of their arguments exactly once (except for the first argument, which is always a Lua state), and so do not generate any hidden side-effects.

在大部分C 库中，Lua API函数不检查他们参数的有效性和一致性。然而，我们可以通过在编译Lua时使用宏`LUA_USE_APICHECK`，来改变这一行为。


Lua库完全是可重入的：它没有全局变量。它在一个动态结构中保存需要的信息，叫做 `Lua state`。

每一个Lua state都有一个或者多个线程
Each Lua state has one or more threads, which correspond to independent, cooperative lines of execution. The type lua_State (despite its name) refers to a thread. (Indirectly, through the thread, it also refers to the Lua state associated to the thread.)

必须给库中每个函数传递一个指向线程的指针（第一个参数），除了`lua_nrestate`，它创建一个Lua state，并返回指向新的state的主线程的指针。
