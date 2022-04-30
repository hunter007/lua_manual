函数和类型
========

我们在这里列出了C API中所有的函数和类型，以字母的顺序。每个函数都有这个标记：`[-o, +p, x]`。

第一个信息：`o`，表示这个函数从栈中弹出多少个元素。第二个信息：`p`表示这个函数会向栈中压入多少个元素（所有函数都是先从栈中弹出它的参数，最后压入它的返回结果）。`x|y`则表示这个函数可以压入/弹出`x`或者`y`个元素，取决于具体情况。`?`意味着我们不能只看它的参数就知道这个函数会压入/弹出多少个元素（如依赖栈中有什么）。第三个是`x`，告诉我们是否会抛出错误：`-`意味着不会抛出错误, `m`意味着可能抛出out-of-memory错误或者`__gc`元方法抛出的错误；`e`意味着函数可能抛出任何错误；`v`意味着有目的地抛出错误。

##### lua_absindex #####

```c
int lua_absindex (lua_State *L, int idx);       [-0, +0, –]
```

Converts the acceptable index idx into an equivalent absolute index (that is, one that does not depend on the stack top).

##### lua_Alloc #####

```c
typedef void * (*lua_Alloc) (void *ud,
                             void *ptr,
                             size_t osize,
                             size_t nsize);
```

The type of the memory-allocation function used by Lua states. The allocator function must provide a functionality similar to realloc, but not exactly the same. Its arguments are ud, an opaque pointer passed to lua_newstate; ptr, a pointer to the block being allocated/reallocated/freed; osize, the original size of the block or some code about what is being allocated; and nsize, the new size of the block.

When ptr is not NULL, osize is the size of the block pointed by ptr, that is, the size given when it was allocated or reallocated.

When ptr is NULL, osize encodes the kind of object that Lua is allocating. osize is any of LUA_TSTRING, LUA_TTABLE, LUA_TFUNCTION, LUA_TUSERDATA, or LUA_TTHREAD when (and only when) Lua is creating a new object of that type. When osize is some other value, Lua is allocating memory for something else.

Lua assumes the following behavior from the allocator function:

When nsize is zero, the allocator must behave like free and return NULL.

When nsize is not zero, the allocator must behave like realloc. The allocator returns NULL if and only if it cannot fulfill the request. Lua assumes that the allocator never fails when osize >= nsize.

Here is a simple implementation for the allocator function. It is used in the auxiliary library by luaL_newstate.

     static void *l_alloc (void *ud, void *ptr, size_t osize,
                                                size_t nsize) {
       (void)ud;  (void)osize;  /* not used */
       if (nsize == 0) {
         free(ptr);
         return NULL;
       }
       else
         return realloc(ptr, nsize);
     }
Note that Standard C ensures that free(NULL) has no effect and that realloc(NULL,size) is equivalent to malloc(size). This code assumes that realloc does not fail when shrinking a block. (Although Standard C does not ensure this behavior, it seems to be a safe assumption.)

##### lua_arith #####

```c
void lua_arith (lua_State *L, int op);
```

Performs an arithmetic or bitwise operation over the two values (or one, in the case of negations) at the top of the stack, with the value at the top being the second operand, pops these values, and pushes the result of the operation. The function follows the semantics of the corresponding Lua operator (that is, it may call metamethods).

The value of op must be one of the following constants:

