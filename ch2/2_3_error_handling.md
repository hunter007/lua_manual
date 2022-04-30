错误处理
======

Because Lua is an embedded extension language, all Lua actions start from C code in the host program calling a function from the Lua library. (When you use Lua standalone, the lua application is the host program.) Whenever an error occurs during the compilation or execution of a Lua chunk, control returns to the host, which can take appropriate measures (such as printing an error message).

因为 Lua 是一个嵌入式的可扩展语言，主程序调用一个 Lua 库中的函数，所有 Lua 动作将从 C 代码开始。(当独立使用 Lua 时，Lua 应用就是主程序)。当 在Lua 块的编译或者执行时发生错误，控制权会返回给主程序，主程序将采取相应的措施（如打印错误信息）。

Lua code can explicitly generate an error by calling the error function. If you need to catch errors in Lua, you can use pcall or xpcall to call a given function in protected mode.
通过调用错误函数，Lua 代码可以显式产生一个错误。如果需要在 Lua 中捕捉错误，可以使用`pcall`或者`xpcall`在保护模式下调用某个函数。

Whenever there is an error, an error object (also called an error message) is propagated with information about the error. Lua itself only generates errors whose error object is a string, but programs may generate errors with any value as the error object. It is up to the Lua program or its host to handle such error objects.

不论何时出现一个错误，错误对象（也叫错误消息）被冒泡出来。Lua 本身只会生成字符串表示的错误对象，但是程序可以生成任何值表示的错误对象。这取决于是 Lua 程序还是宿主程序来处理错误对象。

When you use xpcall or lua_pcall, you may give a message handler to be called in case of errors. This function is called with the original error object and returns a new error object. It is called before the error unwinds the stack, so that it can gather more information about the error, for instance by inspecting the stack and creating a stack traceback. This message handler is still protected by the protected call; so, an error inside the message handler will call the message handler again. If this loop goes on for too long, Lua breaks it and returns an appropriate message. (The message handler is called only for regular runtime errors. It is not called for memory-allocation errors nor for errors while running finalizers.)

当使用`xpcall`或者`lua_pcall`时，可以指定一个消息 Handler，这个 handler 在出现错误时被调用。这个函数被调用时，会传入原始错误对象，并返回一个新的错误对象。
