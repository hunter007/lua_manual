栈
====

Lua使用虚拟栈向C传递值或者从C取回值。这个栈中的每个元素代表着一个Lua的值(nil, 数字，字符串等)。API中的函数可以通过他们收到的Lua状态参数可以访问到这个栈。


无论何时Lua调用C，被调用函数会收到一个新栈，这个栈独立于之前的栈，也独立于仍旧活跃的C函数的栈。开始时，这个栈包含传递给C函数的所有参数，这是C函数可以存储临时的Lua值得地方，必须把要返回的值push给调用者(lua_CFunction)


For convenience, most query operations in the API do not follow a strict stack discipline. Instead, they can refer to any element in the stack by using an index: A positive index represents an absolute stack position (starting at 1); a negative index represents an offset relative to the top of the stack. More specifically, if the stack has n elements, then index 1 represents the first element (that is, the element that was pushed onto the stack first) and index n represents the last element; index -1 also represents the last element (that is, the element at the top) and index -n represents the first element.
