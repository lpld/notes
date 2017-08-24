# Contents
[TOC]
# Chapter 1. Introduction
## 1.7. The origins of functional languages
 * In 1931, Godel showed that any system powerful enough to describe arithmetic was necessarily __incomplete__.
 * In 1936, three distinct formal approaches to computability were proposed:
   * Turing's __Turing machines__
   * Kleene's __recursive functions theory__
   * Church's __lambda calculus__

# Chapter 2. λ calculus
## 2.1. Abstraction
> Abstraction is based on generalization through the introduction of a name to replace a value and specialization through the replacement of a name with another value.

## 2.4. λ expressions
`<expression> ::= <name> | <function> | <application>`
 * A _name_ may be any sequence of non-blank characters.
 * A _fucntion_ is an abstraction over a λ expression and has the form:
   `<function> ::= λ<name>.<body>`,
   where
   `<body> ::= <expression>`
   The name is called function's __bound variable__.
 * A _function application_ (also called __bound pair__) has the form:
   `<application> ::= (<function expression> <argument expression>)`,
   where
   `<function expression> ::= <expression>`
   `<argument expression> ::= <expression>`

There are two approches to evaluating function application. All occurrences of the function's bound variable in the function's body expression are then replaced by:
 * _either_ the value of the argument expression (__applicative order__),
 * _or_ the unevaluated argument expression (__normal order__).

## 2.5. Simple λ functions
### 1. Identity function
  `λx.x` returns the value to which it's applied.

  Applying _identity function_ to itself:
  `(λx.x λx.x)` -> `λx.x`
### 2. Self-application function
  `λs.(s s)` applies its argument to its argument.

  Some applications:
  * Appling _identity function_ to _self-application function_:
    `(λx.x λs.(s s))` ->
    `λs.(s s)`
  * Applying _self-application function_ to _identity function_:
    `(λs.(s s) λx.x)` ->
    `(λx.x λx.x)` ->
    `λx.x`
  * Applying _self-application function_ to itself:
    `(λs.(s s) λs.(s s))` ->
    `(λs.(s s) λs.(s s))` ->
    ... (never terminates)

### 3. Function application function
  `λfunc.λarg.(func arg)`

  For example, applying the _identity function_ to _self-application function_:
  `((λfunc.λarg.(func arg) λx.x) λs.(s s))` ->
  `(λarg.(λx.x arg) λs.(s s))` ->
  `(λx.x λs.(s s))` ->
  `λs.(s s)`

## 2.7. Naming functions and β reduction
  We will use:

  `def <name> = <function>`

  to define a name/function association, for instance:
  `def identity = λx.x`
  `def self_apply = λs.(s s)`
  `def apply = λfunc.λarg.(func arg)`

  We will use the notation:

  `(<name> <argument>) == (<function> <argument>)`

  to indicate the replacement of a _<name>_ by its associated _<function>_.

  Formally, the replacement of of a _bound variable_ with an _argument_ in a function body is called __β reduction__. We will introduce the notation

  `(<function> <argument>) => <expression>`

  to mean that the _<expression>_ results from the application of the _<function>_ to the unevaluated _<argument>_. When we have seen a sequence of reductions before, we will omit the reductions and write:

  `=> ... =>`.

## 2.8. Functions from functions
### Identity
  Let's define a function with same effect as the _identity function_:

  `def identity2 = λx.((apply identity) x)`

  Let's apply this to identity function:
  `(identity2 identity)` ==
  `(λx.((apply identity) x) identity)` =>
  `((apply identity) identity)` ==
  `((λfunc.λarg.(func arg) identity) identity)` =>
  `(λarg.(identity arg) identity)` =>
  `(identity identity)` => ... =>
  `identity`

  Let us show that the _identity_ and the _identity2_ are equivalent. Suppose, `<argument>` stays for any expression. Then
  `(identity2 <argument>)` ==
  `(λx.((apply identity) x) <argument>)` =>
  `((apply identity) <argument>)` => ... =>
  `(identity <argument>)` => ... =>
  `<argument>`,
  so _identity_ and _identity2_ have the same effect.

### Application
  We can use the _function application function_ to define a function with the same effect as the _function application function_ itself. Suppose `<function>` stays for any function. Then
  `(apply <function>)` ==
  `(λfunc.λarg.(func arg) <function>)` =>
  `λarg.(<function> arg)`.

  Applying this to any `<argument>` we get:
  `(λarg.(<function> arg) <argument>)` =>
  `(<function> <argument>)`,

  which is the application of the original function to an argument. Using _apply_ adds a layer of _β reduction_ to the application.

### Self-application
  `def self_apply2 = λs.((apply s) s)`

  Let us apply this to the _identity_ function:
  `(self_apply2 identity)` ==
  `(λs.((apply s) s) identity)` =>
  `((apply identity) identity)` => ... =>
  `(identity identity)` => ... =>
  `identity`

  In general, applying _self\_apply2_ to any `<argument>`:
  `(self_apply2 <argument>)` ==
  `(λs.((apply s) s) <argument>)` =>
  `((apply <argument>) <argument>)` => ... =>
  `(<argument> <argument>)`

  So, _self\_apply_ and _self\_apply2_ have the same effect.

