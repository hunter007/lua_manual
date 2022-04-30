操作协程
======

这个库由一些协程类操作组成，包括表协程。查看(§2.6)[../ch2/2_6_coroutines.md]以了解协程的描述。

- **`coroutine.create(f)`**
用函数`f`创建一个协程对象。`f`必须是函数。返回新的协程对象，类型是`thread`。

- **`coroutine.isyieldable()`**
如果正在运行的协程可以yield，返回true。如果协程不是主协程，也不在不可yield的C函数里面。那么就是一个可以yield的协程。

- **`coroutine.resume(co [,val1, ···])`**
继续执行协程`co`。第一次resume一个协程时，开始执行代码。参数val1, ...都传入创建协程的函数体内。如果这个协程已经yield了，`resume`会重新启动它，参数val1, ... 作为yield行的结果传入。如果这个协程没有出错，`resume`会返回true和传递给yield的参数（当协程yield的时候），或者由函数体返回的所有值（当协程终止的时候）。如果有错误，`resume`返回false和错误信息。

- **`coroutine.running()`**
返回正在运行的协程和一个boolean值。如果这个协程是主协程，返回true。

- **`coroutine.status(co)`**
返回协程`co`的状态，如果协程正在运行，返回一个字符串:"running""；如果协程被`yield`调用挂起，或者还没有哦开始执行，返回"suspended"；如果协程是激活的但没有运行（也就是说，被另一个协程resume了），返回"normal"；如果协程已经完成了，或者因为错误而停止，返回"dead"。

- **`coroutine.wrap(f)`**
用函数`f`创建新协程。`f`必须是函数。`wrap`返回一个函数，每次调用这个函数，可以resume这个协程。传递给这个函数的参数，就像调用`resume`传给`resume`的参数。返回的结果哦，就像`resume`返回的结果，除了第一个值。出错时，冒泡这个error。

- **`coroutine.yield(···)`**
挂起正在运行的协程，传递给yield的参数，将作为`resume`的结果返回。
