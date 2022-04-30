标准库的变化
==========

- The bit32 library has been deprecated. It is easy to require a compatible external library or, better yet, to replace its functions with appropriate bitwise operations. (Keep in mind that bit32 operates on 32-bit integers, while the bitwise operators in Lua 5.3 operate on Lua integers, which by default have 64 bits.)
- The Table library now respects metamethods for setting and getting elements.
- The ipairs iterator now respects metamethods and its __ipairs metamethod has been deprecated.
- Option names in io.read do not have a starting '*' anymore. For compatibility, Lua will continue to accept (and ignore) this character.
- The following functions were deprecated in the mathematical library: atan2, cosh, sinh, tanh, pow, frexp, and ldexp. You can replace math.pow(x,y) with x^y; you can replace math.atan2 with math.atan, which now accepts one or two arguments; you can replace math.ldexp(x,exp) with x * 2.0^exp. For the other operations, you can either use an external library or implement them in Lua.
- The searcher for C loaders used by require changed the way it handles versioned names. Now, the version should come after the module name (as is usual in most other tools). For compatibility, that searcher still tries the old format if it cannot find an open function according to the new style. (Lua 5.2 already worked that way, but it did not document the change.)
- The call collectgarbage("count") now returns only one result. (You can compute that second result from the fractional part of the first result.)