## 2.9. Argument selection and argument pairing functions
### Selecting first/second argument
  `def select_first = λfirst.λsecond.first`
  `def select_second = λfirst.λsecond.second`

  _select\_second_ applied to anything returns a version of _identity_:
  `(select_second <argument>)` ==
  `(λfirst.λsecond.second <argument>)` =>
  `λsecond.second`, which is equivalent to `λx.x`.

  _select\_first_ applied to _identity_ returns a version of _select\_second_:
  `(select_first identity)` ==
  `(λfirst.λsecond.first identity)` =>
  `λsecond.identity` ==
  `λsecond.λx.x`, which is equivalent to `λfirst.λsecond.second`.

### Making pairs of two arguments
  `def make_pair = λfirst.λsecond.λfunc.((func first) second)`

  Applying it to arbitrary arguments:
  `((make_pair <argument1>) <argument2>)` ==
  `((λfirst.λsecond.λfunc.((func first) second) <argument1>) <argument2>)` =>
  `(λsecond.λfunc.((func <argument1>) second) <argument2>)` =>
  `λfunc.((func <argument1>) <argument2>)`

  Applying this function to _select\_first_ returns the first argument:
  `(λfunc.((func <argument1>) <argument2>) select_first)` =>
  `((select_first <argument1>) <argument2>)` ==
  `((λfirst.λsecond.first <argument1>) <argument2>)` =>
  `(λsecond.<argument1> <argument2>)` =>
  `<argument1>`,

  and applying this function to _select\_second_ returns ths second argument:
  `(λfunc.((func <argument1>) <argument2>) select_second)` =>
  `((select_second <argument1>) <argument2>)` ==
  `((λfirst.λsecond.second <argument1>) <argument2>)` =>
  `(λsecond.second <argument2>)` =>
  `<argument2>`,

## 2.11. Name clashes and α conversion
  A name clash arises when a β reduction places an expression with a free variablle in the scope of a bound variable with the same name as the free variable. consistent renaming, which is known as __α conversion__, removes the name clash. For a function:

  `λ<name1>.<body>`

  the name `<name1>` and all free occurrences of `<name1>` in `<body>` may be replaced by a new name `<name2>` provided `<name2>` is not the name of a free variable in `λ<name1>.<body>`.

## 2.12. Simplification through η reduction

  Let's look at an expression

  `λ<name>.(<expression> <body>)`

  and apply it to an arbitrary `<argument>`:

  `(λ<name>.(<expression> <body>) <argument>)` =>
  `(<expression> <argument>)`.

  We can see that `λ<name>.(<expression> <body>)` is equivalent to `<expression>`. This simplification is called __η reduction__.

# Chapter 3. Conditions, booleans and numbers
## 3.1. Truth values and conditional expressions
  We can model a conditional expression using a version of `make_pair` function:

  `def cond = λe1.λe2.λc.((c e1) e2)`

  Applying it to arbitrary expressions `<expression1>` and `<expression2>`:
  `((cond <expression1>) <expression2>)` => ... =>
  `λc.((c <expression1>) <expression2>)`

  Now, if this function is applied to `select_first`:
  `(λc.((c <expression1>) <expression2>) select_first)` => ... =>
  `<expression1>`

  and if it is applied to `select_second`:
  `(λc.((c <expression1>) <expression2>) select_second)` => ... =>
  `<expression2>`

  Now we'll define
  `def true = select_first`
  `def false = select_second`

## 3.2. NOT

  We will use conditional expression to define `not`. If true, then false; if false, then true (TRUE ? FALSE : TRUE):

  `def not = λx.(((cond false) true) x)`

  Simplifying the inner body:
  `(((cond false) true) x)` ==
  `(((λe1.λe2.λc.((c e1) e2) false) true) x)` =>
  `((λe2.λc.((c false) e2) true) x)` =>
  `(λc.((c false) true) x)` =>
  `((x false) true)`

  So
  `def not = λx.((x false) true)`

  Let's check:
  `(not true)` ==
  `(λx.((x false) true) true)` =>
  `((true false) true)` ==
  `((λfst.λsnd.fst false) true)` =>
  `(λsnd.false true)` =>
  `false`

  `(not false)` ==
  `(λx.((x false) true) false)` =>
  `((false false) true)` ==
  `((λfst.λsnd.snd false) true)` =>
  `(λsnd.snd true)` =>
  `true`

## 3.3. AND

  Againg, we'll use conditional expression to define `and`. If first and true, then second; if first is false, then false (X ? Y : FALSE).
  `def and = λx.λy.(((cond y) false) x)`

  Simplifying the inner body:
  `(((cond y) false) x)` ==
  `(((λe1.λe2.λc.((c e1) e2) y) false) x)` =>
  `((λe2.λc.((c y) e2) false) x)` =>
  `(λc.((c y) false) x)` =>
  `((x y) false)`

  So
  `def and = λx.λy.((x y) false) `

  Let's check:
  `((and true) false)` ==
  `((λx.λy.((x y) false) true) false)` =>
  `(λy.((true y) false) false)` =>
  `((true false) false)` ==
  `((λfst.λsnd.fst false) false)` =>
  `(λsnd.false false)` =>
  `false`

## 3.4. OR

  (X ? TRUE : Y):
  `def or = λx.λy.(((cond true) y) x)`

  Simplifying the inner body:
  `(((cond true) y) x)` ==
  `(((λe1.λe2.λc.((c e1) e2) true) y) x)` =>
  `((λe2.λc.((c true) e2) y) x)` =>
  `(λc.((c true) y) x)` =>
  `((x true) y)`

  So
  `def or = λx.λy.((x true) y)`

  Let's check:
  `((or false) true)` ==
  `((λx.λy.((x true) y) false) true)` =>
  `(λy.((false true) y) true)` =>
  `((false true) true)` ==
  `((λfst.λsnd.snd true) true)` ==
  `(λsnd.snd true)` ==
  `true`