LUA_OPADD: performs addition (+)
LUA_OPSUB: performs subtraction (-)
LUA_OPMUL: performs multiplication (*)
LUA_OPDIV: performs float division (/)
LUA_OPIDIV: performs floor division (//)
LUA_OPMOD: performs modulo (%)
LUA_OPPOW: performs exponentiation (^)
LUA_OPUNM: performs mathematical negation (unary -)
LUA_OPBNOT: performs bitwise NOT (~)
LUA_OPBAND: performs bitwise AND (&)
LUA_OPBOR: performs bitwise OR (|)
LUA_OPBXOR: performs bitwise exclusive OR (~)
LUA_OPSHL: performs left shift (<<)
LUA_OPSHR: performs right shift (>>)

##### lua_atpanic #####

```c
lua_CFunction lua_atpanic (lua_State *L, lua_CFunction panicf);
```
Sets a new panic function and returns the old one (see §4.6).


###### lua_call ######

```c
void lua_call (lua_State *L, int nargs, int nresults);
```
Calls a function.

To call a function you must use the following protocol: first, the function to be called is pushed onto the stack; then, the arguments to the function are pushed in direct order; that is, the first argument is pushed first. Finally you call lua_call; nargs is the number of arguments that you pushed onto the stack. All arguments and the function value are popped from the stack when the function is called. The function results are pushed onto the stack when the function returns. The number of results is adjusted to nresults, unless nresults is LUA_MULTRET. In this case, all results from the function are pushed; Lua takes care that the returned values fit into the stack space, but it does not ensure any extra space in the stack. The function results are pushed onto the stack in direct order (the first result is pushed first), so that after the call the last result is on the top of the stack.

Any error inside the called function is propagated upwards (with a longjmp).

The following example shows how the host program can do the equivalent to this Lua code:

     a = f("how", t.x, 14)
Here it is in C:

     lua_getglobal(L, "f");                  /* function to be called */
     lua_pushliteral(L, "how");                       /* 1st argument */
     lua_getglobal(L, "t");                    /* table to be indexed */
     lua_getfield(L, -1, "x");        /* push result of t.x (2nd arg) */
     lua_remove(L, -2);                  /* remove 't' from the stack */
     lua_pushinteger(L, 14);                          /* 3rd argument */
     lua_call(L, 3, 1);     /* call 'f' with 3 arguments and 1 result */
     lua_setglobal(L, "a");                         /* set global 'a' */
Note that the code above is balanced: at its end, the stack is back to its original configuration. This is considered good programming practice.

##### lua_callk #####

```c
void lua_callk (lua_State *L,
                int nargs,
                int nresults,
                lua_KContext ctx,
                lua_KFunction k);
```

This function behaves exactly like lua_call, but allows the called function to yield (see §4.7).

##### lua_CFunction #####

```c
typedef int (*lua_CFunction) (lua_State *L);
```

Type for C functions.

In order to communicate properly with Lua, a C function must use the following protocol, which defines the way parameters and results are passed: a C function receives its arguments from Lua in its stack in direct order (the first argument is pushed first). So, when the function starts, lua_gettop(L) returns the number of arguments received by the function. The first argument (if any) is at index 1 and its last argument is at index lua_gettop(L). To return values to Lua, a C function just pushes them onto the stack, in direct order (the first result is pushed first), and returns the number of results. Any other value in the stack below the results will be properly discarded by Lua. Like a Lua function, a C function called by Lua can also return many results.

As an example, the following function receives a variable number of numeric arguments and returns their average and their sum:

     static int foo (lua_State *L) {
       int n = lua_gettop(L);    /* number of arguments */
       lua_Number sum = 0.0;
       int i;
       for (i = 1; i <= n; i++) {
         if (!lua_isnumber(L, i)) {
           lua_pushliteral(L, "incorrect argument");
           lua_error(L);
         }
         sum += lua_tonumber(L, i);
       }
       lua_pushnumber(L, sum/n);        /* first result */
       lua_pushnumber(L, sum);         /* second result */
       return 2;                   /* number of results */
     }

##### lua_checkstack #####

```c
int lua_checkstack (lua_State *L, int n);
```
确保栈至少有n个额外的slot（我们可以安全地压入n个值。）如果不满足这个请求，或者超过栈最大空间时（一般要几千个元素时。），函数返回false。也或者是不能分配足够的空间了。这个函数绝不会收缩栈的大小；如果栈已经有足够的空间，不会对栈做什么操作。

##### lua_close #####

```c
void lua_close (lua_State *L);     -- [-0, +0, –]
```
销毁给定 Lua state中的所有对象（可能需要调用相应的gc元方法），并且释放这个state使用的所有动态内存。在有些平台上，可能不需要调用这个函数，因为当宿主程序退出时，所有资源会自然而然地释放掉。另一方面，长时间运行的程序会创建很多state，如果daemon程序或者web服务器，可能需要在对象不再被使用的时候就立刻关闭state。

##### lua_compare #####

```c
int lua_compare (lua_State *L, int index1, int index2, int op);   -- [-0, +0, e]
```

比较两个lua值。如果`index1`对应的值和`index2`对应的值比，满足`op`操作，那么返回`1`。（可能会调用元方法）。否则返回`0`。如果任意一个值不是有效的，也会返回0。
支持的`op`有：

  - LUA_OPEQ: 相等比较 (==)
  - LUA_OPLT: 小于比较 (<)
  - LUA_OPLE: 小于等于比较 (<=)


##### lua_concat #####

```c
void lua_concat (lua_State *L, int n);    -- [-n, +1, e]
```

连接栈顶的n个值，弹出它们，然后把结果压入栈。如果 n == 1，函数什么也不做；如果 n == 0，结果是空字符串。连接操作和 Lua的字符串连接操作语义相同([§3.4.6]())


##### lua_copy #####

```c
void lua_copy (lua_State *L, int fromidx, int toidx);   -- [-0, +0, –]
```

拷贝指定索引(`fromidx`)的元素，到另一个有效的索引`toidx`，替换掉这个位置的值。其他地方的值不受影响。


##### lua_createtable #####

```c
void lua_createtable (lua_State *L, int narr, int nrec);
```

Creates a new empty table and pushes it onto the stack. Parameter narr is a hint for how many elements the table will have as a sequence; parameter nrec is a hint for how many other elements the table will have. Lua may use these hints to preallocate memory for the new table. This preallocation is useful for performance when you know in advance how many elements the table will have. Otherwise you can use the function lua_newtable.

##### lua_dump #####

```c
int lua_dump (lua_State *L,
                        lua_Writer writer,
                        void *data,
                        int strip);
```
Dumps a function as a binary chunk. Receives a Lua function on the top of the stack and produces a binary chunk that, if loaded again, results in a function equivalent to the one dumped. As it produces parts of the chunk, lua_dump calls function writer (see lua_Writer) with the given data to write them.

If strip is true, the binary representation may not include all debug information about the function, to save space.

The value returned is the error code returned by the last call to the writer; 0 means no errors.

This function does not pop the Lua function from the stack.

##### lua_error #####

```c
int lua_error (lua_State *L);   /* [-1, +0, v] */
```
Generates a Lua error, using the value at the top of the stack as the error object. This function does a long jump, and therefore never returns (see luaL_error).

##### lua_gc #####

```c
int lua_gc (lua_State *L, int what, int data);  /* [-0, +0, m] */
```
Controls the garbage collector.

This function performs several tasks, according to the value of the parameter what:

LUA_GCSTOP: stops the garbage collector.
LUA_GCRESTART: restarts the garbage collector.
LUA_GCCOLLECT: performs a full garbage-collection cycle.
LUA_GCCOUNT: returns the current amount of memory (in Kbytes) in use by Lua.
LUA_GCCOUNTB: returns the remainder of dividing the current amount of bytes of memory in use by Lua by 1024.
LUA_GCSTEP: performs an incremental step of garbage collection.
LUA_GCSETPAUSE: sets data as the new value for the pause of the collector (see §2.5) and returns the previous value of the pause.
LUA_GCSETSTEPMUL: sets data as the new value for the step multiplier of the collector (see §2.5) and returns the previous value of the step multiplier.
LUA_GCISRUNNING: returns a boolean that tells whether the collector is running (i.e., not stopped).
For more details about these options, see collectgarbage.

##### lua_getallocf #####

```c
lua_Alloc lua_getallocf (lua_State *L, void **ud);  /* [-0, +0, –] */
```

Returns the memory-allocation function of a given state. If ud is not NULL, Lua stores in *ud the opaque pointer given when the memory-allocator function was set.

##### lua_getfield #####

```c
int lua_getfield (lua_State *L, int index, const char *k);   /* [-0, +1, e] */
```

Pushes onto the stack the value t[k], where t is the value at the given index. As in Lua, this function may trigger a metamethod for the "index" event (see §2.4).

Returns the type of the pushed value.

##### lua_getextraspace #####

```c
void *lua_getextraspace (lua_State *L);   /* [-0, +0, –] */
```

Returns a pointer to a raw memory area associated with the given Lua state. The application can use this area for any purpose; Lua does not use it for anything.

Each new thread has this area initialized with a copy of the area of the main thread.

By default, this area has the size of a pointer to void, but you can recompile Lua with a different size for this area. (See LUA_EXTRASPACE in luaconf.h.)

##### lua_getglobal #####

```c
int lua_getglobal (lua_State *L, const char *name);  /* [-0, +1, e] */
```

Pushes onto the stack the value of the global name. Returns the type of that value.

##### lua_geti #####

```c
int lua_geti (lua_State *L, int index, lua_Integer i); /* [-0, +1, e] */
```

Pushes onto the stack the value t[i], where t is the value at the given index. As in Lua, this function may trigger a metamethod for the "index" event (see §2.4).

Returns the type of the pushed value.

##### lua_getmetatable #####

```c
int lua_getmetatable (lua_State *L, int index);  /* [-0, +(0|1), –] */
```

If the value at the given index has a metatable, the function pushes that metatable onto the stack and returns 1. Otherwise, the function returns 0 and pushes nothing on the stack.

##### lua_gettable #####

```c
int lua_gettable (lua_State *L, int index);   /* [-1, +1, e] */
```

Pushes onto the stack the value t[k], where t is the value at the given index and k is the value at the top of the stack.

This function pops the key from the stack, pushing the resulting value in its place. As in Lua, this function may trigger a metamethod for the "index" event (see §2.4).

Returns the type of the pushed value.

##### lua_gettop #####

```c
int lua_gettop (lua_State *L);  /* [-0, +0, –] */
```

Returns the index of the top element in the stack. Because indices start at 1, this result is equal to the number of elements in the stack; in particular, 0 means an empty stack.

##### lua_getuservalue #####

```c
int lua_getuservalue (lua_State *L, int index);  /* [-0, +1, –] */
```

Pushes onto the stack the Lua value associated with the full userdata at the given index.

Returns the type of the pushed value.

##### lua_insert #####

```c
void lua_insert (lua_State *L, int index);   /* [-1, +1, –] */
```

Moves the top element into the given valid index, shifting up the elements above this index to open space. This function cannot be called with a pseudo-index, because a pseudo-index is not an actual stack position.

##### lua_Integer #####

```c
typedef ... lua_Integer;
```
The type of integers in Lua.

By default this type is long long, (usually a 64-bit two-complement integer), but that can be changed to long or int (usually a 32-bit two-complement integer). (See LUA_INT_TYPE in luaconf.h.)

Lua also defines the constants LUA_MININTEGER and LUA_MAXINTEGER, with the minimum and the maximum values that fit in this type.

##### lua_isboolean #####

```c
int lua_isboolean (lua_State *L, int index); /* [-0, +0, –] */
```

Returns 1 if the value at the given index is a boolean, and 0 otherwise.

##### lua_iscfunction #####

```c
int lua_iscfunction (lua_State *L, int index);   /* [-0, +0, –] */
```

Returns 1 if the value at the given index is a C function, and 0 otherwise.

##### lua_isfunction #####

```c
int lua_isfunction (lua_State *L, int index); /* [-0, +0, –] */
```

Returns 1 if the value at the given index is a function (either C or Lua), and 0 otherwise.

##### lua_isinteger #####

```c
int lua_isinteger (lua_State *L, int index);   /* [-0, +0, –] */
```

Returns 1 if the value at the given index is an integer (that is, the value is a number and is represented as an integer), and 0 otherwise.

##### lua_islightuserdata #####

```c
int lua_islightuserdata (lua_State *L, int index);   /* [-0, +0, –] */
```

Returns 1 if the value at the given index is a light userdata, and 0 otherwise.

##### lua_isnil #####

```c
int lua_isnil (lua_State *L, int index);   /* [-0, +0, –] */
```

Returns 1 if the value at the given index is nil, and 0 otherwise.

##### lua_isnone #####

```c
int lua_isnone (lua_State *L, int index);  /* [-0, +0, –] */
```

Returns 1 if the given index is not valid, and 0 otherwise.

##### lua_isnoneornil #####

```c
int lua_isnoneornil (lua_State *L, int index);   /* [-0, +0, –] */
```

Returns 1 if the given index is not valid or if the value at this index is nil, and 0 otherwise.

##### lua_isnumber #####

```c
int lua_isnumber (lua_State *L, int index);  /* [-0, +0, –] */
```

Returns 1 if the value at the given index is a number or a string convertible to a number, and 0 otherwise.

##### lua_isstring #####

```c
int lua_isstring (lua_State *L, int index);   /* [-0, +0, –] */
```

Returns 1 if the value at the given index is a string or a number (which is always convertible to a string), and 0 otherwise.


##### lua_istable #####
```c
int lua_istable (lua_State *L, int index);  /* [-0, +0, –] */
```

Returns 1 if the value at the given index is a table, and 0 otherwise.

##### lua_isthread #####

```c
int lua_isthread (lua_State *L, int index);   /* [-0, +0, –] */
```

Returns 1 if the value at the given index is a thread, and 0 otherwise.

##### lua_isuserdata #####

```c
int lua_isuserdata (lua_State *L, int index);  /* [-0, +0, –] */
```

Returns 1 if the value at the given index is a userdata (either full or light), and 0 otherwise.

##### lua_isyieldable #####

```c
int lua_isyieldable(lua_State *L);  /* [-0, +0, –] */
```

Returns 1 if the given coroutine can yield, and 0 otherwise.

##### lua_KContext #####

```c
typedef ... lua_KContext; /* [-0, +0, –] */
```

The type for continuation-function contexts. It must be a numeric type. This type is defined as intptr_t when intptr_t is available, so that it can store pointers too. Otherwise, it is defined as ptrdiff_t.

##### lua_KFunction #####

```c
typedef int (*lua_KFunction) (lua_State *L, int status, lua_KContext ctx);
```
Type for continuation functions (see §4.7).

##### lua_len #####

```c
void lua_len (lua_State *L, int index);  /* [-0, +1, e] */
```

Returns the length of the value at the given index. It is equivalent to the '#' operator in Lua (see §3.4.7) and may trigger a metamethod for the "length" event (see §2.4). The result is pushed on the stack.

##### lua_load #####

```c
int lua_load (lua_State *L,
              lua_Reader reader,
              void *data,
              const char *chunkname,
              const char *mode);  /* [-0, +1, –] */
```

Loads a Lua chunk without running it. If there are no errors, lua_load pushes the compiled chunk as a Lua function on top of the stack. Otherwise, it pushes an error message.

The return values of lua_load are:

LUA_OK: no errors;
LUA_ERRSYNTAX: syntax error during precompilation;
LUA_ERRMEM: memory allocation (out-of-memory) error;
LUA_ERRGCMM: error while running a __gc metamethod. (This error has no relation with the chunk being loaded. It is generated by the garbage collector.)
The lua_load function uses a user-supplied reader function to read the chunk (see lua_Reader). The data argument is an opaque value passed to the reader function.

The chunkname argument gives a name to the chunk, which is used for error messages and in debug information (see §4.9).

lua_load automatically detects whether the chunk is text or binary and loads it accordingly (see program luac). The string mode works as in function load, with the addition that a NULL value is equivalent to the string "bt".

lua_load uses the stack internally, so the reader function must always leave the stack unmodified when returning.

If the resulting function has upvalues, its first upvalue is set to the value of the global environment stored at index LUA_RIDX_GLOBALS in the registry (see §4.5). When loading main chunks, this upvalue will be the _ENV variable (see §2.2). Other upvalues are initialized with nil.

##### lua_newstate #####

```c
lua_State *lua_newstate (lua_Alloc f, void *ud);  /* [-0, +0, –] */
```

Creates a new thread running in a new, independent state. Returns NULL if it cannot create the thread or the state (due to lack of memory). The argument f is the allocator function; Lua does all memory allocation for this state through this function (see lua_Alloc). The second argument, ud, is an opaque pointer that Lua passes to the allocator in every call.

##### lua_newtable #####

```c
void lua_newtable (lua_State *L);  /* [-0, +1, m] */
```

Creates a new empty table and pushes it onto the stack. It is equivalent to lua_createtable(L, 0, 0).

##### lua_newthread #####

```c
lua_State *lua_newthread (lua_State *L);  /* [-0, +1, m] */
```

Creates a new thread, pushes it on the stack, and returns a pointer to a lua_State that represents this new thread. The new thread returned by this function shares with the original thread its global environment, but has an independent execution stack.

There is no explicit function to close or to destroy a thread. Threads are subject to garbage collection, like any Lua object.

##### lua_newuserdata #####

```c
void *lua_newuserdata (lua_State *L, size_t size);  /* [-0, +1, m] */
```

This function allocates a new block of memory with the given size, pushes onto the stack a new full userdata with the block address, and returns this address. The host program can freely use this memory.

##### lua_next #####

```
int lua_next (lua_State *L, int index);  /* [-1, +(2|0), e] */
```

Pops a key from the stack, and pushes a key–value pair from the table at the given index (the "next" pair after the given key). If there are no more elements in the table, then lua_next returns 0 (and pushes nothing).

A typical traversal looks like this:

```c
/* table is in the stack at index 't' */
lua_pushnil(L);  /* first key */
while (lua_next(L, t) != 0) {
  /* uses 'key' (at index -2) and 'value' (at index -1) */
  printf("%s - %s\n",
         lua_typename(L, lua_type(L, -2)),
         lua_typename(L, lua_type(L, -1)));
  /* removes 'value'; keeps 'key' for next iteration */
  lua_pop(L, 1);
}
```

While traversing a table, do not call lua_tolstring directly on a key, unless you know that the key is actually a string. Recall that lua_tolstring may change the value at the given index; this confuses the next call to lua_next.

See function next for the caveats of modifying the table during its traversal.

##### lua_Number #####

```c
typedef ... lua_Number;
```

The type of floats in Lua.

By default this type is double, but that can be changed to a single float or a long double. (See LUA_FLOAT_TYPE in luaconf.h.)

##### lua_numbertointeger #####

```c
int lua_numbertointeger (lua_Number n, lua_Integer *p);
```

Converts a Lua float to a Lua integer. This macro assumes that n has an integral value. If that value is within the range of Lua integers, it is converted to an integer and assigned to *p. The macro results in a boolean indicating whether the conversion was successful. (Note that this range test can be tricky to do correctly without this macro, due to roundings.)

This macro may evaluate its arguments more than once.

##### lua_pcall #####

```c
int lua_pcall (lua_State *L, int nargs, int nresults, int msgh);  /* [-(nargs + 1), +(nresults|1), –] */
```

Calls a function in protected mode.

Both nargs and nresults have the same meaning as in lua_call. If there are no errors during the call, lua_pcall behaves exactly like lua_call. However, if there is any error, lua_pcall catches it, pushes a single value on the stack (the error object), and returns an error code. Like lua_call, lua_pcall always removes the function and its arguments from the stack.

If msgh is 0, then the error object returned on the stack is exactly the original error object. Otherwise, msgh is the stack index of a message handler. (This index cannot be a pseudo-index.) In case of runtime errors, this function will be called with the error object and its return value will be the object returned on the stack by lua_pcall.

Typically, the message handler is used to add more debug information to the error object, such as a stack traceback. Such information cannot be gathered after the return of lua_pcall, since by then the stack has unwound.

The lua_pcall function returns one of the following constants (defined in lua.h):

LUA_OK (0): success.
LUA_ERRRUN: a runtime error.
LUA_ERRMEM: memory allocation error. For such errors, Lua does not call the message handler.
LUA_ERRERR: error while running the message handler.
LUA_ERRGCMM: error while running a __gc metamethod. For such errors, Lua does not call the message handler (as this kind of error typically has no relation with the function being called).

##### lua_pcallk #####

```c
int lua_pcallk (lua_State *L,
                int nargs,
                int nresults,
                int msgh,
                lua_KContext ctx,
                lua_KFunction k);  /* [-(nargs + 1), +(nresults|1), –] */
```

This function behaves exactly like lua_pcall, but allows the called function to yield (see §4.7).

##### lua_pop #####

```c
void lua_pop (lua_State *L, int n); /* [-n, +0, –] */
```
从栈顶弹出n个元素。

##### lua_pushboolean #####

```c
void lua_pushboolean (lua_State *L, int b);  /* [-0, +1, –] */
```
将值`b`作为`boolean`压入栈顶。

##### lua_pushcclosure #####

```c
void lua_pushcclosure (lua_State *L, lua_CFunction fn, int n);    /* [-n, +1, m] */
```

Pushes a new C closure onto the stack.

When a C function is created, it is possible to associate some values with it, thus creating a C closure (see §4.4); these values are then accessible to the function whenever it is called. To associate values with a C function, first these values must be pushed onto the stack (when there are multiple values, the first value is pushed first). Then lua_pushcclosure is called to create and push the C function onto the stack, with the argument n telling how many values will be associated with the function. lua_pushcclosure also pops these values from the stack.

The maximum value for n is 255.

When n is zero, this function creates a light C function, which is just a pointer to the C function. In that case, it never raises a memory error.

##### lua_pushcfunction #####

```c
void lua_pushcfunction (lua_State *L, lua_CFunction f); /* [-0, +1, –] */
```

Pushes a C function onto the stack. This function receives a pointer to a C function and pushes onto the stack a Lua value of type function that, when called, invokes the corresponding C function.

Any function to be callable by Lua must follow the correct protocol to receive its parameters and return its results (see lua_CFunction).

##### lua_pushfstring #####

```c
const char *lua_pushfstring (lua_State *L, const char *fmt, ...); /* [-0, +1, e] */
```

Pushes onto the stack a formatted string and returns a pointer to this string. It is similar to the ISO C function sprintf, but has some important differences:

You do not have to allocate space for the result: the result is a Lua string and Lua takes care of memory allocation (and deallocation, through garbage collection).
The conversion specifiers are quite restricted. There are no flags, widths, or precisions. The conversion specifiers can only be '%%' (inserts the character '%'), '%s' (inserts a zero-terminated string, with no size restrictions), '%f' (inserts a lua_Number), '%I' (inserts a lua_Integer), '%p' (inserts a pointer as a hexadecimal numeral), '%d' (inserts an int), '%c' (inserts an int as a one-byte character), and '%U' (inserts a long int as a UTF-8 byte sequence).
Unlike other push functions, this function checks for the stack space it needs, including the slot for its result.


##### lua_pushglobaltable #####

```c
void lua_pushglobaltable (lua_State *L);  /* [-0, +1, –] */
```
把全局环境压入栈。


##### lua_pushinteger #####

```c
void lua_pushinteger (lua_State *L, lua_Integer n);  /*  [-0, +1, –] */
```
将一个Lua整数压入栈顶。


##### lua_pushlightuserdata #####

```c
void lua_pushlightuserdata (lua_State *L, void *p);  /* [-0, +1, –] */
```

将 light userdata压入栈顶。

Userdata represent C values in Lua. A light userdata represents a pointer, a void*. It is a value (like a number): you do not create it, it has no individual metatable, and it is not collected (as it was never created). A light userdata is equal to "any" light userdata with the same C address.


##### lua_pushliteral #####

```c
const char *lua_pushliteral (lua_State *L, const char *s);  /* [-0, +1, m] */
```

这个宏等效于`lua_pushstring`，但是只能用于当`s`是字面量字符串时。


##### lua_pushlstring #####

```c
const char *lua_pushlstring (lua_State *L, const char *s, size_t len);  /* [-0, +1, m] */
```

Pushes the string pointed to by s with size len onto the stack. Lua makes (or reuses) an internal copy of the given string, so the memory at s can be freed or reused immediately after the function returns. The string can contain any binary data, including embedded zeros.

Returns a pointer to the internal copy of the string.


##### lua_pushnil #####

```c
void lua_pushnil (lua_State *L);  /* [-0, +1, –] */
```
将Lua的nil压入栈。


##### lua_pushnumber #####

```c
void lua_pushnumber (lua_State *L, lua_Number n);  /* [-0, +1, –] */
```

将一个Lua的浮点数压入栈。


##### lua_pushstring #####
```c
const char *lua_pushstring (lua_State *L, const char *s);   /* [-0, +1, m] */
```

Pushes the zero-terminated string pointed to by s onto the stack. Lua makes (or reuses) an internal copy of the given string, so the memory at s can be freed or reused immediately after the function returns.

Returns a pointer to the internal copy of the string.

If s is NULL, pushes nil and returns NULL.

##### lua_pushthread #####

```c
int lua_pushthread (lua_State *L);  /* [-0, +1, –] */
```

Pushes the thread represented by L onto the stack. Returns 1 if this thread is the main thread of its state.

##### lua_pushvalue #####

```c
void lua_pushvalue (lua_State *L, int index);  /* [-0, +1, –] */
```

将指定索引位置的值压入栈顶。


##### lua_pushvfstring #####

```c
const char *lua_pushvfstring (lua_State *L,
                              const char *fmt,
                              va_list argp);   /* [-0, +1, m] */
```

Equivalent to lua_pushfstring, except that it receives a va_list instead of a variable number of arguments.


##### lua_rawequal #####

```c
int lua_rawequal (lua_State *L, int index1, int index2); /* [-0, +0, –] */
```

Returns 1 if the two values in indices index1 and index2 are primitively equal (that is, without calling the __eq metamethod). Otherwise returns 0. Also returns 0 if any of the indices are not valid.

##### lua_rawget #####

```c
int lua_rawget (lua_State *L, int index);  /* [-1, +1, –] */
```

Similar to lua_gettable, but does a raw access (i.e., without metamethods).

##### lua_rawgeti #####

```c
int lua_rawgeti (lua_State *L, int index, lua_Integer n);  /* [-0, +1, –] */
```

Pushes onto the stack the value t[n], where t is the table at the given index. The access is raw, that is, it does not invoke the __index metamethod.

Returns the type of the pushed value.

##### lua_rawgetp #####

```c
int lua_rawgetp (lua_State *L, int index, const void *p); /* [-0, +1, –] */
```

Pushes onto the stack the value t[k], where t is the table at the given index and k is the pointer p represented as a light userdata. The access is raw; that is, it does not invoke the __index metamethod.

Returns the type of the pushed value.

##### lua_rawlen #####

```c
size_t lua_rawlen (lua_State *L, int index);  /* [-0, +0, –] */
```

Returns the raw "length" of the value at the given index: for strings, this is the string length; for tables, this is the result of the length operator ('#') with no metamethods; for userdata, this is the size of the block of memory allocated for the userdata; for other values, it is 0.

##### lua_rawset #####

```c
void lua_rawset (lua_State *L, int index);  /* [-2, +0, m] */
```
类似于`lua_settable`，但是执行的是原始赋值（不执行元方法）。


##### lua_rawseti #####

```c
void lua_rawseti (lua_State *L, int index, lua_Integer i);   /* [-1, +0, m] */
```

Does the equivalent of t[i] = v, where t is the table at the given index and v is the value at the top of the stack.

This function pops the value from the stack. The assignment is raw, that is, it does not invoke the __newindex metamethod.


##### lua_rawsetp #####

```c
void lua_rawsetp (lua_State *L, int index, const void *p);  /* [-1, +0, m] */
```

Does the equivalent of t[p] = v, where t is the table at the given index, p is encoded as a light userdata, and v is the value at the top of the stack.

This function pops the value from the stack. The assignment is raw, that is, it does not invoke __newindex metamethod.


##### lua_Reader #####

```c
typedef const char * (*lua_Reader) (lua_State *L,
                                    void *data,
                                    size_t *size);
```

The reader function used by lua_load. Every time it needs another piece of the chunk, lua_load calls the reader, passing along its data parameter. The reader must return a pointer to a block of memory with a new piece of the chunk and set size to the block size. The block must exist until the reader function is called again. To signal the end of the chunk, the reader must return NULL or set size to zero. The reader function may return pieces of any size greater than zero.

##### lua_register #####

```c
void lua_register (lua_State *L, const char *name, lua_CFunction f);  /* [-0, +0, e] */
```

Sets the C function f as the new value of global name. It is defined as a macro:

```c
     #define lua_register(L,n,f) \
            (lua_pushcfunction(L, f), lua_setglobal(L, n))
```


##### lua_remove #####

```c
void lua_remove (lua_State *L, int index);  /* [-1, +0, –] */
```

Removes the element at the given valid index, shifting down the elements above this index to fill the gap. This function cannot be called with a pseudo-index, because a pseudo-index is not an actual stack position.

##### lua_replace #####

```c
void lua_replace (lua_State *L, int index);  /* [-1, +0, –] */
```

Moves the top element into the given valid index without shifting any element (therefore replacing the value at that given index), and then pops the top element.

##### lua_resume #####

```c
int lua_resume (lua_State *L, lua_State *from, int nargs);  /* [-?, +?, –] */
```

Starts and resumes a coroutine in the given thread L.

To start a coroutine, you push onto the thread stack the main function plus any arguments; then you call lua_resume, with nargs being the number of arguments. This call returns when the coroutine suspends or finishes its execution. When it returns, the stack contains all values passed to lua_yield, or all values returned by the body function. lua_resume returns LUA_YIELD if the coroutine yields, LUA_OK if the coroutine finishes its execution without errors, or an error code in case of errors (see lua_pcall).

In case of errors, the stack is not unwound, so you can use the debug API over it. The error object is on the top of the stack.

To resume a coroutine, you remove any results from the last lua_yield, put on its stack only the values to be passed as results from yield, and then call lua_resume.

The parameter from represents the coroutine that is resuming L. If there is no such coroutine, this parameter can be NULL.

##### lua_rotate #####

```c
void lua_rotate (lua_State *L, int idx, int n);  /* [-0, +0, –] */
```

Rotates the stack elements between the valid index idx and the top of the stack. The elements are rotated n positions in the direction of the top, for a positive n, or -n positions in the direction of the bottom, for a negative n. The absolute value of n must not be greater than the size of the slice being rotated. This function cannot be called with a pseudo-index, because a pseudo-index is not an actual stack position.

##### lua_setallocf #####

```c
void lua_setallocf (lua_State *L, lua_Alloc f, void *ud);  /* [-0, +0, –] */
```

Changes the allocator function of a given state to f with user data ud.

##### lua_setfield #####

```c
void lua_setfield (lua_State *L, int index, const char *k);  /* [-1, +0, e] */
```

Does the equivalent to t[k] = v, where t is the value at the given index and v is the value at the top of the stack.

This function pops the value from the stack. As in Lua, this function may trigger a metamethod for the "newindex" event (see §2.4).

##### lua_setglobal #####

```c
void lua_setglobal (lua_State *L, const char *name);   /* [-1, +0, e] */
```

Pops a value from the stack and sets it as the new value of global name.

##### lua_seti #####

```c
void lua_seti (lua_State *L, int index, lua_Integer n); /* [-1, +0, e] */
```

Does the equivalent to t[n] = v, where t is the value at the given index and v is the value at the top of the stack.

This function pops the value from the stack. As in Lua, this function may trigger a metamethod for the "newindex" event (see §2.4).

##### lua_setmetatable #####

```c
void lua_setmetatable (lua_State *L, int index);  /* [-1, +0, –] */
```

Pops a table from the stack and sets it as the new metatable for the value at the given index.

##### lua_settable #####

```c
void lua_settable (lua_State *L, int index);  /* [-2, +0, e] */
```

Does the equivalent to t[k] = v, where t is the value at the given index, v is the value at the top of the stack, and k is the value just below the top.

This function pops both the key and the value from the stack. As in Lua, this function may trigger a metamethod for the "newindex" event (see §2.4).

##### lua_settop #####

```c
void lua_settop (lua_State *L, int index); /* [-?, +?, –] */
```

Accepts any index, or 0, and sets the stack top to this index. If the new top is larger than the old one, then the new elements are filled with nil. If index is 0, then all stack elements are removed.

##### lua_setuservalue #####

```c
void lua_setuservalue (lua_State *L, int index);  /* [-1, +0, –] */
```

Pops a value from the stack and sets it as the new value associated to the full userdata at the given index.

##### lua_State #####

```c
typedef struct lua_State lua_State;
```

An opaque structure that points to a thread and indirectly (through the thread) to the whole state of a Lua interpreter. The Lua library is fully reentrant: it has no global variables. All information about a state is accessible through this structure.

A pointer to this structure must be passed as the first argument to every function in the library, except to lua_newstate, which creates a Lua state from scratch.

##### lua_status #####

```c
int lua_status (lua_State *L);   /* [-0, +0, –] */
```

Returns the status of the thread L.

The status can be 0 (LUA_OK) for a normal thread, an error code if the thread finished the execution of a lua_resume with an error, or LUA_YIELD if the thread is suspended.

You can only call functions in threads with status LUA_OK. You can resume threads with status LUA_OK (to start a new coroutine) or LUA_YIELD (to resume a coroutine).

##### lua_stringtonumber #####

```c
size_t lua_stringtonumber (lua_State *L, const char *s);   /* [-0, +1, –] */
```

Converts the zero-terminated string s to a number, pushes that number into the stack, and returns the total size of the string, that is, its length plus one. The conversion can result in an integer or a float, according to the lexical conventions of Lua (see §3.1). The string may have leading and trailing spaces and a sign. If the string is not a valid numeral, returns 0 and pushes nothing. (Note that the result can be used as a boolean, true if the conversion succeeds.)

##### lua_toboolean #####

```c
int lua_toboolean (lua_State *L, int index);  /* [-0, +0, –] */
```

Converts the Lua value at the given index to a C boolean value (0 or 1). Like all tests in Lua, lua_toboolean returns true for any Lua value different from false and nil; otherwise it returns false. (If you want to accept only actual boolean values, use lua_isboolean to test the value's type.)

##### lua_tocfunction #####

```c
lua_CFunction lua_tocfunction (lua_State *L, int index);  /* [-0, +0, –] */
```
Converts a value at the given index to a C function. That value must be a C function; otherwise, returns NULL.

##### lua_tointeger #####

```c
lua_Integer lua_tointeger (lua_State *L, int index);  /* [-0, +0, –] */
```

Equivalent to lua_tointegerx with isnum equal to NULL.

##### lua_tointegerx #####

```c
lua_Integer lua_tointegerx (lua_State *L, int index, int *isnum);   /* [-0, +0, –] */
```

Converts the Lua value at the given index to the signed integral type lua_Integer. The Lua value must be an integer, or a number or string convertible to an integer (see §3.4.3); otherwise, lua_tointegerx returns 0.

If isnum is not NULL, its referent is assigned a boolean value that indicates whether the operation succeeded.

##### lua_tolstring #####

```c
const char *lua_tolstring (lua_State *L, int index, size_t *len);  /* [-0, +0, m] */
```

Converts the Lua value at the given index to a C string. If len is not NULL, it sets *len with the string length. The Lua value must be a string or a number; otherwise, the function returns NULL. If the value is a number, then lua_tolstring also changes the actual value in the stack to a string. (This change confuses lua_next when lua_tolstring is applied to keys during a table traversal.)

lua_tolstring returns a pointer to a string inside the Lua state. This string always has a zero ('\0') after its last character (as in C), but can contain other zeros in its body.

Because Lua has garbage collection, there is no guarantee that the pointer returned by lua_tolstring will be valid after the corresponding Lua value is removed from the stack.

##### lua_tonumber #####

```c
lua_Number lua_tonumber (lua_State *L, int index);  /* [-0, +0, –] */
```

Equivalent to lua_tonumberx with isnum equal to NULL.

##### lua_tonumberx #####

```c
lua_Number lua_tonumberx (lua_State *L, int index, int *isnum);   /* [-0, +0, –] */
```

Converts the Lua value at the given index to the C type lua_Number (see lua_Number). The Lua value must be a number or a string convertible to a number (see §3.4.3); otherwise, lua_tonumberx returns 0.

If isnum is not NULL, its referent is assigned a boolean value that indicates whether the operation succeeded.

##### lua_topointer #####

```c
const void *lua_topointer (lua_State *L, int index);   /* [-0, +0, –] */
```

Converts the value at the given index to a generic C pointer (void*). The value can be a userdata, a table, a thread, or a function; otherwise, lua_topointer returns NULL. Different objects will give different pointers. There is no way to convert the pointer back to its original value.

Typically this function is used only for hashing and debug information.

##### lua_tostring #####

```c
const char *lua_tostring (lua_State *L, int index);  /* [-0, +0, m] */
```

Equivalent to lua_tolstring with len equal to NULL.


##### lua_tothread #####

```c
lua_State *lua_tothread (lua_State *L, int index);  /* [-0, +0, –] */
```

Converts the value at the given index to a Lua thread (represented as lua_State*). This value must be a thread; otherwise, the function returns NULL.

##### lua_touserdata #####

```c
void *lua_touserdata (lua_State *L, int index);   /*  [-0, +0, –] */
```

If the value at the given index is a full userdata, returns its block address. If the value is a light userdata, returns its pointer. Otherwise, returns NULL.

##### lua_type #####

```c
int lua_type (lua_State *L, int index);  /* [-0, +0, –] */
```

Returns the type of the value in the given valid index, or LUA_TNONE for a non-valid (but acceptable) index. The types returned by lua_type are coded by the following constants defined in lua.h: LUA_TNIL (0), LUA_TNUMBER, LUA_TBOOLEAN, LUA_TSTRING, LUA_TTABLE, LUA_TFUNCTION, LUA_TUSERDATA, LUA_TTHREAD, and LUA_TLIGHTUSERDATA.

##### lua_typename #####

```c
const char *lua_typename (lua_State *L, int tp);   /* [-0, +0, –] */
```

Returns the name of the type encoded by the value tp, which must be one the values returned by lua_type.

##### lua_Unsigned #####

```c
typedef ... lua_Unsigned;
```

The unsigned version of lua_Integer.


##### lua_upvalueindex #####

```c
int lua_upvalueindex (int i); /* [-0, +0, –] */
```

Returns the pseudo-index that represents the i-th upvalue of the running function (see §4.4).

##### lua_version #####

```c
const lua_Number *lua_version (lua_State *L);  /* [-0, +0, –] */
```

Returns the address of the version number (a C static variable) stored in the Lua core. When called with a valid lua_State, returns the address of the version used to create that state. When called with NULL, returns the address of the version running the call.

##### lua_Writer #####

```c

typedef int (*lua_Writer) (lua_State *L,
                           const void* p,
                           size_t sz,
                           void* ud);
```

The type of the writer function used by lua_dump. Every time it produces another piece of chunk, lua_dump calls the writer, passing along the buffer to be written (p), its size (sz), and the data parameter supplied to lua_dump.

The writer returns an error code: 0 means no errors; any other value means an error and stops lua_dump from calling the writer again.

##### lua_xmove #####

```c
void lua_xmove (lua_State *from, lua_State *to, int n); /* [-?, +?, –] */
```

Exchange values between different threads of the same state.

This function pops n values from the stack from, and pushes them onto the stack to.

##### lua_yield #####

```c
int lua_yield (lua_State *L, int nresults);  /* [-?, +?, e] */
```

This function is equivalent to lua_yieldk, but it has no continuation (see §4.7). Therefore, when the thread resumes, it continues the function that called the function calling lua_yield.

##### lua_yieldk #####

```c
int lua_yieldk (lua_State *L,
                int nresults,
                lua_KContext ctx,
                lua_KFunction k);  /* [-?, +?, e] */
```

Yields a coroutine (thread).

When a C function calls lua_yieldk, the running coroutine suspends its execution, and the call to lua_resume that started this coroutine returns. The parameter nresults is the number of values from the stack that will be passed as results to lua_resume.

When the coroutine is resumed again, Lua calls the given continuation function k to continue the execution of the C function that yielded (see §4.7). This continuation function receives the same stack from the previous function, with the n results removed and replaced by the arguments passed to lua_resume. Moreover, the continuation function receives the value ctx that was passed to lua_yieldk.

Usually, this function does not return; when the coroutine eventually resumes, it continues executing the continuation function. However, there is one special case, which is when this function is called from inside a line or a count hook (see §4.9). In that case, lua_yieldk should be called with no continuation (probably in the form of lua_yield) and no results, and the hook should return immediately after the call. Lua will yield and, when the coroutine resumes again, it will continue the normal execution of the (Lua) function that triggered the hook.

This function can raise an error if it is called from a thread with a pending C call with no continuation function, or it is called from a thread that is not running inside a resume (e.g., the main thread).
