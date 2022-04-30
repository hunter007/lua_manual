独立运行
=======
尽管Lua被设计为一种可扩展的，可嵌入到C程序中的语言，它也经常被用于独立程序中。一个独立的Lua解释器，也叫`lua`。这个独立的解释器包含了所有标准库、调试库。用法如下：

```shell
    lua [选项] [script [args]]
```

选项包括：

- **`-e stat`**: 执行字符串 stat

- **`-l mod`**: "requires" mod并且将结果保存到全局的@mod

- **`-i`**: 运行脚本，然后进入交互模式。

- **`-v`**: 打印版本信息。

- **`-E`**: 忽略环境变量

- **`--`**: 停止梳理选项

- **`-`**: 从标准输入获得脚本，不处理选项。

处理完选项之后，lua运行给定的脚本。当没有参数运行时，lua相当于`lua -v -i`,从标准输入中获取脚本。

在运行之前，如果不使用`-E`，lua解释器会检查`LUA_INIT_5_3`（如果不存在则检查`LUA_INIT`）。如果变量内容形如`@filename`，lua就执行这个文件。否则，lua把内容当做字符串处理。然后执行参数指定的脚本，或进入交互式环境。

当使用`-E`选项时，lua解释器处理忽略`LUA_INIT`外，还忽略`LUA_PATH`和`LUA_CPATH`，用`luaconf.h`定义的路径值设置`package.path`和`package.cpath`。

除了`-i`和`-E`，所有选项按顺序处理。例如：

```shell
$ lua -e'a=1' -e 'print(a)' script.lua
```

首先将a设置为1，然后打印a，最后运行script.lua文件。

运行任何脚本之前，lua会先收集所有命令行参数，放入`arg`的全局表中。脚本名用`arg[0]`索引，脚本名之后的参数用1索引，以此类推。脚本名之前的任何参数用负索引，如：

```shell
$ lua -la b.lua t1 t2
```
`arg`表的内容如下：

```lua
arg = { [-2] = "lua", [-1] = "-la",
        [0] = "b.lua",
        [1] = "t1", [2] = "t2" }
```

如果没有文件名，解释器名称的索引是`0`，然后是其他参数。如：
```shell
$ lua -e "print(arg[1])"
```

will print "-e". If there is a script, the script is called with arguments arg[1], ···, arg[#arg]. (Like all chunks in Lua, the script is compiled as a vararg function.)

在交互模式中，Lua不断地打印提示符，等待输入。输入一行之后，lua首先将这一行进行解释。如果成功，就打印值。否则就把这一行当语句解释。如果语句未完成，解释器打印一个续行符，等待语句完成。

如果全局变量`_PROOMPT`是一个字符串，那么它的值就是提示符。类似地，`_PROOMPT2`就是语句未完成时的提示符。

在脚本中，如果遇见未保护的错误，解释器就输出到标准错误流中。如果错误不是一个字符串，但是存在元方法`__tostring`，解释器就调用这个方法，产生最终的消息。否则，解释器就转换这个错误对象为字符串，添加错误栈到它上面。

当正常完成的时候，解释器关闭主lua state(`lua_close`)。要避免这一步，脚本可以调用`os.exit`。

在unix系统中，要把lua当做脚本解释器，lua会略过以`#`开始的第一行。lua脚本第一行以`#!`开始，并加上`chmod +x`可执行的权限，如：

```shell
#!/usr/bin/env lua
```
