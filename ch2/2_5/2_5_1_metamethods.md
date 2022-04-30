垃圾收集元方法
============

我们可以为表设置垃圾收集的元方法（通过**C API**也可以为`userdata`设置垃圾收集的元方法）。这些元方法也叫`finalizers`。`finalizer`允许我们协调垃圾收集和外部资源管理（关闭文件、网络或者数据库连接，或者释放自己的内存）。

在来及手机的时候，对于要回收的对象（表和userdata），我们可以标记它是可回收的。我们通过给对象设置元表，并且在元表中设置`__gc字段，将一个对象标记成可回收的。如果给一个对象设置的元表中没有`__gc`，后来又增加了`__gc`，这个对象不被认为是可回收的。

当一个被标记的对象变成垃圾时，并不会立即被垃圾收集器回收掉。Lua把这个对象放入一个列表，回收结束之后，Lua便利这个列表。列表中的每个对象，Lua检查它的`__gc`方法：如果是一个函数，Lua调用这个函数，这个对象时这个函数的第一个参数；如果不是一个函数，Lua简单地忽略掉。


At the end of each garbage-collection cycle, the finalizers for objects are called in the reverse order that the objects were marked for finalization, among those collected in that cycle; that is, the first finalizer to be called is the one associated with the object marked last in the program. The execution of each finalizer may occur at any point during the execution of the regular code.

Because the object being collected must still be used by the finalizer, that object (and other objects accessible only through it) must be resurrected by Lua. Usually, this resurrection is transient, and the object memory is freed in the next garbage-collection cycle. However, if the finalizer stores the object in some global place (e.g., a global variable), then the resurrection is permanent. Moreover, if the finalizer marks a finalizing object for finalization again, its finalizer will be called again in the next cycle where the object is unreachable. In any case, the object memory is freed only in a GC cycle where the object is unreachable and not marked for finalization.

When you close a state (see lua_close), Lua calls the finalizers of all objects marked for finalization, following the reverse order that they were marked. If any finalizer marks objects for collection during that phase, these marks have no effect.
