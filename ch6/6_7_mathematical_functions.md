Mathematical Functions
======================

This library provides basic mathematical functions. It provides all its functions and constants inside the table math. Functions with the annotation "integer/float" give integer results for integer arguments and float results for float (or mixed) arguments. Rounding functions (math.ceil, math.floor, and math.modf) return an integer when the result fits in the range of an integer, or a float otherwise.

math.abs (x)
Returns the absolute value of x. (integer/float)

math.acos (x)
Returns the arc cosine of x (in radians).

math.asin (x)
Returns the arc sine of x (in radians).

math.atan (y [, x])
Returns the arc tangent of y/x (in radians), but uses the signs of both arguments to find the quadrant of the result. (It also handles correctly the case of x being zero.)

The default value for x is 1, so that the call math.atan(y) returns the arc tangent of y.

math.ceil (x)
Returns the smallest integral value larger than or equal to x.

math.cos (x)
Returns the cosine of x (assumed to be in radians).

math.deg (x)
Converts the angle x from radians to degrees.

math.exp (x)
Returns the value ex (where e is the base of natural logarithms).

math.floor (x)
Returns the largest integral value smaller than or equal to x.

math.fmod (x, y)
Returns the remainder of the division of x by y that rounds the quotient towards zero. (integer/float)

math.huge
The float value HUGE_VAL, a value larger than any other numeric value.

math.log (x [, base])
Returns the logarithm of x in the given base. The default for base is e (so that the function returns the natural logarithm of x).

math.max (x, ···)
Returns the argument with the maximum value, according to the Lua operator <. (integer/float)

math.maxinteger
An integer with the maximum value for an integer.
math.min (x, ···)
Returns the argument with the minimum value, according to the Lua operator <. (integer/float)

math.mininteger
An integer with the minimum value for an integer.
math.modf (x)
Returns the integral part of x and the fractional part of x. Its second result is always a float.

math.pi
The value of π.

math.rad (x)
Converts the angle x from degrees to radians.

math.random ([m [, n]])
When called without arguments, returns a pseudo-random float with uniform distribution in the range [0,1). When called with two integers m and n, math.random returns a pseudo-random integer with uniform distribution in the range [m, n]. (The value n-m cannot be negative and must fit in a Lua integer.) The call math.random(n) is equivalent to math.random(1,n).

This function is an interface to the underling pseudo-random generator function provided by C.

math.randomseed (x)
Sets x as the "seed" for the pseudo-random generator: equal seeds produce equal sequences of numbers.

math.sin (x)
Returns the sine of x (assumed to be in radians).

math.sqrt (x)
Returns the square root of x. (You can also use the expression x^0.5 to compute this value.)

math.tan (x)
Returns the tangent of x (assumed to be in radians).

math.tointeger (x)
If the value x is convertible to an integer, returns that integer. Otherwise, returns nil.

math.type (x)
Returns "integer" if x is an integer, "float" if it is a float, or nil if x is not a number.

math.ult (m, n)
Returns a boolean, true if and only if integer m is below integer n when they are compared as unsigned integers.
