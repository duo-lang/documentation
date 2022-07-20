# Kinds

Most languages choose one evaluation strategy and use that strategy globally.
Strict languages like OCaml, SML or F# choose a call-by-value (CBV) evaluation strategy, whereas non-strict languages choose either call-by-name (CBN) or call-by-need.
Instead of comitting to a global evaluation strategy, the evaluation strategy in Duo is specified for every type individually, and this choice is then reflected in the kind of that type.
It is possible, for example, to have both strict booleans and lazy booleans within the same language.
The difference between strict booleans and lazy booleans is then recorded on the level of kinds.
For strict booleans we would have `StrictBool : CBV`, and for lazy booleans `LazyBool : CBN`, where `CBV` and `CBN` replace the single kind `*` of inhabitated types that other languages have.

## Evaluation Orders

Duo currently supports two different evaluation strategies.
The two evaluation strategies are call-by-value and call-by-name.

| Evaluation Order | Explanation    |
|------------------|----------------|
| CBV              | Call-by-value  |
| CBN              | Call-by-name   |


## Inhabitated Kinds

The different evaluation strategies are reflected in the kinds of types.
Instead of having only one inhabitated kind, usually written `*` in the type theory literature, there are several different kinds which classify inhabitated types.
These inhabitated kinds are called "MonoKind". Currently there are four different MonoKinds, one for each of boxed values of CBV and CBN types, and one for each of the primitive unboxed types.

| MonoKind         | Explanation                       |
|------------------|-----------------------------------|
| CBV              | The kind of boxed CBV types       |
| CBN              | The kind of boxed CBN types       |
| I64Rep           | The kind of the unboxed #I64 type | 
| F64Rep           | The kind of the unboxed #F64 type |


## Higher Kinds

Higher kinds are the kinds given to type constructors.
Since the only available type constructors in Duo construct CBV or CBN types, they follow the following restricted grammar.
Each argument in a PolyKind has a variance, either `+` for covariant arguments or `-` for contravariant arguments, a type variable `a`, and a
MonoKind of type type variable, `mk`.

| PolyKind          | Explanation                       |
|-------------------|-----------------------------------|
| (+-a : mk)* -> eo | Polykind                          |
| CBV               | Syntactic sugar for () -> CBV     |
| CBN               | Syntactic sugar for () -> CBN     |

