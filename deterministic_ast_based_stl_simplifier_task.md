# Deterministic AST-Based STL Formula Simplifier

## Objective

Implement:

``` python
def simplify_stl(formula: str) -> str:
    ...
```

Parse STL into an AST, simplify using sound rewrite rules, normalize
into a deterministic canonical form, and return canonical STL text.

## Supported Syntax

``` text
Predicates: x > 0, y <= 5, speed < 10, a, b
Constants: true, false
Boolean: !phi, phi & psi, phi | psi
Temporal: G[a,b](phi), F[a,b](phi)
Parentheses: (phi)
```

## Required Rewrite Rules

``` text
!!phi -> phi

phi & true -> phi
true & phi -> phi
phi & false -> false
false & phi -> false

phi | false -> phi
false | phi -> phi
phi | true -> true
true | phi -> true

phi & phi -> phi
phi | phi -> phi

!(phi & psi) -> !phi | !psi
!(phi | psi) -> !phi & !psi

G[a,b](G[c,d](phi)) -> G[a+c,b+d](phi)
F[a,b](F[c,d](phi)) -> F[a+c,b+d](phi)

G[a,b](phi & psi) -> G[a,b](phi) & G[a,b](psi)
F[a,b](phi | psi) -> F[a,b](phi) | F[a,b](psi)

G[a,b](true) -> true
G[a,b](false) -> false
F[a,b](true) -> true
F[a,b](false) -> false
```

## Forbidden Rewrites

``` text
G[a,b](phi | psi) -> G[a,b](phi) | G[a,b](psi)
F[a,b](phi & psi) -> F[a,b](phi) & F[a,b](psi)

G[a,b](F[c,d](phi)) -> G[a+c,b+d](phi)
F[a,b](G[c,d](phi)) -> F[a+c,b+d](phi)
```

## Canonical Normalization

1.  Bottom-up rewriting to fixed point
2.  Rewrite priority: constants → negation → flatten → dedup → temporal
    merge → temporal distribute → sort → format
3.  Flatten associative operators
4.  Lexicographic operand sorting
5.  Deduplicate identical normalized subtrees
6.  Always distribute G over & and F over \|
7.  Operator precedence: !, temporal \> & \> \|
8.  Format intervals as `[a,b]`
9.  Preserve predicate internals except trimming whitespace
10. Idempotence:

``` python
simplify_stl(simplify_stl(phi)) == simplify_stl(phi)
```

## Evaluation

A correct solution must:

-   use AST rewriting rather than string replacement
-   simplify correctly
-   avoid unsound rewrites
-   produce deterministic output
-   preserve STL semantics
