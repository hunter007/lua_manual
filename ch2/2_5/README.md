垃圾收集
=======

Lua performs automatic memory management. This means that you do not have to worry about allocating memory for new objects or freeing it when the objects are no longer needed. Lua manages memory automatically by running a garbage collector to collect all dead objects (that is, objects that are no longer accessible from Lua). All memory used by Lua is subject to automatic management: strings, tables, userdata, functions, threads, internal structures, etc.

Lua执行自动内存管理。这意味着我们不需要为新对象分配内存和不再需要对象时如何释放内存而担心。Lua通过运行垃圾收集器自动地管理内存，垃圾收集器会收集所有已经死去的对象（Lua不再访问的对象）。Lua使用的内存都会自动管理：字符串、表、userdata、函数、线程、内部结构等。

Lua implements an incremental mark-and-sweep collector. It uses two numbers to control its garbage-collection cycles: the garbage-collector pause and the garbage-collector step multiplier. Both use percentage points as units (e.g., a value of 100 means an internal value of 1).

Lua实现了一个增量的标记-清楚收集器。这个收集器使用两个数字来控制它的垃圾收集循环：暂停和步进器。两者都使用百分比作为单位。（如100等于内部值1）


The garbage-collector pause controls how long the collector waits before starting a new cycle. Larger values make the collector less aggressive. Values smaller than 100 mean the collector will not wait to start a new cycle. A value of 200 means that the collector waits for the total memory in use to double before starting a new cycle.
垃圾收集器暂停控制收集器在开始一个新的周期之前等待多久。更大的值使搜集器更少侵入性。比100小的值意味着收集器不会等待开始一个新周期。200意味着收集器等待内存使用2倍才开始新周期。

The garbage-collector step multiplier controls the relative speed of the collector relative to memory allocation. Larger values make the collector more aggressive but also increase the size of each incremental step. You should not use values smaller than 100, because they make the collector too slow and can result in the collector never finishing a cycle. The default is 200, which means that the collector runs at "twice" the speed of memory allocation.
垃圾收集器步进器控制收集器相对内存分配的速度。Larger values make the collector more aggressive but also increase the size of each incremental step. 不应该使用小于100的值，因为这会使收集器变慢，可能导致收集器永远不能完成一个周期。默认值使200，这意味着收集器以内存分配速度两倍的速度运行一次。

If you set the step multiplier to a very large number (larger than 10% of the maximum number of bytes that the program may use), the collector behaves like a stop-the-world collector. If you then set the pause to 200, the collector behaves as in old Lua versions, doing a complete collection every time Lua doubles its memory usage.

如果将步进器设置为一个很大的值（大于程序可用内存最大值的10%），收集器会发生STW（stop-the-world）。如果设置暂停为200，收集器的行为和旧的Lua版本一样——Lua内存每增大一倍，执行一次完整的收集。


You can change these numbers by calling lua_gc in C or collectgarbage in Lua. You can also use these functions to control the collector directly (e.g., stop and restart it).

通过在C中调用lua_gc，或者在Lua中调用collectgarbage，我们可以改变这些值。我们也可以使用这些函数，直接控制收集器（如停止并重启它）。
