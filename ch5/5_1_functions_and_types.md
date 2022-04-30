函数和类型
=========

这里我们列出辅助库中所有函数和类型（字母顺序）

luaL_addchar
[-?, +?, m]
void luaL_addchar (luaL_Buffer *B, char c);
把字节C放入 buffer B中 (查看 luaL_Buffer)

luaL_addlstring
[-?, +?, m]
void luaL_addlstring (luaL_Buffer *B, const char *s, size_t l);
Adds the string pointed to by s with length l to the buffer B (see luaL_Buffer). The string can contain embedded zeros.

luaL_addsize
[-?, +?, –]
void luaL_addsize (luaL_Buffer *B, size_t n);
Adds to the buffer B (see luaL_Buffer) a string of length n previously copied to the buffer area (see luaL_prepbuffer).

luaL_addstring
[-?, +?, m]
void luaL_addstring (luaL_Buffer *B, const char *s);
Adds the zero-terminated string pointed to by s to the buffer B (see luaL_Buffer).

luaL_addvalue
[-1, +?, m]
void luaL_addvalue (luaL_Buffer *B);
Adds the value at the top of the stack to the buffer B (see luaL_Buffer). Pops the value.

This is the only function on string buffers that can (and must) be called with an extra element on the stack, which is the value to be added to the buffer.

luaL_argcheck
[-0, +0, v]
void luaL_argcheck (lua_State *L,
                    int cond,
                    int arg,
                    const char *extramsg);
Checks whether cond is true. If it is not, raises an error with a standard message (see luaL_argerror).

luaL_argerror
[-0, +0, v]
int luaL_argerror (lua_State *L, int arg, const char *extramsg);
Raises an error reporting a problem with argument arg of the C function that called it, using a standard message that includes extramsg as a comment:

     bad argument #arg to 'funcname' (extramsg)
This function never returns.

luaL_Buffer
typedef struct luaL_Buffer luaL_Buffer;
Type for a string buffer.

A string buffer allows C code to build Lua strings piecemeal. Its pattern of use is as follows:

First declare a variable b of type luaL_Buffer.
Then initialize it with a call luaL_buffinit(L, &b).
Then add string pieces to the buffer calling any of the luaL_add* functions.
Finish by calling luaL_pushresult(&b). This call leaves the final string on the top of the stack.
If you know beforehand the total size of the resulting string, you can use the buffer like this:

First declare a variable b of type luaL_Buffer.
Then initialize it and preallocate a space of size sz with a call luaL_buffinitsize(L, &b, sz).
Then copy the string into that space.
Finish by calling luaL_pushresultsize(&b, sz), where sz is the total size of the resulting string copied into that space.
During its normal operation, a string buffer uses a variable number of stack slots. So, while using a buffer, you cannot assume that you know where the top of the stack is. You can use the stack between successive calls to buffer operations as long as that use is balanced; that is, when you call a buffer operation, the stack is at the same level it was immediately after the previous buffer operation. (The only exception to this rule is luaL_addvalue.) After calling luaL_pushresult the stack is back to its level when the buffer was initialized, plus the final string on its top.

luaL_buffinit
[-0, +0, –]
void luaL_buffinit (lua_State *L, luaL_Buffer *B);
Initializes a buffer B. This function does not allocate any space; the buffer must be declared as a variable (see luaL_Buffer).

luaL_buffinitsize
[-?, +?, m]
char *luaL_buffinitsize (lua_State *L, luaL_Buffer *B, size_t sz);
Equivalent to the sequence luaL_buffinit, luaL_prepbuffsize.

luaL_callmeta
[-0, +(0|1), e]
int luaL_callmeta (lua_State *L, int obj, const char *e);
Calls a metamethod.

If the object at index obj has a metatable and this metatable has a field e, this function calls this field passing the object as its only argument. In this case this function returns true and pushes onto the stack the value returned by the call. If there is no metatable or no metamethod, this function returns false (without pushing any value on the stack).

