注册
====

Lua提供了一个注册表——一个预定义的表，可被任何C代码用来存储Lua值。这个注册表总是可以用`LUA_REGISTRYINDEX`来索引定位。任何C库都可以在这个表中存储数据，但是必须小心地选择key，以避免和其他库产生冲突。一般来说，可以用包含库名的字符串作为key，or a light userdata with the address of a C object in your code，或者你代码产生的Lua对象。至于变量名，由一个下划线，跟着大写字母的key，是为Lua保留的。

注册表中的整数key用于引用机制(`luaL_ref`)，和一些预定义的值，因此，整数key不能用于其他目的。

当我们创建了一个新的Lua State时，它的注册表会带有一些预定义的值。这些预定义的值可以通过整数key索引，而key定义在`lua.h`中。现在定义了以下常量：

- **LUA_RIDX_MAINTHREAD**: 这个索引注册了这个state的主线程(就是和state一起创建的那个线程。)
- **LUA_RIDX_GLOBALS**: 这个索引注册了全局环境。