## 3.5. Natural numbers

  We will represent natural numbers as __successors of zero__.
  `def one = (succ zero)`
  `def two = (succ one)`
  `def three = (succ two)`

  We will define __zero__ and __succ__ the following way:
  `def zero = identity`
  `def succ = λn.λs.((s false) n)`

  Each time _succ_ is applied to a number n it builds a pair function with false first and the original number second:
  `one` ==
  `(succ zero)` ==
  `(λn.λs.((s false) n) zero)` =>
  `λs.((s false) zero)`

  `two` ==
  `(succ one)` ==
  `(λn.λs.((s false) n) one)` =>
  `λs.((s false) one)` =>
  `λs.((s false) λs.((s false) zero))`

  Now we can define a unary function _iszero_ which returns _true_ if its argument is zero.
  A number other than zero is a function with an argument which may be used as a selector:
  `λs.((s false) <number>)`

  If the argument is set to `select_first` then `false` will be selected:
  `(λs.((s false) <number>) select_first)` =>
  `((select_first false) <number>)` ==
  `((λfst.λsnd.fst false) <number>)` =>
  `(λsnd.false <number>)` =>
  `false`

  If _zero_, which is an `identity` function, is applied to `select_first`, then `select_first`, which is same as _true_ by definition, will be returned:
  `(zero select_first)` ==
  `(λx.x select_first)` =>
  `select_first` ==
  `true`

  This suggests using:
  `def iszero = λn.(n select_first)`

  We can now define predecessor function `pred`. For our representation, _pred_ should strip off a layer of nesting from an arbitrary number `λs.((s false) <number>)` and return the `<number>`. This suggests using `select_second`:
  `(λs.((s false) <number>) select_second)` =>
  `((select_second false) <number>)` ==
  `((λfst.λsnd.snd false) <number>)` =>
  `(λsnd.snd <number>)` =>
  `<number>`

  so we might define a first version of _pred_ as:
  `def pred1 = λn.(n select_second)`

  However, there's a problem if we apply this function to _zero_:
  `(pred1 zero)` ==
  `(λn.(n select_second) zero)` =>
  `(zero select_second)` ==
  `(λx.x select_second)` =>
  `select_second` ==
  `false`

  which is not a representation of a number. So we can do something like this:
  `<number> = zero ? zero : predecessor of <number>`

  so
  `def pred = λn.(((cond zero) (pred1 n)) (iszero n))`

  Simplifying the body:
  `(((cond zero) (pred1 n)) (iszero n))` ==
  `(((λe1.λe2.λc.((c e1) e2) zero) (pred1 n)) (iszero n))` =>
  `((λe2.λc.((c zero) e2) (pred1 n)) (iszero n))` =>
  `(λc.((c zero) (pred1 n)) (iszero n))` =>
  `(((iszero n) zero) (pred1 n))` ==
  `(((iszero n) zero) (λn.(n select_second) n))` =>
  `(((iszero n) zero) (n select_second))`

  So
  `def pred = λn.(((iszero n) zero) (n select_second))`

## 3.6. Simplified notations
  We can ommit parentheses for the function application when its clear from the context what function is applied to which parameters. Also we can rewrite all our functions in simpified form, ommitting all lambdas:

  `def identity x = x`
  `def self_apply s = s s`
  `def apply func arg = func arg`
  `def select_first first second = first`
  `def select_second first second = second`
  `def make_pair e1 e2 c = c e1 e2`
  `def cond e1 e2 c = c e1 e2`
  `def true first second = first`
  `def false first second = second`
  `def not x = x false true`
  `def and x y = x y false`
  `def or x y = x true y`

  For some functions there are standard equivalent notations:
  `cond <true_choice> <false_choice> <condition>` can be written using _if... then... else..._:
  `if <condition>`
  `then <true choice>`
  `then <false choice>`

  Now we can rewrite some of our functions:
  ```haskell
  def pred =
    if iszero n
    then zero
    else n select_second

  def not x =
    if x
    then false
    else true

  def and x y
    if x
    then y
    else false

  def or x y
    if x
    then true
    else y
  ```

# Chapter 4. Recursion and arithmetic
## 4.3. Passing function to itself
  We will define addition function the following way:

  ```haskell
  def add = add2 add2

  def add2 f x y =
    if iszero y
    then x
    else f f (succ x) (pred y)
  ```

  This definition expands and evalutes as:
  ```haskell
  add ==

  (λf.λx.λy.
    if iszero y
    then x
    else f f (succ x) (pred y)) add2 =>

  λx.λy.
    if iszero y
    then x
    else add2 add2 (succ x) (pred y)
  ```
  We do not strictly need to replace other occurrences of `add2`, as its definition contains no references to itself.

## 4.4. Applicative order reduction
  * __applicative order__: `->`
  * __normal order__: `=>`