luaL_checkany
[-0, +0, v]
void luaL_checkany (lua_State *L, int arg);
Checks whether the function has an argument of any type (including nil) at position arg.

luaL_checkinteger
[-0, +0, v]
lua_Integer luaL_checkinteger (lua_State *L, int arg);
Checks whether the function argument arg is an integer (or can be converted to an integer) and returns this integer cast to a lua_Integer.

luaL_checklstring
[-0, +0, v]
const char *luaL_checklstring (lua_State *L, int arg, size_t *l);
Checks whether the function argument arg is a string and returns this string; if l is not NULL fills *l with the string's length.

This function uses lua_tolstring to get its result, so all conversions and caveats of that function apply here.

luaL_checknumber
[-0, +0, v]
lua_Number luaL_checknumber (lua_State *L, int arg);
Checks whether the function argument arg is a number and returns this number.

luaL_checkoption
[-0, +0, v]
int luaL_checkoption (lua_State *L,
                      int arg,
                      const char *def,
                      const char *const lst[]);
Checks whether the function argument arg is a string and searches for this string in the array lst (which must be NULL-terminated). Returns the index in the array where the string was found. Raises an error if the argument is not a string or if the string cannot be found.

If def is not NULL, the function uses def as a default value when there is no argument arg or when this argument is nil.

This is a useful function for mapping strings to C enums. (The usual convention in Lua libraries is to use strings instead of numbers to select options.)

luaL_checkstack
[-0, +0, v]
void luaL_checkstack (lua_State *L, int sz, const char *msg);
Grows the stack size to top + sz elements, raising an error if the stack cannot grow to that size. msg is an additional text to go into the error message (or NULL for no additional text).

luaL_checkstring
[-0, +0, v]
const char *luaL_checkstring (lua_State *L, int arg);
Checks whether the function argument arg is a string and returns this string.

This function uses lua_tolstring to get its result, so all conversions and caveats of that function apply here.

luaL_checktype
[-0, +0, v]
void luaL_checktype (lua_State *L, int arg, int t);
Checks whether the function argument arg has type t. See lua_type for the encoding of types for t.

luaL_checkudata
[-0, +0, v]
void *luaL_checkudata (lua_State *L, int arg, const char *tname);
Checks whether the function argument arg is a userdata of the type tname (see luaL_newmetatable) and returns the userdata address (see lua_touserdata).

luaL_checkversion
[-0, +0, v]
void luaL_checkversion (lua_State *L);
Checks whether the core running the call, the core that created the Lua state, and the code making the call are all using the same version of Lua. Also checks whether the core running the call and the core that created the Lua state are using the same address space.

luaL_dofile
[-0, +?, e]
int luaL_dofile (lua_State *L, const char *filename);
Loads and runs the given file. It is defined as the following macro:

     (luaL_loadfile(L, filename) || lua_pcall(L, 0, LUA_MULTRET, 0))
It returns false if there are no errors or true in case of errors.

luaL_dostring
[-0, +?, –]
int luaL_dostring (lua_State *L, const char *str);
Loads and runs the given string. It is defined as the following macro:

     (luaL_loadstring(L, str) || lua_pcall(L, 0, LUA_MULTRET, 0))
It returns false if there are no errors or true in case of errors.

luaL_error
[-0, +0, v]
int luaL_error (lua_State *L, const char *fmt, ...);
Raises an error. The error message format is given by fmt plus any extra arguments, following the same rules of lua_pushfstring. It also adds at the beginning of the message the file name and the line number where the error occurred, if this information is available.

This function never returns, but it is an idiom to use it in C functions as return luaL_error(args).

luaL_execresult
[-0, +3, m]
int luaL_execresult (lua_State *L, int stat);
This function produces the return values for process-related functions in the standard library (os.execute and io.close).

luaL_fileresult
[-0, +(1|3), m]
int luaL_fileresult (lua_State *L, int stat, const char *fname);
This function produces the return values for file-related functions in the standard library (io.open, os.rename, file:seek, etc.).

