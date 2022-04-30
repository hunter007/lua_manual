# 简介

![Lua logo](./images/lua_logo.gif)

Lua 是一个强大的、高效的、轻量级、可嵌入的脚本语言，支持过程式编程、面向对象编程、函数式编程、数据驱动编程和数据描述。

Lua 将简单的过程式语法和基于关联数组和可扩展语义的强大的数据描述结构结合到一起。Lua 是动态类型的，通过字节码运行于一个基于注册的虚拟机上，拥有增加的垃圾收集的自动内存管理，在配置、脚本和快速原型方面是理想的语言。


#### LuaRocks ####

![LuaRocks logo](./images/luarocks_logo.svg)

`LuaRocks`是 Lua 的包管理器。LuaRocks 支持创建和安装 Lua 模块（一个自包含的包，叫做 rocks）。It allows you to create and install Lua modules as self-contained packages called rocks. You can download and install LuaRocks on Unix and Windows. Get started

##### 安装 #####

```shell
$ wget https://luarocks.org/releases/luarocks-3.0.4.tar.gz
$ tar zxpf luarocks-3.0.4.tar.gz
$ cd luarocks-3.0.4
$ ./configure; sudo make bootstrap
$ sudo luarocks install luasocket
$ lua
Lua 5.3.5 Copyright (C) 1994-2018 Lua.org, PUC-Rio
> require "socket"
```


#### Awesome Lua ####

在 github 上维护以一分优质资源列表：

[https://github.com/LewisJEllis/awesome-lua](https://github.com/LewisJEllis/awesome-lua/blob/master/readme.md)