## 4.5. Recursion function

  Multiplication function:
  ```haskell
  def mult x y =
    if iszero y
    then zero
    else add x (mult x (pred y))
  ```

  Removing self-reference:
  ```haskell
  def mult1 f x y
    if iszero y
    then zero
    else add x (f x (pred y))
  ```

  We would like to have a function `recursive`:
  `def mult = recursive mult1`

  This function must not only pass a copy of its argument to that argument, but also ensure that the self-application will continue: the copying mechanism must be passed on as well. This suggests that _recursive_ should be of the form:
  `def recursive f = f <'f' and copy>`

  If recurisve is applied to _mult1_:
  ```haskell
  recurisve mult1 ==
  λf.(f <'f' and copy>) mult1 =>
  mult1 <'mult1' and copy> ==
  (λf.λx.λy.
    if iszero y
    then zero
    else add x (f x (pred y))) <'mult1' and copy> =>

  λx.λy.
    if iszero y
    then zero
    else add x (<'mult1' and copy> x (pred y))
  ```

  In the body we have `<'mult1' and copy> x (pred y)`, but we require `mult1 <'mult1' and copy> x (pred y)`, so that `<'mult1' and copy>` is passed on again through `mult1`'s bound variable to the next level of recursion. Thus, the copy mechanism must be such that:
  `<'mult1' and copy>` => ... => `mult1 <'mult1' and copy>`.

  So the copy mechanism must be an application, and that application must be self-replicating. We know that self-application function `λs.(s s)` will self-replicate, when applied to itself, but the replication never ends. Self-replication must be delayed through abstraction with construction of a new function:
  `λf.λs.(f (s s))`

  When this function is applied to an arbitrary function, we get:
  `λf.λs.(f (s s)) <function1>` => `λs.(<function1> (s s))`

  If this function is now applied to itself:
  ```haskell
  λs.(<function1> (s s)) λs.(<function1> (s s)) =>
  <function1> (λs.(<function1> (s s)) λs.(<function1> (s s)))
  ```

  We have a copy mechanism that matches our requirements. Now we can define `recurisve`:
  `def recursive f = λs.(f (s s)) λs.(f (s s))`

  Let's check how it works with mult:
  `def mult = recurisve mult1`

  This definition evaluates to:
  ```haskell
  λf.(λs.(f (s s)) λs.(f (s s))) mult1 =>
  λs.(mult1 (s s)) λs.(mult1 (s s)) =>
  mult1 (λs.(mult1 (s s)) λs.(mult1 (s s))) ==

  (λf.λx.λy.
    if iszero y
    then zero
    else add x (f x (pred y))) (λs.(mult1 (s s)) λs.(mult1 (s s))) =>

  λx.λy.
    if iszero y
    then zero
    else add x ((λs.(mult1 (s s)) λs.(mult1 (s s))) x (pred y))
  ```

  Let's try:
  ```haskell
  mult tree two => ... =>
  (λx.λy.
    if iszero y
    then zero
    else add x ((λs.(mult1 (s s)) λs.(mult1 (s s))) x (pred y))) three two => ... =>

  if iszero two
  then zero
  else add three ((λs.(mult1 (s s)) λs.(mult1 (s s))) three (pred two)) => ... =>

  add three ((λs.(mult1 (s s)) λs.(mult1 (s s))) three (pred two)) ->

  add three (mult1 (λs.(mult1 (s s)) λs.(mult1 (s s))) three (pred two)) ==

  add three ((λx.λy.
              if iszero y
              then zero
              else add x ((λs.(mult1 (s s)) λs.(mult1 (s s)))
                          x (pred y))) three (pred two)) -> ... ->

  add three if iszero (pred two)
            then zero
            else add three ((λs.(mult1 (s s)) λs.(mult1 (s s)))
                            three (pred (pred two))) -> ... ->

  add three (add three ((λs.(mult1 (s s)) λs.(mult1 (s s)))
                        three (pred (pred two)))) ->

  add three (add three (mult1 (λs.(mult1 (s s)) λs.(mult1 (s s)))
                        three (pred (pred two)))) ==

  add three (add three ((λx.λy.
                          if iszero y
                          then zero
                          else add x ((λs.(mult1 (s s)) λs.(mult1 (s s))) x (pred y)))
                        three (pred (pred two)))) ->

  add three (add three if iszero (pred (pred (two)))
                       then zero
                       else add three ((λs.(mult1 (s s)) λs.(mult1 (s s)))
                                       three (pred (pred (pred two))))) ->

  add three (add three zero) -> ... ->
  add three three => ... =>
  six
  ```

## 4.6. Recustion notation

  The function `recursive` is known as a __paradoxical combinator__ or a __fixed point finder__, and is called __Y__ in λ calculus literature.
  `rec <name> = <expression>`

  Example:
  ```haskell
  rec add x y =
    if iszero y
    then x
    else add (succ x) (pred y)
  ```

  is equivalent to:
  ```haskell
  def add1 f x y =
    if iszero y
    then x
    else f (succ x) (pred y)

  def add = recursive add1
  ```

  And for multiplication we have:
  ```haskell
  rec mult x y =
    if iszero y
    then zero
    else add x (mult x (pred y))
  ```

## 4.7. Arithmetic operations
### 1. Raising to power
  ```haskell
  rec power x y =
    if iszero y
    then one
    else mult x (power x (pred y))
  ```

### 2. Subtraction
  ```haskell
  rec sub x y =
    if iszero y
    then x
    else sub (pred x) (pred y)
  ```
### 3. Comparison
  Because of our implementation of `pred`, we cannot rely on `sub` to get the difference of two arbitrary numbers. With our implementation, if we subtract a number from a smaller number we'll get `zero`. So we need a function for absolute difference:
  `def abs_diff x y = add (sub x y) (sub y x)`

  Now we can determine if two numbers are equal:
  `def equal x y = iszero (abs_diff x y)`

  Equivalent definition:
  ```haskell
  rec equal x y =
    if and (iszero x) (iszero y)
    then true
    else
      if or (iszero x) (iszero y)
      then false
      else equal (pred x) (pred y)
  ```

  Arithmetic inequalities:
  `def greater x y = not (iszero (sub x y))`
  `def greater_or_equal x y = iszero (sub y x)`

