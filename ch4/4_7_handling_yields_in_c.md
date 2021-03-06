Handling Yields in C
====================

Internally, Lua uses the C longjmp facility to yield a coroutine. Therefore, if a C function foo calls an API function and this API function yields (directly or indirectly by calling another function that yields), Lua cannot return to foo any more, because the longjmp removes its frame from the C stack.

To avoid this kind of problem, Lua raises an error whenever it tries to yield across an API call, except for three functions: lua_yieldk, lua_callk, and lua_pcallk. All those functions receive a continuation function (as a parameter named k) to continue execution after a yield.

We need to set some terminology to explain continuations. We have a C function called from Lua which we will call the original function. This original function then calls one of those three functions in the C API, which we will call the callee function, that then yields the current thread. (This can happen when the callee function is lua_yieldk, or when the callee function is either lua_callk or lua_pcallk and the function called by them yields.)

Suppose the running thread yields while executing the callee function. After the thread resumes, it eventually will finish running the callee function. However, the callee function cannot return to the original function, because its frame in the C stack was destroyed by the yield. Instead, Lua calls a continuation function, which was given as an argument to the callee function. As the name implies, the continuation function should continue the task of the original function.

As an illustration, consider the following function:

     int original_function (lua_State *L) {
       ...     /* code 1 */
       status = lua_pcall(L, n, m, h);  /* calls Lua */
       ...     /* code 2 */
     }
Now we want to allow the Lua code being run by lua_pcall to yield. First, we can rewrite our function like here:

     int k (lua_State *L, int status, lua_KContext ctx) {
       ...  /* code 2 */
     }

     int original_function (lua_State *L) {
       ...     /* code 1 */
       return k(L, lua_pcall(L, n, m, h), ctx);
     }
In the above code, the new function k is a continuation function (with type lua_KFunction), which should do all the work that the original function was doing after calling lua_pcall. Now, we must inform Lua that it must call k if the Lua code being executed by lua_pcall gets interrupted in some way (errors or yielding), so we rewrite the code as here, replacing lua_pcall by lua_pcallk:

     int original_function (lua_State *L) {
       ...     /* code 1 */
       return k(L, lua_pcallk(L, n, m, h, ctx2, k), ctx1);
     }
Note the external, explicit call to the continuation: Lua will call the continuation only if needed, that is, in case of errors or resuming after a yield. If the called function returns normally without ever yielding, lua_pcallk (and lua_callk) will also return normally. (Of course, instead of calling the continuation in that case, you can do the equivalent work directly inside the original function.)

Besides the Lua state, the continuation function has two other parameters: the final status of the call plus the context value (ctx) that was passed originally to lua_pcallk. (Lua does not use this context value; it only passes this value from the original function to the continuation function.) For lua_pcallk, the status is the same value that would be returned by lua_pcallk, except that it is LUA_YIELD when being executed after a yield (instead of LUA_OK). For lua_yieldk and lua_callk, the status is always LUA_YIELD when Lua calls the continuation. (For these two functions, Lua will not call the continuation in case of errors, because they do not handle errors.) Similarly, when using lua_callk, you should call the continuation function with LUA_OK as the status. (For lua_yieldk, there is not much point in calling directly the continuation function, because lua_yieldk usually does not return.)

Lua treats the continuation function as if it were the original function. The continuation function receives the same Lua stack from the original function, in the same state it would be if the callee function had returned. (For instance, after a lua_callk the function and its arguments are removed from the stack and replaced by the results from the call.) It also has the same upvalues. Whatever it returns is handled by Lua as if it were the return of the original function.
