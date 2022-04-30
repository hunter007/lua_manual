API的变化
========

- Continuation functions now receive as arguments what they needed to get through lua_getctx, so lua_getctx has been removed. Adapt your code accordingly.
- Function lua_dump has an extra parameter, strip. Use 0 as the value of this parameter to get the old behavior.
- Functions to inject/project unsigned integers (lua_pushunsigned, lua_tounsigned, lua_tounsignedx, luaL_checkunsigned, luaL_optunsigned) were deprecated. Use their signed equivalents with a type cast.
- Macros to project non-default integer types (luaL_checkint, luaL_optint, luaL_checklong, luaL_optlong) were deprecated. Use their equivalent over lua_Integer with a type cast (or, when possible, use lua_Integer in your code).