### 4. Division
  ```haskell
  rec div1 x y =
    if greater y x
    then zero
    else succ (div1 (sub x y) y)

  rec div x y =
    if iszero y
    then zero
    else div1 x y
  ```

# Chapter 5. Types
## 5.3. Representing type objects

  We will represent an object as a type/value pair:
  `def make_obj type value = λs.(s type value)`

​	  The type is selected with `select_first`:
  `def type obj = obj select_first`

  And the value is selected with `selec_second`:
  `def value obj = obj select_second`

  We will use numbers to represent types and numeric comparison to test type:
  `def istype t obj = equal (type obj) t`

  We are going to define typed objects and operations in terms of untyped objects and operations. In general, our approach will be:
      1. check argument types
      2. extract untyped values from typed arguments
      3. carry out untyped operations on untyped values
      4. construct typed result from untyped result

  In general we will used upper case letters for typed constructs and lower case letters for untyped constructs.

## 5.4. Errors

  Whenever we detect a type error we will return an appropriate error object. Such an object will have type `error_type` represented as `zero`:
  `def error_type = zero`

  We need a function to construct error objects:
  `def MAKE_ERROR = make_obj error_type`

  This definition expands to
  ```haskell
  make_obj error_type ==
  λtype.λvalue.λs.(type value) error_type =>
  λvalue.λs.(s error_type value)
  ```

  We will define a universal error object of type `error_type`:
  `def ERROR = MAKE_ERROR error_type`

  This definition expands to:
  ```haskell
  MAKE_ERROR error_type ==
  make_obj error_type error_type => ... =>
  λvalue.λs.(s error_type value) error_type =>
  λs.(s error_type error_type)
  ```

  We can test for an object of type error by using `istype` to look for `error_type`:
  `def iserror = istype error_type`

  It expands to:
  ```haskell
  iserror ==
  istype error_type ==
  λt.λobj.(equal (type obj) t) error_type =>
  λobj.(equal (type obj) error_type)
  ```

  For example, to test that `ERROR` is of type `error_type`:
  ```haskell
  iserror ERROR ==
  λobj.(equal (type obj) error_type) ERROR =>
  equal (type ERROR) error_type ==
  equal (λobj.(obj select_first) ERROR) error_type ->
  equal (ERROR select_first) error_type ==
  equal (λs.(s error_type error_type) select_first) error_type ->
  equal (select_first error_type error_type) error_type -> ... ->
  equal error_type error_type => ... =>
  true
  ```

## 5.5. Booleans

  We will represent the __boolean__ type as `one`:
  `def bool_type = one`
  `def MAKE_BOOL = make_obj bool_type`

  which expands to
  ```haskell
  make_obj bool_type ==
  λtype.λvalue.λs.(s type value) bool_type =>
  λvalue.λs.(s bool_type value)
  ```

  We can now construct the typed booleans:
  ```haskell
  def TRUE = MAKE_BOOL true
  def FALSE = MAKE_BOOL false
  ```

  Testing boolean:
  `def isbool = istype bool_type`

  A boolean error:
  `def BOOL_ERROR = MAKE_ERROR bool_type`

  Typed boolean functions:
  ```haskell
  def NOT X =
    if isbool X
    then MAKE_BOOL (not (value X))
    else BOOL_ERROR

  def AND X Y =
    if and (isbool X) (isbool Y)
    then MAKE_BOOL (and (value X) (value Y))
    else BOOL_ERROR
  ```

  Let's evaluate
  ```haskell
  AND TRUE FALSE => ... =>

  if and (isbool TRUE) (isbool FALSE)
  then MAKE_BOOL (and (value TRUE) (value FALSE))
  else BOOL_ERROR
  ```

  First of all:
  ```haskell
  isbool TRUE ==
  istype bool_type TRUE ==
  λt.λobj.(equal (type obj) t) bool_type TRUE => ... =>
  equal (type TRUE) bool_type ==
  equal (λobj.(obj select_first) TRUE) bool_type ->
  equal (TRUE select_first) bool_type ==
  equal (MAKE_BOOL true select_first) bool_type ==
  equal (make_obj bool_type true select_first) bool_type ==
  equal (λtype.λobj.λs.(s type obj) bool_type true select_first) bool_type -> ... ->
  equal (select_first bool_type true) bool_type -> ... ->
  equal bool_type bool_type => ... =>
  true
  ```

  Similarly:
  ```haskell
  isbool FALSE => ... =>
  true
  ```

  Now we have:
  ```haskell
  MAKE_BOOL (and (value TRUE) (value FALSE)) ==
  MAKE_BOOL (and (λobj.(obj select_second) TRUE) (λobj.(obj select_second) FALSE)) -> ... ->
  MAKE_BOOL (and (TRUE select_second) (FALSE select_second)) ==

  MAKE_BOOL (and (MAKE_BOOL true select_second)
                 (MAKE_BOOL false select_second)) -> ... ->

  MAKE_BOOL (and (make_obj bool_type true select_second)
                 (make_obj bool_type false select_second)) -> ... ->

  MAKE_BOOL (and (λtype.λobj.λs.(s type obj) bool_type true select_second)
                 (λtype.λobj.λs.(s type obj) bool_type false select_second)) ==

  MAKE_BOOL (and (select_second bool_type true)
                 (select_second bool_type false)) -> ... ->

  MAKE_BOOL (and true false) -> ... ->

  MAKE_BOOL false => ... =>
  FALSE
  ```

