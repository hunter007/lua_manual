Lua is a powerful, efficient, lightweight, embeddable scripting language. It supports procedural programming, object-oriented programming, functional programming, data-driven programming, and data description.
Lua 是一种强大的、高效的、轻量级的、可嵌入的脚本语言，支持过程式编程、面向对象编程、函数式编程、数据驱动式编程和数据描述。

Lua combines simple procedural syntax with powerful data description constructs based on associative arrays and extensible semantics. Lua is dynamically typed, runs by interpreting bytecode with a register-based virtual machine, and has automatic memory management with incremental garbage collection, making it ideal for configuration, scripting, and rapid prototyping.


Lua is implemented as a library, written in clean C, the common subset of Standard C and C++. The Lua distribution includes a host program called lua, which uses the Lua library to offer a complete, standalone Lua interpreter, for interactive or batch use. Lua is intended to be used both as a powerful, lightweight, embeddable scripting language for any program that needs one, and as a powerful but lightweight and efficient stand-alone language.

As an extension language, Lua has no notion of a "main" program: it works embedded in a host client, called the embedding program or simply the host. (Frequently, this host is the stand-alone lua program.) The host program can invoke functions to execute a piece of Lua code, can write and read Lua variables, and can register C functions to be called by Lua code. Through the use of C functions, Lua can be augmented to cope with a wide range of different domains, thus creating customized programming languages sharing a syntactical framework.

Lua is free software, and is provided as usual with no guarantees, as stated in its license. The implementation described in this manual is available at Lua's official web site, www.lua.org.
Lua 是一个自由软件，无需授权。本手册描述的实现在官网 www.lua.org 上可以找到。

Like any other reference manual, this document is dry in places. For a discussion of the decisions behind the design of Lua, see the technical papers available at Lua's web site. For a detailed introduction to programming in Lua, see Roberto's book, Programming in Lua.和其他参考手册一样，这个文档仅仅是文档。如果要讨论 lua 底层的设计，可以到 Lua 的官网查看技术论文。详细介绍 Lua 编程，可以查看 Roberto写的书:《Programming in Lua》。
