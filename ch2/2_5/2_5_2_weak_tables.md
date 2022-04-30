弱表
====

弱表就是一个表，它的元素都是弱引用。弱引用是被垃圾收集器忽略的。换句话说，一个对象的仅有引用是弱引用，垃圾收集器会把它回收了。

弱表可能有弱键，或者弱值，或者两种都有。有弱值得表支持回收它的值，但禁止回收它的键。仅有弱值又有弱键的表支持回收键和值。在任何情况下，如果键或者值被回收，整个键值对都会从表中删除。一个弱表的类型是由它的元表中`__mode`字段（值是一个字符串）控制的，如果`__mode`字段包含`k`这个表的键是弱引用；如果`__mode`包含`v`，这个表的值是弱引用。

拥有弱键强值(值是强引用)的表，也叫`ephemeron table`瞬间表。在瞬间表中，只有当键是可用的的时候，值才是可用的。特殊情况：如果键的仅剩引用是通过它的值引用的，这个键值对会被移除。

表的弱引用性的变化，仅会在下一次垃圾回收过程中有效。特别的，即使我们将表的弱引用性改成强模式，Lua仍然会在改变生效之前回收一些数据。

只有有显式构造器的对象会从表中移除。值，如数字和轻量C函数，不是垃圾回收的目标，因此不会从表中移除（除非他们关联的值被回收了）。尽管字符串是垃圾回收的目标，他们没有显式构造器，因此也不会从表中移除。


Resurrected objects (that is, objects being finalized and objects accessible only through objects being finalized) have a special behavior in weak tables. They are removed from weak values before running their finalizers, but are removed from weak keys only in the next collection after running their finalizers, when such objects are actually freed. This behavior allows the finalizer to access properties associated with the object through weak tables.

If a weak table is among the resurrected objects in a collection cycle, it may not be properly cleared until the next cycle.