## 5.6. Typed conditional expressions

  ```haskell
  def COND E1 E2 C =
    if isbool C
    then
      if value C
      then E1
      else E2
    else BOOL_ERROR
  ```

  We will now write

  ```haskell
  IF <condition>
  THEN <expression1>
  ELSE <expression2>
  ```

  instead of `COND <expression1> <expression2> <condition>`.

  We also need typed versions of type testers:

  ```haskell
  def ISERROR E = MAKE_BOOL (iserror E)
  def ISBOOL E = MAKE_BOOL (isbool E)
  ```

## 5.7. Numbers and arithmetic

  ```haskell
  def numb_type = two

  def MAKE_NUMB = make_obj numb_type
               => λvalue.λs.(s numb_type value)

  def NUMB_ERROR = MAKE_ERROR numb_type
                => λs.(s error_type numb_type)

  def isnumb = istype numb_type
            => λobj.(equal (type obj) numb_type)

  def ISNUMB N = MAKE_BOOL (isnumb N)
  ```

  Now we can construct a typed zero:

  ```haskell
  def 0 = MAKE_NUMB zero
       => λs.(s numb_type zero)
  ```

  Now we need a typed successor function:
  ```haskell
  def SUCC N =
    if isnumb N
    then MAKE_NUMB succ (value N)
    else NUMB_ERROR
  ```

  to define numbers:

  ```haskell
  def 1 = SUCC 0
  def 2 = SUCC 1
  def 3 = SUCC 2
  ...
  ```

  `1` expands as:

  ```haskell
  1 ==
  SUCC 0 => ... =>

  if isnumb 0
  then MAKE_NUMB succ (value 0)
  else NUMB_ERROR
  ```

  First of all:
  ```haskell
  isnumb 0 ==
  istype numb_type 0 =>
  λt.λobj.(equal (type obj) t) numb_type 0 => ... =>
  equal (type 0) numb_type =>
  equal (λobj.(obj select_first) 0) numb_type ->
  equal (0 select_first) numb_type ==
  equal (λs.(s numb_type zero) select_first) numb_type ->
  equal (select_first numb_type zero) numb_type -> ... ->
  equal numb_type numb_type => ... =>
  true
  ```

  Then:
  ```haskell
  MAKE_NUMB (succ (value 0)) ==
  MAKE_NUMB (succ (λobj.(obj select_second) 0)) ->
  MAKE_NUMB (succ (0 select_second)) ==
  MAKE_NUMB (succ (λs.(s numb_type zero) select_second)) ->
  MAKE_NUMB (succ (select_second numb_type zero)) -> ... ->
  MAKE_NUMB (succ zero) ==
  MAKE_NUMB one ==
  λtype.λobj.λs.(s type obj) numb_type one => ... =>
  λs.(s numb_type one)
  ```

  In general, a typed number is a pair with the untyped equivalent as value.

  We can now redefine predecessor to return an error for zero:

  ```haskell
  def PRED N =
    if isnumb N
    then
      if iszero (value N)
      then NUMB_ERROR
      else MAKE_NUMB ((value N) select_second)
    else NUMB_ERROR
  ```

  Typed test for zero:
  ```haskell
  def ISZERO N =
    if isnumb (value N)
    then MAKE_BOOL (iszero (value N))
    else NUMB_ERROR
  ```

  Now we can redefine the binary arithmetic operations. For this we'll introduce an auxiliary function:
  ```haskell
  def both_nums X Y = and (isnumb X) (isnumb Y)
  ```

  Addition:
  ```haskell
  def + X Y =
    if both_nums X Y
    then MAKE_NUMB (add (value X) (value Y))
    else NUMB_ERROR
  ```

  Multiplication:
  ```haskell
  def * X Y =
    if both_nums X Y
    then MAKE_NUMB (mult (value X) (value Y))
    else NUMB_ERROR
  ```

  Division:
  ```haskell
  def / X Y =
    if both_nums X Y
    then
      if iszero (value Y)
      then NUMB_ERROR
      else MAKE_NUMB (div (value X) (value Y))
    else NUMB_ERROR
  ```

  Equality:
  ```haskell
  def EQUAL X Y =
    if both_nums X Y
    then MAKE_BOOL (equal (value X) (value Y))
    else NUMB_ERROR
  ```

