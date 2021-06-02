# SparseArraysN

[![Build Status](https://github.com/jlapeyre/SparseArraysN.jl/workflows/CI/badge.svg)](https://github.com/jlapeyre/SparseArraysN.jl/actions)
[![Coverage](https://codecov.io/gh/jlapeyre/SparseArraysN.jl/branch/master/graph/badge.svg)](https://codecov.io/gh/jlapeyre/SparseArraysN.jl)

This package is a copy of `SparseArrays` from the Julia standard library, modified slightly to
accomodate types for which we would like the non-stored element of a type `T` to be a value other than
`zero(T)`.

Only `SparseVector`s have been modified to support values other than `zero(T)`.

## Motivation
The motivation is to support applications for which the non-stored element is `one(T)`. Large
amounts of code, especially linear algebra functions, will not give correct results for
non-stored values other than `zero(T)`. However, there is a signficant amount of code
that does work. And it is very convnient to have this tested, debugged, and possibly optimized
code available in a coherent design when one needs to use `one(T)` as the non-stored value.

## Implementation
We replace relevant occurrences of `zero`, `iszero`, and `zeros`, with `neutral`, `isneutral` and `neutrals`.
These functions have methods for type `Any` as follows
```julia
neutral(x) = zero(x)
isneutral(x) = iszero(x)
neutrals(::Type{T}, args...) where T = zeros(T, args...)
```
Thus, for any type that does not define methods for `neutral`, etc. the behavior of `SparseVector` is unchanged.
Indeed, the test suite for `SparseVector` passes with these changes.

## Tests

This repo does not currently have any tests for types defining other methods for `neutral`, etc.
The tests are in application code. Of course, some toy application should be tested here.

## Design

It would probably be better to use some kind of trait system, so that application code defines one
method on a type, which in turn gives the correct behavior for all of `neutral`, `isneutral`, and `neutrals`.

## Name

It might be better to call this package `SparseArrays`. I called it `SparseArraysN` so that it is easier to
distinguish in code, but also mainly as an attempt (didn't work) to quiet (by fixing) warnings about redefined methods.
I think the `SparseArrays` library commits type piracy, in that it defines methods for functions
introduced outside of `SparseArrays` on types introduced outside of `SparseArrays`. And `SparseArrays` is
compiled into the standard Julia image. This should probably be fixed in the standard library version.