luaL_getmetafield
[-0, +(0|1), m]
int luaL_getmetafield (lua_State *L, int obj, const char *e);
Pushes onto the stack the field e from the metatable of the object at index obj and returns the type of the pushed value. If the object does not have a metatable, or if the metatable does not have this field, pushes nothing and returns LUA_TNIL.

luaL_getmetatable
[-0, +1, m]
int luaL_getmetatable (lua_State *L, const char *tname);
Pushes onto the stack the metatable associated with name tname in the registry (see luaL_newmetatable) (nil if there is no metatable associated with that name). Returns the type of the pushed value.

luaL_getsubtable
[-0, +1, e]
int luaL_getsubtable (lua_State *L, int idx, const char *fname);
Ensures that the value t[fname], where t is the value at index idx, is a table, and pushes that table onto the stack. Returns true if it finds a previous table there and false if it creates a new table.

luaL_gsub
[-0, +1, m]
const char *luaL_gsub (lua_State *L,
                       const char *s,
                       const char *p,
                       const char *r);
Creates a copy of string s by replacing any occurrence of the string p with the string r. Pushes the resulting string on the stack and returns it.

luaL_len
[-0, +0, e]
lua_Integer luaL_len (lua_State *L, int index);
Returns the "length" of the value at the given index as a number; it is equivalent to the '#' operator in Lua (see §3.4.7). Raises an error if the result of the operation is not an integer. (This case only can happen through metamethods.)

luaL_loadbuffer
[-0, +1, –]
int luaL_loadbuffer (lua_State *L,
                     const char *buff,
                     size_t sz,
                     const char *name);
Equivalent to luaL_loadbufferx with mode equal to NULL.

luaL_loadbufferx
[-0, +1, –]
int luaL_loadbufferx (lua_State *L,
                      const char *buff,
                      size_t sz,
                      const char *name,
                      const char *mode);
Loads a buffer as a Lua chunk. This function uses lua_load to load the chunk in the buffer pointed to by buff with size sz.

This function returns the same results as lua_load. name is the chunk name, used for debug information and error messages. The string mode works as in function lua_load.

luaL_loadfile
[-0, +1, m]
int luaL_loadfile (lua_State *L, const char *filename);
Equivalent to luaL_loadfilex with mode equal to NULL.

luaL_loadfilex
[-0, +1, m]
int luaL_loadfilex (lua_State *L, const char *filename,
                                            const char *mode);
Loads a file as a Lua chunk. This function uses lua_load to load the chunk in the file named filename. If filename is NULL, then it loads from the standard input. The first line in the file is ignored if it starts with a #.

The string mode works as in function lua_load.

This function returns the same results as lua_load, but it has an extra error code LUA_ERRFILE for file-related errors (e.g., it cannot open or read the file).

As lua_load, this function only loads the chunk; it does not run it.

luaL_loadstring
[-0, +1, –]
int luaL_loadstring (lua_State *L, const char *s);
Loads a string as a Lua chunk. This function uses lua_load to load the chunk in the zero-terminated string s.

This function returns the same results as lua_load.

Also as lua_load, this function only loads the chunk; it does not run it.

luaL_newlib
[-0, +1, m]
void luaL_newlib (lua_State *L, const luaL_Reg l[]);
Creates a new table and registers there the functions in list l.

It is implemented as the following macro:

     (luaL_newlibtable(L,l), luaL_setfuncs(L,l,0))
The array l must be the actual array, not a pointer to it.

luaL_newlibtable
[-0, +1, m]
void luaL_newlibtable (lua_State *L, const luaL_Reg l[]);
Creates a new table with a size optimized to store all entries in the array l (but does not actually store them). It is intended to be used in conjunction with luaL_setfuncs (see luaL_newlib).

It is implemented as a macro. The array l must be the actual array, not a pointer to it.