## 5.8. Characters

  We will introduce a new type for characters:
  ```haskell
  def char_type = four
  def CHAR_ERROR = MAKE_ERROR char_type
  def ischar = istype char_type
  def ISCHAR C = MAKE_BOOL (ischar C)
  def MAKE_CHAR = make_obj char_type
  ```

  To provide the ordering, characters will be mapped onto the narural numbers, so the value of a character will be an untyped number. We will use the ASCII values:
  ```haskell
  def '0' = MAKE_CHAR forty_eight
  def '1' = MAKE_CHAR (succ (value '0'))
  ...
  def '9' = MAKE_CHAR (succ (value '8'))
  def 'A' = MAKE_CHAR sixty_five
  def 'B' = MAKE_CHAR (succ (value 'A'))
  ...
  def 'Z' = MAKE_CHAR (succ (value 'Y'))
  def 'a' = MAKE_CHAR ninety_seven
  def 'b' = MAKE_CHAR (succ (value 'a'))
  ...
  def 'z' = MAKE_CHAR (succ (value 'y'))
  ```

  Now we can define character ordering:
  ```haskell
  def CHAR_LESS C1 C2 =
    if and (ischar C1) (ischar C2)
    then MAKE_BOOL (less (value C1) (value C2))
    else CHAR_ERROR
  ```

  and conversion from character to number:
  ```haskell
  def ORD C =
    if ischar C
    then MAKE_NUMB (value C)
    else CHAR_ERROR
  ```

  and vice versa:
  ```haskell
  def CHAR N =
    if isnumb N
    then MAKE_CHAR (value N)
    else NUMB_ERROR
  ```

  For example, to find the numeric equivalent of 'A':
  ```haskell
  ORD 'A' => ... =>
  MAKE_NUMB (value 'A') ==
  MAKE_NUMB (value λs.(s char_type sixty_five)) ==
  MAKE_NUMB (λobj.(obj select_second) λs.(s char_type sixty_five)) -> ... ->
  MAKE_NUMB (λs.(s char_type sixty_five) select_second) ->
  MAKE_NUMB (select_second char_type sixty_five) -> ... ->
  MAKE_NUMB sixty_five ==
  λvalue.λs.(s numb_type value) sixty_five =>
  λs.(s numb_type sixty_five) ==
  65
  ```

  Equality:
  ```haskell
  def CHAR_EQUAL C1 C2 =
    if and (ischar C1) (ischar C2)
    then MAKE_BOOL (equal (value C1) (value C2))
    else CHAR_ERROR
  ```

## 5.11. Infix operators

  We will allow the names for logical and arithmetic functions to be used as __infix__ operators:
  ```haskell
  <exp1> AND <exp2> == AND <exp1> <exp2>
  <exp1> OR <exp2> == OR <exp1> <exp2>
  <exp1> + <exp2> == + <exp1> <exp2>
  <exp1> - <exp2> == - <exp1> <exp2>
  <exp1> * <exp2> == * <exp1> <exp2>
  <exp1> / <exp2> == / <exp1> <exp2>
  ```

  To simplify the presentation we will not introduce operator precedence or implicit associativity.

## 5.12. Case definitions and structure matching

  Here is a new syntax for structure matching.

  For boolean functions, we will allow the use of constants `TRUE` and `FALSE` in place of bound variables. In general, for
  ```haskell
  def <name> <bound variable>
    IF <bound variable>
    THEN <expression1>
    ELSE <expression2>
  ```

  we will now write:
  ```haskell
  def <name> TRUE  = <expression1>
   or <name> FALSE = <expression2>
  ```

  For numbers, we will allow the use of constant `0` and bound variables qualified by nested `SUCC`s in place of bound variables. In general, for:
  ```haskell
  rec <name> <bound variable>
    IF ISZERO <bound variable>
    THEN <expression1>
    ELSE <expression2 using (PRED <bound variable>)>
  ```

  we will now write:
  ```haskell
  rec <name> 0 = <expression1>
   or <name> (SUCC <bound variable>) = <expression2 using <bound variable>>
  ```

# Chapter 6. Lists and strings
## 6.2. List representation

  ```haskell
  def list_type = three
  def islist = istype list_type
  def ISLIST L = MAKE_BOOL (islist L)
  def LIST_ERROR = MAKE_ERROR list_type
  ```

  A list value will consist of a pair made from the list head and tail, so the general form of a list object with `<head>` and `<tail>` will be:
  ```haskell
  λs.(s list_type λs.(s <head> <tail>))
  ```

  Thus, we define:
  ```haskell
  def MAKE_LIST = make_obj list_type
  def CONS H T =
    if islist T
    then MAKE_LIST λs.(s H T)
    else LIST_ERROR

  def NIL = MAKE_LIST λs.(s LIST_ERROR LIST_ERROR)
  ```

  Now we can use the pair selectors to extract the head and a tail:
  ```haskell
  def HEAD L =
    if islist L
    then (value L) select_first
    else LIST_ERROR

  def TAIL L =
    if islist L
    then (value L) select_second
    else LIST_ERROR
  ```

  Tests for empty list checks:
  ```haskell
  def isnil L =
    if islist N
    then iserror (HEAD L)
    else LIST_ERROR

  def ISNIL L
    if islist N
    then MAKE_BOOL (iserror (HEAD L))
    else LIST_ERROR
  ```

## 6.3. Operations on lists
### 1. Linear length
  ```haskell
  rec LENGTH L =
    IF ISNIL L
    THEN 0
    ELSE SUCC (LENGTH (TAIL L))
  ```

### 2. Appending lists
  ```haskell
  rec APPEND L1 L2 =
    If ISNIL L1
    THEN L2
    ELSE CONS (HEAD L1) (APPEND (TAIL L1) L2)
  ```

## 6.4. List notation
  We will introduce a binary infix operator in place of `CONS`:
  ```haskell
  <expression1> :: <expression2> == CONS <expression1> <expression2>
  ```

  Also, we will represent a linear list with an implicit `NIL` at the end as a sequence of objects, separated by commas:
  ```haskell
  X::NIL == [X]
  X::[Y] == [X,Y]
  NIL == []
  ```

  Note, that constructing a list with `CONS` is not the same thing as using `[` and `]`. For list constructed with `[` and `]` there is an assumed empty list at the end. Thus:
  ```haskell
  [<first>, <second>] == CONS <first> (CONS <second> NIL)
  ```

  We may simplify long lists:
  ```haskell
  <exp1>::(<exp2>::<exp3>) == <exp1>::<exp2>::<exp3>
  ```
