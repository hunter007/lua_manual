协程
====

Lua 支持 `coroutine`协程，也叫 `collaborative multithreading`。Lua中的协程代表一个独立的执行线程。然而，不像多线程系统中的线程，协程只能通过显式地调用yield函数，才能挂起一个协程。

通过调用 `coroutine.create` 来创建一个协程。唯一的参数就是一个函数：协程的主函数。`create`仅仅创建新协程并返回一个handle（thread类型的对象），并不启动这个协程。

通过调用`coroutine.resume`来执行一个协程。当第一次调用`coroutine.resume`时，传入`coroutine.create`返回的线程对象，协程开始执行它的主函数。传入`coroutine.resume`的其他参数，也会传入到主函数中。协程开始运行之后，它就一直运行，直到终止或者`yield`。

协程的终止有两种方式：正常情况下，它的主函数返回了（显式的或者隐式的，最后一条指令完成之后）；非正常情况下，产生了一个不受保护的错误。正常终止的情况下，`coroutine.resume`返回`true`和主函数返回的值。非正常情况下，`coroutine.resume`返回false和错误。

通过调用`coroutine.yield`将一个协程yield。当协程yield的时候，`coroutine.resume`会立即返回，即使yield发生在嵌套函数调用里面（也就是说，不是在主函数中，而是在主函数直接或间接调用的函数中发生了yield），这种情况下，`coroutine.resume`也返回true,同时和传入`coroutine.yield`的其他参数。下次我们resume同一个协程的时候，它继续从上次yield的地方开始执行，传入`coroutine.resume`中的参数，作为上次`coroutine.yield`的返回值。

和`coroutine.create`类似，`coroutine.wrap`也创建一个协程，但是它返回一个函数。当调用这个函数的时候，开始执行这个协程。传递给这个函数的参数，也传递给`coroutine.resume`。`coroutine.wrap`返回所有`coroutine.resume`返回的值，除了第一个(boolean error code)。和`coroutine.resume`不同的是，`coroutine.wrap`不捕获错误，错误会冒泡到调用者。

一个协程工作的例子，考虑以下代码：
```lua
function foo (a)
  print("foo", a)
  return coroutine.yield(2*a)
end

co = coroutine.create(function (a,b)
      print("co-body", a, b)
      local r = foo(a+1)
      print("co-body", r)
      local r, s = coroutine.yield(a+b, a-b)
      print("co-body", r, s)
      return b, "end"
end)

print("main", coroutine.resume(co, 1, 10))
print("main", coroutine.resume(co, "r"))
print("main", coroutine.resume(co, "x", "y"))
print("main", coroutine.resume(co, "x", "y"))
```

当运行的时候，会产生下面的输出：

     co-body 1       10
     foo     2
     main    true    4
     co-body r
     main    true    11      -9
     co-body x       y
     main    true    10      end
     main    false   cannot resume dead coroutine

也可以通过**C API**创建和维护协程：`lua_newthread`、`lua_resume`和`lua_yield`。
