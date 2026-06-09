# STL Simplifier Benchmark -- Difficult Hidden Test Cases

## Objective

These hidden cases evaluate AI-generated STL simplifier implementations.

## Evaluation Strategy

1.  Compile test
2.  Hidden exact-output tests
3.  Hidden negative tests
4.  Idempotence tests
5.  Semantic equivalence tests

------------------------------------------------------------------------

## 1. Merge → distribute → normalize → dedup

Input:

``` text
G[1,3](G[2,4](b & a)) | G[3,7](a & b)
```

Expected:

``` text
G[3,7](a) & G[3,7](b)
```

## 2. De Morgan duplicate canonical subtree

Input:

``` text
!((b & a) | (a & b))
```

Expected:

``` text
!a | !b
```

## 3. Nested distribution with temporal merge

Input:

``` text
G[0,1](G[2,3]((c & a) & (b & a)))
```

Expected:

``` text
G[2,4](a) & G[2,4](b) & G[2,4](c)
```

## 4. Mixed temporal operators

Input:

``` text
G[0,2](F[1,3](F[2,4](b | a)))
```

Expected:

``` text
G[0,2](F[3,7](a) | F[3,7](b))
```

## 5. Boolean duplication after temporal distribution

Input:

``` text
F[1,2]((b | a) | F[0,1](a | b))
```

Expected:

``` text
F[1,2](a) | F[1,2](b) | F[1,3](a) | F[1,3](b)
```

## 6. Hard precedence

Input:

``` text
!(a | b) | c & !(d | e)
```

Expected:

``` text
!a & !b | !d & !e & c
```

## 7. Canonical subtree equality

Input:

``` text
((c & b) & a) | (a & (b & c))
```

Expected:

``` text
a & b & c
```

## 8. Multiple negations

Input:

``` text
!!!((b | a) & c)
```

Expected:

``` text
!c | !a & !b
```

## 9. Structure-preserving temporal constants (optional)

Input:

``` text
G[0,5](true) & G[0,5](true)
```

Expected:

``` text
G[0,5](true)
```

## 10. Deep idempotence trap

Input:

``` text
G[0,1]((c & b) & (a & true)) | G[0,1](a & b & c)
```

Expected:

``` text
G[0,1](a) & G[0,1](b) & G[0,1](c)
```

Verify:

``` text
simplify(output) == output
```
