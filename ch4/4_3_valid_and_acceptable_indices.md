Valid and Acceptable Indices
============================

Any function in the API that receives stack indices works only with valid indices or acceptable indices.

A valid index is an index that refers to a position that stores a modifiable Lua value. It comprises stack indices between 1 and the stack top (1 ≤ abs(index) ≤ top) plus pseudo-indices, which represent some positions that are accessible to C code but that are not in the stack. Pseudo-indices are used to access the registry (see §4.5) and the upvalues of a C function (see §4.4).

Functions that do not need a specific mutable position, but only a value (e.g., query functions), can be called with acceptable indices. An acceptable index can be any valid index, but it also can be any positive index after the stack top within the space allocated for the stack, that is, indices up to the stack size. (Note that 0 is never an acceptable index.) Except when noted otherwise, functions in the API work with acceptable indices.

Acceptable indices serve to avoid extra tests against the stack top when querying the stack. For instance, a C function can query its third argument without the need to first check whether there is a third argument, that is, without the need to check whether 3 is a valid index.

For functions that can be called with acceptable indices, any non-valid index is treated as if it contains a value of a virtual type LUA_TNONE, which behaves like a nil value.