luaL_newmetatable
[-0, +1, m]
int luaL_newmetatable (lua_State *L, const char *tname);
If the registry already has the key tname, returns 0. Otherwise, creates a new table to be used as a metatable for userdata, adds to this new table the pair __name = tname, adds to the registry the pair [tname] = new table, and returns 1. (The entry __name is used by some error-reporting functions.)

In both cases pushes onto the stack the final value associated with tname in the registry.

luaL_newstate
[-0, +0, –]
lua_State *luaL_newstate (void);
Creates a new Lua state. It calls lua_newstate with an allocator based on the standard C realloc function and then sets a panic function (see §4.6) that prints an error message to the standard error output in case of fatal errors.

Returns the new state, or NULL if there is a memory allocation error.

luaL_openlibs
[-0, +0, e]
void luaL_openlibs (lua_State *L);
Opens all standard Lua libraries into the given state.

luaL_opt
[-0, +0, e]
T luaL_opt (L, func, arg, dflt);
This macro is defined as follows:

     (lua_isnoneornil(L,(arg)) ? (dflt) : func(L,(arg)))
In words, if the argument arg is nil or absent, the macro results in the default dflt. Otherwise, it results in the result of calling func with the state L and the argument index arg as arguments. Note that it evaluates the expression dflt only if needed.

luaL_optinteger
[-0, +0, v]
lua_Integer luaL_optinteger (lua_State *L,
                             int arg,
                             lua_Integer d);
If the function argument arg is an integer (or convertible to an integer), returns this integer. If this argument is absent or is nil, returns d. Otherwise, raises an error.

luaL_optlstring
[-0, +0, v]
const char *luaL_optlstring (lua_State *L,
                             int arg,
                             const char *d,
                             size_t *l);
If the function argument arg is a string, returns this string. If this argument is absent or is nil, returns d. Otherwise, raises an error.

If l is not NULL, fills the position *l with the result's length. If the result is NULL (only possible when returning d and d == NULL), its length is considered zero.

This function uses lua_tolstring to get its result, so all conversions and caveats of that function apply here.

luaL_optnumber
[-0, +0, v]
lua_Number luaL_optnumber (lua_State *L, int arg, lua_Number d);
If the function argument arg is a number, returns this number. If this argument is absent or is nil, returns d. Otherwise, raises an error.

luaL_optstring
[-0, +0, v]
const char *luaL_optstring (lua_State *L,
                            int arg,
                            const char *d);
If the function argument arg is a string, returns this string. If this argument is absent or is nil, returns d. Otherwise, raises an error.

luaL_prepbuffer
[-?, +?, m]
char *luaL_prepbuffer (luaL_Buffer *B);
Equivalent to luaL_prepbuffsize with the predefined size LUAL_BUFFERSIZE.

luaL_prepbuffsize
[-?, +?, m]
char *luaL_prepbuffsize (luaL_Buffer *B, size_t sz);
Returns an address to a space of size sz where you can copy a string to be added to buffer B (see luaL_Buffer). After copying the string into this space you must call luaL_addsize with the size of the string to actually add it to the buffer.

luaL_pushresult
[-?, +1, m]
void luaL_pushresult (luaL_Buffer *B);
Finishes the use of buffer B leaving the final string on the top of the stack.

luaL_pushresultsize
[-?, +1, m]
void luaL_pushresultsize (luaL_Buffer *B, size_t sz);
Equivalent to the sequence luaL_addsize, luaL_pushresult.

luaL_ref
[-1, +0, m]
int luaL_ref (lua_State *L, int t);
Creates and returns a reference, in the table at index t, for the object at the top of the stack (and pops the object).

A reference is a unique integer key. As long as you do not manually add integer keys into table t, luaL_ref ensures the uniqueness of the key it returns. You can retrieve an object referred by reference r by calling lua_rawgeti(L, t, r). Function luaL_unref frees a reference and its associated object.

If the object at the top of the stack is nil, luaL_ref returns the constant LUA_REFNIL. The constant LUA_NOREF is guaranteed to be different from any reference returned by luaL_ref.