## 6.6. Deletion from a list
  ```haskell
  rec DELETE V L =
    IF ISNIL L
    THEN NIL
    ELSE
      IF EQUAL V (HEAD L)
      THEN TAIL L
      ELSE (HEAD H)::(DELETE V (TAIL L))
  ```

## 6.7. List comparison
  ```haskell
  rec LIST_EQUAL L1 L2 =
   IF AND (ISNIL L1) (ISNIL L2)
   THEN TRUE
   ELSE
     IF OR (ISNIL L1) (ISNIL L2)
     THEN FALSE
     ELSE AND (EQUAL (HEAD L1) (HEAD L2)) (LIST_EQUAL (TAIL L1) (TAIL L2))
  ```

## 6.8. Strings
  We will introduce __strings__ as linear lists of characters.
  ```haskell
  def ISSTRING S =
    IF ISNIL S
    THEN TRUE
    ELSE AND (ISCHAR (HEAD S)) (ISSTRING (TAIL S))
  ```

  We will represent a string as a sequence of characters within quotes. In general, the string
  ```haskell
  "<character><characters>"
  ```
  is equivalent to the list
  ```haskell
  '<character>'::"<characters>"
  ```

## 6.9. String comparison
  ```haskell
  rec STRING_EQUAL S1 S2 =
    IF AND (ISNIL S1) (ISNIL S2)
    THEN TRUE
    ELSE
      IF OR (ISNIL S1) (ISNIL S2)
      THEN FALSE
      ELSE AND (CHAR_EQUAL (HEAD S1) (HEAD S2))
               (STRING_EQUAL (TAIL S1) (TAIL S2))

  rec STRING_LESS S1 S2 =
    IF ISNIL S1
    THEN NOT (ISNIL S2)
    ELSE
      IF ISNIL S2
      THEN FALSE
      ELSE OR (CHAR_LESS (HEAD S1) (HEAD S2))
              (AND (CHAR_EQUAL (HEAD S1) (HEAD S2)) (STRING_LESS (TAIL S1) (TAIL S2)))
  ```

## 6.10. Numeric string to number conversion
  To get the numeric value of a character we can take its ASCII code and subtract zero's ASCII code from it:
  ```haskell
  def digit_value d = sub (value d) (value '0')
  def DIGIT_VALUE d = MAKE_NUMB (digit_value d)
  ```

  To get the numeric value of a string:
  ```haskell
  rec string_val v S =
    IF ISNIL S
    THEN v
    ELSE string_val (add (mult ten v) (digit_value (HEAD S)))
                    (TAIL S)

  def STRING_VAL S = MAKE_NUMB (string_val zero S)
  ```

## 6.11. Structure matching with lists
  We will extend or structure matching notation to. In general, for:
  ```haskell
  rec <name> <bound variable> =
    IF ISNIL <bound variable>
    THEN <expression1>
    ELSE <expression2 using (HEAD <bound variable>) and (TAIL <bound variable>)
  ```

  we will write:
  ```haskell
  rec <name> []               = <expression1>
   or <name> (<head>::<tail>) = <expression2 using <head> and <tail>>
  ```

  For example, to flatten the list:
  ```haskell
  rec FLAT []     = []
   or FLAT (H::T) =
     IF (ISLIST H) APPEND (FLAT H) (FLAT T)
     ELSE H::(FLAT T)
  ```

## 6.12. Ordered linear lists, insertion and sorting
  ```haskell
  rec INSERT S []     = [S]
   or INSERT S (H::T) =
    IF STRING_LESS S H
    THEN (S::H::T)
    ELSE H::(INSERT S T)
  ```

  ```haskell
  rec SORT []     = []
   or SORT (H::T) = INSERT H (SORT T)
  ```

## 6.13. Indexed linear list access

  Get an element by index:
  ```haskell
  rec IFIND N []            = []
   or IFIND 0 (H::T)        = H
   or IFIND (SUCC N) (H::T) = IFIND N T
  ```
  Comment: how will we distinguish [] denoting then end of the list (when LENGTH L < N) from [] denoting a real element whose value is []? Wouldn't it be better to return an error in case of `IFIND N []`?

  ```haskell
  rec IDELETE N []            = []
   or IDELETE 0 (H::T)        = T
   or IDELETE (SUCC N) (H::T) = H::(IDELETE N T)
  ```

  Insert element before N-th index:
  ```haskell
  rec IBEFORE 0 E L             = E::L
   or IBEFORE N E []            = []
   or IBEFORE (SUCC N) E (H::T) = H::(IBEFORE N E T)
  ```

  Replace an element:
  ```haskell
  rec IREPLACE N E []            = []
   or IREPLACE 0 E (H::T)        = E::T
   or IREPLACE (SUCC N) E (H::T) = H::(IREPLACE N E T)
  ```

## 6.14. Mapping functions
  ```haskell
  rec MAPCAR FUNC []     = []
   or MAPCAR FUNC (H::T) = (FUNC H)::(MAPCAR FUNC T)
  ```

  For example
  ```haskell
  def DOUBLE = MAPCAR λX.(X*2)
  def PLUTAL = MAPCAR λW.(APPEND W "s")
  ```

  Mapping two lists:
  ```haskell
  rec MAPCARS FUNC [] []             = []
   or MAPCARS FUNC (H1::T1) (H2::T2) = (FUNC H1 H2)::(MAPCARS FUNC T1 T2)
  ```
  
  For example
  ```haskell
  def SUM2 = MAPCARS λX.λY.(X + Y)
  ```
  
