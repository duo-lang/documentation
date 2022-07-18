# Types

## Kinds and Evaluation Order

Most languages choose one evaluation strategy.
Strict languages like OCaml, SML or F# choose a call-by-value (CBV) evaluation strategy, whereas non-strict languages choose either
call-by-name (CBN) or call-by-need.
Duo is not comitted to either strategy.
Instead, the user can choose between two different evaluation strategies for each type individually.
The two evaluation strategies are:

| Evaluation Order | Explanation    |
|------------------|----------------|
| CBV              | Call-by-value  |
| CBN              | Call-by-name   |

The different evaluation strategies are reflected in the kinds of types.
Instead of having only one inhabitated kind, usually written `*` in the type theory literature, there are several different kinds which classify inhabitated types.
These inhabitated kinds are called "MonoKind". Currently there are four different MonoKinds, one for each of boxed values of CBV and CBN types, and one for each of the primitive unboxed types.

| MonoKind         | Explanation                       |
|------------------|-----------------------------------|
| CBV              | The kind of boxed CBV types       |
| CBN              | The kind of boxed CBN types       |
| I64Rep           | The kind of the unboxed #I64 type | 
| F64Rep           | The kind of the unboxed #F64 type |

Higher kinds are the kinds given to type constructors.
Since the only available type constructors in Duo construct CBV or CBN types, they follow the following restricted grammar.
Each argument in a PolyKind has a variance, either `+` for covariant arguments or `-` for contravariant arguments, a type variable `a`, and a
MonoKind of type type variable, `mk`.

| PolyKind          | Explanation                       |
|-------------------|-----------------------------------|
| (+-a : mk)* -> eo | Polykind                          |
| CBV               | Syntactic sugar for () -> CBV     |
| CBN               | Syntactic sugar for () -> CBN     |

## Builtin Types

There are two builtin types which correspond to numeric types supported by most modern architectures.
The builtin types are given in the following table.

| Type       | Kind    | Explanation                                     |
|------------|---------|-------------------------------------------------|
| #I64       | I64Rep  | Unboxed signed 64-Bit integers                  |
| #F64       | F64Rep  | Unboxed 64-Bit precision floating point numbers |

Each Builtin type has its own kind, which reflects the fact that these types are unboxed.
Since they are unboxed, it is in general not possible to apply a polymorphic function to an argument of a builtin type.
The standard library provides wrappers in the `I64.ds` and `F64.ds` modules which provide the boxed variants of the builtin unboxed types.

## Nominal Types

Duo has two different variants of nominal types; nominal data types and nominal codata types.

#### Nominal Data Types

Nominal data types are declared using the `data` keyword.
For example, the following two declarations declare Booleans and Peano natural numbers, respectively.

```
data Bool : CBV { True, False };

data Nat : CBV { Zero, Succ(Nat) };
```

Note that we have to indicate the evaluation order we want to use for that type.
We can also use nominal data types to declare the unit and void type.

```
data Unit : CBV { MkUnit };
data Void : CBV {};
```

When we declare parameterized data types, we have to declare both the kind of the type parameter, and its variance.
For example, we can declare the type of lists of CBV types.

```
data List : (+a : CBV) -> CBV {
    Nil,
    Cons(a, List(a))
};
```

In order for the algebraic subtyping approach to work, all type parameters have to be either covariant or contravariant.
It currently isn't possible to use invariant type parameters, but such a feature might be added as syntactic sugar at a future point.

#### Nominal Codata Types

Codata types are introduced using the `codata` keyword.

One of the standard examples of a codata type is the example of an infinite stream of natural numbers, called `NatStream`.
A stream has two possible observations, `Head` and `Tail`, which we call the destructors of the codata type.
The destructor `Head` returns the first element of the stream, and the destructor `Tail` returns the remainder of the stream.

```
codata NatStream : CBN {
    Head[Nat], 
    Tail[NatStream]
};
```

Codata types can also be parameterized, similar to the List example of the previous section.

```
codata Stream : (+a : CBV) -> CBN {
    Head[a],
    Tail[Stream(a)]
};
```

Codata types make it possible to define the function type, instead of having a builtin function type like most functional programming languages.
The declaration of the function type looks like this.

```
codata Fun : (-a : CBV, +b CBV) -> CBN {
    Ap(a)[b]
}
```

##### Restriction on lattice types in arguments

It is not possible to use lattice types in the arguments of constructors or destructors of data and codata types.
For example, the following type is not allowed:

```
data Foo :  CBN {
    Bar(Top)
}; 
```

Without this restriction, lattice types will occur in positions in which they are not allowed.
For the example above, `Top` occurs in a negative position in the typing rule for the constructor:

```
Gamma |- e :prd tau     tau <: Top
------------------------------------T-Ctor
Gamma |- Bar(e) :prd Foo
```

but in a positive position in the typing rule for the destructor:

```
Gamma, x : Top |- c :cmd
--------------------------------------- T-Match
Gamma |- case { Bar(x) => c } :cns Foo
```

## Structural Types

Nominal data and codata types define a new type together with all of its constructors (resp. destructors).
But sometimes it is more useful to have a more structural type, where we can introduce new constructors and destructors at arbitrary places in the program.
These types are often called extensible variants and records, and Duo supports them in the form of structural data and codata types.

Structural data types are similar to polymorphic variants in, for example, OCaml.
Structural codata types are similar to extensible records in, for example, Purescript.
In distinction to those systems, constructors and destructors of structural data and codata types have to be declared in Duo before they can be used.
This approach has the following benefits:

- It becomes possible to identify typos when using structural data and codata.
- The structural constructors and destructors can be documented using a docstring, which can be shown on hover.

#### Structural Data Types

A constructor of a structural data type is introduced with a `constructor` declaration.

```
constructor True : CBV;
```

When occuring in types, structural data types are written using angle brackets.
The structural encoding of booleans, for example, looks like this:
```
< True, False >
```
whereas the structural encoding of Peano natural numbers looks like this

```
mu a. < Z , S(a)>
```


#### Structural Codata Types

A destructor of a structural codata type is introduced with a `destructor` declaration.

```
destructor Ap(CBV)[CBN] : CBN;
```

Structural codata types are written using braces. The structural encoding of NatStreams, for example, are written as follows:

```
mu a. { Head[Nat], Tail[a] }
```

## Structural Refinement Types

Structural refinement types are a novel idea in Duo which combine features from both nominal and structural types.
They are declared similarly to nominal types, but use the additional keyword `refinement` in front of the declaration

```
refinement data Bool : CBV { True, False };
refinement codata NatStream : CBN {
    Head[Nat],
    Tail[NatStream]
};
```

The syntax for refinement types is very similar to the syntax for structural data and codata types, but the refined type is indicated by a vertical pipe in front of the constructors/destructors.
The four possible refinements of the boolean type, for example, are written like this:
```
< Bool | True, False >
< Bool | True >
< Bool | False >
< Bool | >
```

And a refinement of the codata type of NatStream is written like this:

```
{ NatStream | Head[Nat] }
```

## Lattice Types

Duo uses subtyping, and some of the operations of the subtyping lattice are available in the surface syntax.
In particular, unions and intersections, as well as the top and bottom elements of the subtyping lattice can be used in types.

| Type   | Explanation                                 |
|--------|---------------------------------------------|
| Top    | The top element of the subtyping lattice    |
| Bot    | The bottom element of the subtyping lattice |
| S \\/ T | The union of S and T                        |  
| S /\ T | The intersection of S and T                 |