luaL_Reg
typedef struct luaL_Reg {
  const char *name;
  lua_CFunction func;
} luaL_Reg;
Type for arrays of functions to be registered by luaL_setfuncs. name is the function name and func is a pointer to the function. Any array of luaL_Reg must end with a sentinel entry in which both name and func are NULL.

luaL_requiref
[-0, +1, e]
void luaL_requiref (lua_State *L, const char *modname,
                    lua_CFunction openf, int glb);
If modname is not already present in package.loaded, calls function openf with string modname as an argument and sets the call result in package.loaded[modname], as if that function has been called through require.

If glb is true, also stores the module into global modname.

Leaves a copy of the module on the stack.

luaL_setfuncs
[-nup, +0, m]
void luaL_setfuncs (lua_State *L, const luaL_Reg *l, int nup);
Registers all functions in the array l (see luaL_Reg) into the table on the top of the stack (below optional upvalues, see next).

When nup is not zero, all functions are created sharing nup upvalues, which must be previously pushed on the stack on top of the library table. These values are popped from the stack after the registration.

luaL_setmetatable
[-0, +0, –]
void luaL_setmetatable (lua_State *L, const char *tname);
Sets the metatable of the object at the top of the stack as the metatable associated with name tname in the registry (see luaL_newmetatable).

luaL_Stream
typedef struct luaL_Stream {
  FILE *f;
  lua_CFunction closef;
} luaL_Stream;
The standard representation for file handles, which is used by the standard I/O library.

A file handle is implemented as a full userdata, with a metatable called LUA_FILEHANDLE (where LUA_FILEHANDLE is a macro with the actual metatable's name). The metatable is created by the I/O library (see luaL_newmetatable).

This userdata must start with the structure luaL_Stream; it can contain other data after this initial structure. Field f points to the corresponding C stream (or it can be NULL to indicate an incompletely created handle). Field closef points to a Lua function that will be called to close the stream when the handle is closed or collected; this function receives the file handle as its sole argument and must return either true (in case of success) or nil plus an error message (in case of error). Once Lua calls this field, it changes the field value to NULL to signal that the handle is closed.

luaL_testudata
[-0, +0, m]
void *luaL_testudata (lua_State *L, int arg, const char *tname);
This function works like luaL_checkudata, except that, when the test fails, it returns NULL instead of raising an error.

luaL_tolstring
[-0, +1, e]
const char *luaL_tolstring (lua_State *L, int idx, size_t *len);
Converts any Lua value at the given index to a C string in a reasonable format. The resulting string is pushed onto the stack and also returned by the function. If len is not NULL, the function also sets *len with the string length.

If the value has a metatable with a __tostring field, then luaL_tolstring calls the corresponding metamethod with the value as argument, and uses the result of the call as its result.

luaL_traceback
[-0, +1, m]
void luaL_traceback (lua_State *L, lua_State *L1, const char *msg,
                     int level);
Creates and pushes a traceback of the stack L1. If msg is not NULL it is appended at the beginning of the traceback. The level parameter tells at which level to start the traceback.

luaL_typename
[-0, +0, –]
const char *luaL_typename (lua_State *L, int index);
Returns the name of the type of the value at the given index.

luaL_unref
[-0, +0, –]
void luaL_unref (lua_State *L, int t, int ref);
Releases reference ref from the table at index t (see luaL_ref). The entry is removed from the table, so that the referred object can be collected. The reference ref is also freed to be used again.

If ref is LUA_NOREF or LUA_REFNIL, luaL_unref does nothing.

luaL_where
[-0, +1, m]
void luaL_where (lua_State *L, int lvl);
Pushes onto the stack a string identifying the current position of the control at level lvl in the call stack. Typically this string has the following format:

     chunkname:currentline:
Level 0 is the running function, level 1 is the function that called the running function, etc.

This function is used to build a prefix for error messages.
