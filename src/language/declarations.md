# Toplevel Declarations

The following declarations are available at the toplevel of a Duo module.

## Import statements

Other Duo modules can be imported using an `import` declaration. The following snippet imports the two modules `Function` and `I64`, which have to be declared in corresponding files `Function.duo` and `I64.duo`.

```
import Function;
import I64;
```

Circular module imports are not allowed and will generate an error. It is currently not possible to only import some of the declarations of a module, or to import a module qualified.

## Type synonyms

```
constructor True;                                                   
constructor False;                                                  
                                                                    
type SBool := < True, False >;  
```

## Type operators

Duo supports user-defined binary type operators with an associativity and precedence specified by the user.
For example, the function type and the `->` operator are specified in the following manner:

```
-- | The function type
codata Fun : (-a : CBV, +b : CBV) -> CBN {
    Ap(a,return b)
};

type operator -> rightassoc at 0 := Fun;
```

This first defines the codata type `Fun` and then introduces the arrow symbol as a custom notation for the `Fun` type with right associativity and precedence level 0.
Every data and codata type has to be introduced with a lexical identifier, `Fun` in this example, before a type operator can be introduced for the type.
The right-hand side of a type operator declaration expects the name of a typeconstructor with arity 2.

Nullary and unary type operators are currently not supported in Duo.

## Data and codata declarations

## Constructor and destructor declarations

## Producer declarations

## Consumer declarations

## Command declarations