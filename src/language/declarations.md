# Toplevel Declarations

The following declarations are available at the toplevel of a Duo module.

## Import statements

Other Duo modules can be imported using an `import` declaration.
The following snippet imports the two modules `Function` and `I64`, which have to be declared in corresponding files `Function.duo` and `I64.duo`.
These files have to be available in the library search path.

```
import Function;
import I64;
```

Circular module imports are not allowed and will generate an error. It is currently not possible to only import some of the declarations of a module, or to import a module qualified.

## Type synonyms

Type synonyms can be defined using the `type` keyword.
In the following snippet, a type synonym `SBool` is defined as an alias for the structural type `< True, False >`. For the typechecker the two types `SBool` and `< True, False >` are indistinguishable.

```
constructor True;                                                   
constructor False;                                                  
                                                                    
type SBool := < True, False >;  
```
Parameterized type synonyms are currently not yet implemented.

## Type operators

Duo supports binary type operators with user-specified associativity and precedence.
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
Nullary and unary type operators are currently not implemented.

## Data and codata declarations

## Constructor and destructor declarations for structural types

Structural types, i.e. polymorphic variants and structural records, require their respective constructors and destructors to be declared at the toplevel before they can be used.
This is different to, for example, OCaml, where a backtick in front of the name of the constructor is used.
This design was chosen for the following reasons:

- Docstrings can be attached to the constructors at the declaration site, and these docstrings can be displayed on hover at the respective use sites.
- Typos in the names of constructors of polymorphic variants can be detected early.
- Since the evaluation of expressions in Duo is kind-directed, we need a place to specify how constructors and destructors of structural types are to be evaluated.

#### Constructor declarations

In order to define a structural variant of booleans, the following two declarations can be used.

```
constructor True;
constructor False;
```
In this case, no evaluation order has been specified, and in the case of constructors this defaults to `CBV`.
I.e. the above declaration is a shorthand for the more explicit declaration:

```
constructor True : CBV;
constructor False : CBV;
```

For constructors which take arguments the user has to specify the evaluation order used for the respective argument position.
Structural peano natural numbers where the argument to the successor constructor is evaluated eagerly are declared as follows:

```
constructor Z;
constructor S(CBV);
```

which, again, is shorthand for

```
constructor Z : CBV;
constructor S(CBV) : CBV;
```
These constructors then occur in [structural data types](types.md#structural-data-types) like `< True, False> ` or `< S(< Z >)>`.

#### Destructor declarations

Destructor declarations are similar to constructor declarations, with one difference.
If you don't specify a evaluation order for the destructor, it defaults to CBN evaluation.
Hence, the declaration

```
destructor Name(CBV);
```

is shorthand for the declaration

```
destructor Name(CBV) : CBN;
```

These destructors then occur in [structural codata types](types.md#structural-codata-types) like `{ Name(String) }`.

## Producer declarations

A producer is declared using the `def prd` syntax.
An optional type signature can be specified.

```
def prd two := S(S(Z));

def prd three: Nat := S(S(S(Z)));

def prd id: forall a. a -> a := \x => x;
```

By default, producers defined using the `def prd` keywords are not recursive.
For recursive definitions, the keyword `rec` has to be added.

## Consumer declarations

A consumer is declared using the `def cns` syntax.
An optional type signature can be specified.

```
def cns exitOnBool := case {
    True => #ExitSuccess,
    False => #ExitFailure
};

def cns exitOnBool : Bool := case {
    True => #ExitSuccess,
    False => #ExitFailure
};
```

By default, consumers defined using the `def cns` keywords are not recursive.
For recursive definitions, the keyword `rec` has to be added.

## Command declarations

A command is declared using the `def cmd` syntax.
Commands have no type, and it is therefore not possible to write a type signature.

```
def cmd exit := #ExitFailure;

def cmd main := #ExitSuccess;
```

The command called "main" is treated specially, since execution starts with the execution of the "main" command.

## Type Class declarations

A type class is declared using the `class` keyword.
Currently only single parameter type classes are supported, thus a single type variable together with its kind and variance have to be declared.

```
class Show(+a : CBV){
    Show(a, return String)
};
```

Inside the body of the class declaration an arbitrary number of type class methods can be defined.
The signature has to respect the variance of the type variable above.
Method and type class name live in different name spaces, so it is possible to overload them.

## Instance declarations

An instance of a type class is declared using the `instance` keyword.
Unlike in languages such as Haskell, instances have to be named.

```
instance showBool : Show Bool {
    Show(b, k) => case b of {
        True => MkString("True"),
        False => MkString("False")
    } >> k
};
```

Type class methods are implemented as commands and can be used as such, e.g.:

```
def cmd main := Show(True, mu x. #Print(x, #ExitSuccess));
```