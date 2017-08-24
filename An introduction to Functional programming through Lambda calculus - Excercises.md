# Chapter 2
## 2.2 Evaluate the following λ expressions:
 * (a) `((λx.λy.(y x) λp.λq.p) λi.i)` =>
       `(λy.(y λp.λq.p) λi.i)` =>
       `(λi.i λp.λq.p)` =>
       `λp.λq.p`

 * (b) `(((λx.λy.λz.((x y) z) λf.λa.(f a)) λi.i) λj.j)` =>
       `((λy.λz.((λf.λa.(f a) y) z) λi.i) λj.j)` =>
       `((λy.λz.(λa.(y a) z) λi.i) λj.j)` =>
       `((λy.λz.(y z) λi.i) λj.j)` =>
       `(λz.(λi.i z) λj.j)` =>
       `(λz.z λj.j)` =>
       `λj.j`

 * (c) `(λh.((λa.λf.(f a) h) h) λf.(f f))` =>
       `(λh.(λf.(f h) h) λf.(f f))` =>
       `(λh.(h h) λf.(f f))` => ... => never terminates

 * (d) `((λp.λq.(p q) (λx.x λa.λb.a)) λk.k)` =>
       `(λq.(λa.λb.a q) λk.k)` =>
       `(λa.λb.a λk.k)` =>
       `λb.λk.k`

 * (e) `(((λf.λg.λx.(f (g x)) λs.(s s)) λa.λb.b) λx.λy.λx)` =>
       `((λg.λx.(λs.(s s) (g x)) λa.λb.b) λx.λy.λx)` =>
       `(λx.(λs.(s s) (λa.λb.b x)) λx.λy.λx)` =>
       `(λx.(λs.(s s) λb.b) λx.λy.λx)` =>
       `(λs.(s s) λb.b)` =>
       `(λb.b λb.b)` =>
       `λb.b`

## 2.3 For each of the following pairs, show that function (i) is equivalent to the function resulting from expression (ii) by applying both to arbitrary arguments:
 * (a): (i) `identity`, (ii) `(apply (apply identity))`

    `((apply (apply identity)) <argument1>)` ==
    `((apply (λf.λa.(f a) identity)) <argument1>)` =>
    `((apply λa.(identity a)) <argument1>)` =>
    `((apply λa.a) <argument1>)` ==
    `((λf.λa.(f a) λa.a) <argument1>)` == | α coversion
    `((λf.λa.(f a) λb.b) <argument1>)` =>
    `(λa.(λb.b a) <argument1>)` =>
    `(λa.a <argument1>)` ==
    `(identity <argument1>)`

 * (b): (i) `apply`, (ii) `λx.λy.(((make_pair x) y) identity)`

    `((λx.λy.(((make_pair x) y) identity) <function>) <argument>)` ==
    `((λx.λy.(((λfst.λsnd.λfunc.((func fst) snd) x) y) identity) <function>) <argument>)` =>
    `((λx.λy.((λsnd.λfunc.((func x) snd) y) identity) <function>) <argument>)` =>
    `((λx.λy.(λfunc.((func x) y) identity) <function>) <argument>)` =>
    `((λx.λy.((identity x) y) <function>) <argument>)` ==
    `((λx.λy.(x y) <function>) <argument>)` ==
    `((apply <function>) <argument>)`

 * (c): (i) `identity`, (ii) `(self_apply (self_apply select_second))`

    `((self_apply (self_apply select_second)) <argument>)` ==
    `((self_apply (λs.(s s) λfst.λsnd.snd)) <argument>)` =>
    `((self_apply (λfst.λsnd.snd λfst.λsnd.snd)) <argument>)` =>
    `((self_apply λsnd.snd) <argument>)` ==
    `((λs.(s s) λsnd.snd) <argument>)` =>
    `((λsnd.snd λsnd.snd) <argument>)` =>
    `(λsnd.snd <argument>)` ==
    `(identity <argument>)`

## 2.4 Define a function `def make_triplet = ...` which is like `make_pair` but constructs a triplet from a sequence of three arguments so that any one of the arguments may be selected by the subsequent application of a triplet to a selector function.
Define selection functions:
`def triplet_first = ...`
`def triplet_first = ...`
`def triplet_third = ...`
which will select the first, second and third item from a triplet respectively.

`def make_triplet = λfst.λsnd.λthd.λfunc.(((func fst) snd) thd)`
`def triplet_first = λfst.λsnd.λthd.fst`
`def triplet_second = λfst.λsnd.λthd.snd`
`def triplet_third = λfst.λsnd.λthd.thd`

Applying `make_triplet` to arbitrary arguments:
`(((make_triplet <arg1>) <arg2>) <arg3>)` ==
`(((λfst.λsnd.λthd.λfunc.(((func fst) snd) thd) <arg1>) <arg2>) <arg3>)` =>
`((λsnd.λthd.λfunc.(((func <arg1>) snd) thd) <arg2>) <arg3>)` =>
`(λthd.λfunc.(((func <arg1>) <arg2>) thd) <arg3>)` =>
`λfunc.(((func <arg1>) <arg2>) <arg3>)`

Applying this function to `triplet_first`:
`(λfunc.(((func <arg1>) <arg2>) <arg3>) triplet_first)` =>
`(((triplet_first <arg1>) <arg2>) <arg3>) `==
`(((λfst.λsnd.λthd.fst <arg1>) <arg2>) <arg3>) `=>
`((λsnd.λthd.<arg1> <arg2>) <arg3>) `=>
`(λthd.<arg1> <arg3>) `=>
`<arg1>`

Applying this function to `triplet_second`:
`(λfunc.(((func <arg1>) <arg2>) <arg3>) triplet_second)` =>
`(((triplet_second <arg1>) <arg2>) <arg3>) `==
`(((λfst.λsnd.λthd.snd <arg1>) <arg2>) <arg3>) `=>
`((λsnd.λthd.snd <arg2>) <arg3>) `=>
`(λthd.<arg2> <arg3>) `=>
`<arg2>`

Applying this function to `triplet_third`:
`(λfunc.(((func <arg1>) <arg2>) <arg3>) triplet_third)` =>
`(((triplet_third <arg1>) <arg2>) <arg3>) `==
`(((λfst.λsnd.λthd.thd <arg1>) <arg2>) <arg3>) `=>
`((λsnd.λthd.thd <arg2>) <arg3>) `=>
`(λthd.thd <arg3>) `=>
`<arg3>`

## 2.6 User α conversion to ensure unique names in the expressions
 * (a) `λx.λy.(λx.y λy.x)` == `λx.λy.(λa.y λb.x)`
 * (b) `λx.(x (λy.(λx.x y) x))` == `λx.(x (λy.(λa.a y) x))`
 * (c) `λa.(λb.a λb.(λa.a b))` == `λa.(λb.a λc.(λd.d c))`
 * (d) `(λfree.bound λbound.(λfree.free bound)) `== `(λa.bound λb.(λc.c b))`
 * (e) `λp.λq.(λr.(p (λq.(λp.(r p)))) (q p))` == `λp.λq.(λr.(p (λs.(λt.(r t)))) (q p))`

# Chapter 3
## 3.1. Define a lambda calculus representation for implication.
 `X ? Y : TRUE`
 `def impl λx.λy.(((cond y) true) x)`

 Simplifying the inner body:
 ```haskell
 (((cond y) true) x) ==
 (((λe1.λe2.λc.((c e1) e2) y) true) x) =>
 ((λe2.λc.((c y) e2) true) x) =>
 (λc.((c y) true) x) =>
 ((x y) true)
 ```

 So
 `def impl = λx.λy.((x y) true)`

 Checking `impl false true`
 ```haskell
 ((impl false) true) ==
 ((λx.λy.((x y) true) false) true) =>
 (λy.((false y) true) true) =>
 ((false true) true) =>
 ((λfst.λsnd.snd true) true) =>
 (λsnd.snd true) =>
 true
 ```

 Checking `impl false false`
 ```haskell
 ((impl false) false) ==
 ((λx.λy.((x y) true) false) false) =>
 (λy.((false y) true) false) =>
 ((false false) true) =>
 ((λfst.λsnd.snd false) true) =>
 (λsnd.snd true) =>
 true
 ```

 Checking `impl true false`
 ```haskell
 ((impl true) false) ==
 ((λx.λy.((x y) true) true) false) =>
 (λy.((true y) true) false) =>
 ((true false) true) =>
 ((λfst.λsnd.fst false) true) =>
 (λsnd.false true) =>
 false
 ```

 Checking `impl true true`
 ```haskell
 impl true true ==
 λx.λy.(x y true) true true =>
 λy.(true y true) true =>
 true true true ==
 λfst.λsnd.fst true true =>
 λsnd.true true =>
 true
 ```

## 3.2. Define λ calculus representation for equivalence
 `X ? Y : !Y`

 `def equiv x y = cond y (not y) x`

 Simplifying the body:
 ```haskell
 cond y (not y) x ==
 λe1.λe2.λc.(c e1 e2) y (not y) x =>
 λe2.λc.(c y e2) (not y) x =>
 λc.(c y (not y)) x =>
 x y (not y)
 ```

 So
 `def equiv x y = x y (not y)`

 Checking `equiv false false`
 ```haskell
 equiv false false ==
 λx.λy.(x y (not y)) false false =>
 λy.(false y (not y)) false =>
 false false (not false) ==
 λfst.λsnd.snd false (not false) =>
 λsnd.snd (not false) =>
 not false ==
 λx.(x false true) false =>
 false false true ==
 λfst.snd.snd false true =>
 λsnd.snd true =>
 true
 ```

 Checking `equiv true false`
 ```haskell
 equiv true false
 λx.λy.(x y (not y)) true false =>
 λy.(true y (not y)) false =>
 true false (not false) ==
 λfst.λsnd.fst false (not false) =>
 λsnd.false (not false) =>
 false
 ```

## 3.3. For each of the following pairs show that the functions are equivalent.
### a.
 * `def f1 = λx.λy.(and (not x) (not y))`
 * `def f2 = λx.λy.(not (or x y))`

 Applying `f1` and `f2` to arbitrary arguments
 ```haskell
 f1 x y ==
 λx.λy.(and (not x) (not y)) x y =>
 λy.(and (not x) (not y)) y =>
 and (not x) (not y)
 ```

 ```haskell
 f2 x y ==
 λx.λy.(not (or x y)) x y =>
 λy.(not (or x y)) y =>
 not (or x y)
 ```

 Let's show that this two applications are equivalent:
 ```haskell
 equiv (and (not x) (not y)) (not (or x y)) ==
 λa.λb.(a b (not b)) (and (not x) (not y)) (not (or x y)) =>
 λb.((and (not x) (not y)) b (not b)) (not (or x y)) =>
 (and (not x) (not y)) (not (or x y)) (not (not (or x y)))  =>
 ```

 Let's try to simplify _not (not x)_
 ```haskell
 not (not x) ==
 λa.(a false true) (not x) =>
 (not x) false true  =>
 (λa.(a false true) x) false true  =>
 (x false true) false true

 If x is true,
 (true false true) false true => ... =>
 false false true => ... =>
 true

 If x is false,
 (false false true) false true => ... =>
 true false true =>
 false

 So not (not x) == x
 ```

 Going back to our expression
 ```haskell
 (and (not x) (not y)) (not (or x y)) (or x y) ==
 (λa.λb.(a b false) (not x) (not y)) (not (or x y)) (or x y) => ... =>
 ((not x) (not y) false) (not (or x y)) (or x y) ==
 ((λa.(a false true) x) (λa.(a false true) y) false) (not (or x y)) (or x y) =>
 ((x false true) (y false true) false) (not (or x y)) (or x y) =>
 ... (to be continued)
 ```

## 3.4. Show that `λx.(succ (pred x))` and `λx.(pred (succ x))` are equivalent for non-zero integer argument.

 Let `n` be an arbitrary number. Let `m` be its successor: `m = λs.(s false n)`, so `m` is non-zero integer.

 Applying first function to m:
 ```haskell
 λx.(succ (pred x)) m =>
 succ (pred m)
 ```

 Let's evaluate `pred m`
 ```haskell
 pred m ==
 λn.(((iszero n) zero) (n select_second)) λs.(s false n) =>
 ((iszero λs.(s false n)) zero) (λs.(s false n) select_second) =>
 ((λx.(x select_first) λs.(s false n)) zero) (λs.(s false n) select_second) =>
 (((λs.(s false n) select_first)) zero) (λs.(s false n) select_second) =>
 ((select_first false n) zero) (λs.(s false n) select_second) =>
 ((λfst.λsnd.fst false n) zero) (λs.(s false n) select_second) =>
 ((λsnd.false n) zero) (λs.(s false n) select_second) =>
 (false zero) (λs.(s false n) select_second) =>
 (λfst.λsnd.snd λx.x) (λs.(s false n) select_second) =>
 λx.x (λs.(s false n) select_second) =>
 λs.(s false n) select_second =>
 select_second false n => ... =>
 n
 ```

 And we already know that `succ n` == `m`.
 So
 `succ (pred m) == m`

 Now apply second function to m:
 ```haskell
 λx.(pred (succ x)) m =>
 pred (succ m)
 ```

 Let's evaluate `succ m`:
 ```haskell
 succ m ==
 λn.λs.(s false n) m =>
 λs.(s false m)
 ```
 Then `pred (succ m) => ... => m`. The evaluation is exatly the same as for `pred m`.

 Both expressions give us m, so they are equivalent for non-zero integers.

# Chapter 4
## 4.1. The following function finds the sum of numbers between `n` and zero.
 ```haskell
 def sum1 f n =
   if iszero n
   then zero
   else add n (f (pred n))

 def sum = recursive sum1
 ```

 Evaluate `sum three`.

 Let's evaluate `sum`:
 ```haskell
 sum ==
 recursive sum1 ==
 λf.(λs.(f (s s)) λs.(f (s s))) sum1 =>
 λs.(sum1 (s s)) λs.(sum1 (s s)) =>
 sum1 (λs.(sum1 (s s)) λs.(sum1 (s s))) ==

 (λf.λn.
   if iszero n
   then zero
   else add n (f (pred n))) (λs.(sum1 (s s)) λs.(sum1 (s s))) =>

 λn.
   if iszero n
   then zero
   else add n ((λs.(sum1 (s s)) λs.(sum1 (s s))) (pred n))
 ```

 So:
 ```haskell
 sum three ==
 (λn.
   if iszero n
   then zero
   else add n ((λs.(sum1 (s s)) λs.(sum1 (s s))) (pred n))) three =>

 if iszero three
 then zero
 else add three ((λs.(sum1 (s s)) λs.(sum1 (s s))) (pred three)) => ... =>

 add three ((λs.(sum1 (s s)) λs.(sum1 (s s))) (pred three)) ->

 add three ((sum1 (λs.(sum1 (s s)) λs.(sum1 (s s)))) (pred three)) ==

 add three (((λf.λn.
             if iszero n
             then zero
             else add n (f (pred n))
           ) (λs.(sum1 (s s)) λs.(sum1 (s s)))) (pred three)) ->

 add three ((λn.
             if iszero n
             then zero
             else add n ((λs.(sum1 (s s)) λs.(sum1 (s s))) (pred n))
           ) (pred three)) ->

 add three (if iszero (pred three)
            then zero
            else add (pred three) ((λs.(sum1 (s s)) λs.(sum1 (s s))) (pred (pred three)))) -> ... ->

 add three (add (pred three) ((λs.(sum1 (s s)) λs.(sum1 (s s))) (pred (pred three)))) ->

 add three (add (pred three) (((λf.λn.
                                 if iszero n
                                 then zero
                                 else add n (f (pred n))
                               ) (λs.(sum1 (s s)) λs.(sum1 (s s)))) (pred (pred three)))) ->

 add three (add (pred three) ((λn.
                                 if iszero n
                                 then zero
                                 else add n ((λs.(sum1 (s s)) λs.(sum1 (s s))) (pred n))
                              ) (pred (pred three)))) ->

 add three (add (pred three) (if iszero (pred (pred three))
                              then zero
                              else add (pred (pred three))
                                       ((λs.(sum1 (s s)) λs.(sum1 (s s))) (pred (pred (pred three)))))) -> ... ->

 add three (add (pred three) (add (pred (pred three))
                                        ((λs.(sum1 (s s)) λs.(sum1 (s s))) (pred (pred (pred three)))))) ->


 add three (add (pred three) (add (pred (pred three))
                                        ((sum1 (λs.(sum1 (s s)) λs.(sum1 (s s)))) (pred (pred (pred three)))))) ==

 add three (add (pred three) (add (pred (pred three))
                                        (((λf.λn.
                                           if iszero n
                                           then zero
                                           else add n (f (pred n)))
                                        (λs.(sum1 (s s)) λs.(sum1 (s s)))) (pred (pred (pred three)))))) ->

 add three (add (pred three) (add (pred (pred three))
                                        ((λn.
                                           if iszero n
                                           then zero
                                           else add n ((λs.(sum1 (s s)) λs.(sum1 (s s))) (pred n))
                                        ) (pred (pred (pred three)))))) ->

 add three (add (pred three) (add (pred (pred three))
                                        (
                                           if iszero (pred (pred (pred three)))
                                           then zero
                                           else add (pred (pred (pred three))) ((λs.(sum1 (s s)) λs.(sum1 (s s))) (pred (pred (pred (pred three)))))
                                        ))) -> ... ->

 add three (add (pred three) (add (pred (pred three)) zero)) -> ... ->
 add three (add (pred three) (add (pred two) zero)) -> ... ->
 add three (add (pred three) (add one zero)) -> ... ->
 add three (add (pred three) one) -> ... ->
 add three (add two one) -> ... ->
 add three three => ... =>
 six
 ```

## 4.2.Write a function that finds the product of the numbers between `n` and `one`. Evaluate `prod three`
 ```haskell
 def prod1 f n =
   if iszero (pred n)
   then one
   else mult n (f (pred n))

 def prod = recursive prod1
 ```

 Evaluating if for `three`:
 ```haskell
 prod three ==
 recursive prod1 three ==
 λf.(λs.(f (s s)) λs.(f (s s))) prod1 three =>
 λs.(prod1 (s s)) λs.(prod1 (s s)) three =>
 prod1 (λs.(prod1 (s s)) λs.(prod1 (s s))) three =>

 (λf.λn.
   if iszero (pred n)
   then one
   else mult n (f (pred n))
 ) (λs.(prod1 (s s)) λs.(prod1 (s s))) three =>

 (λn.
   if iszero (pred n)
   then one
   else mult n ((λs.(prod1 (s s)) λs.(prod1 (s s))) (pred n))) three =>

   if iszero (pred three)
   then one
   else mult three ((λs.(prod1 (s s)) λs.(prod1 (s s))) (pred three)) => ... =>

   mult three ((λs.(prod1 (s s)) λs.(prod1 (s s))) (pred three)) ==

   mult three ((prod1 (λs.(prod1 (s s)) λs.(prod1 (s s)))) (pred three)) ==

   mult three (((λf.λn.
                  if iszero (pred n)
                  then one
                  else mult n (f (pred n))
   ) (λs.(prod1 (s s)) λs.(prod1 (s s)))) (pred three)) ->

   mult three ((λn.
                  if iszero (pred n)
                  then one
                  else mult n ((λs.(prod1 (s s)) λs.(prod1 (s s))) (pred n))
               ) (pred three)) ->

   mult three (if iszero (pred (pred three))
               then one
               else mult (pred three)
                         ((λs.(prod1 (s s)) λs.(prod1 (s s))) (pred (pred three)))) -> ... ->

   mult three (mult (pred three)
                    ((λs.(prod1 (s s)) λs.(prod1 (s s))) (pred (pred three)))) ->

   mult three (mult (pred three)
                    ((prod1 (λs.(prod1 (s s)) λs.(prod1 (s s)))) (pred (pred three)))) ==

   mult three (mult (pred three)
                    (((λf.λn.
                        if iszero (pred n)
                        then one
                        else mult n (f (pred n))
                    ) (λs.(prod1 (s s)) λs.(prod1 (s s)))) (pred (pred three)))) ->

   mult three (mult (pred three)
                    ((λn.
                        if iszero (pred n)
                        then one
                        else mult n ((λs.(prod1 (s s)) λs.(prod1 (s s))) (pred n))
                    ) (pred (pred three)))) ->

   mult three (mult (pred three)
                    (
                        if iszero (pred (pred (pred three)))
                        then one
                        else mult (pred (pred three)) ((λs.(prod1 (s s)) λs.(prod1 (s s))) (pred (pred (pred three))))
                    )) -> ... ->

   mult three (mult (pred three) one) -> ... ->
   mult three (mult two one) -> ... ->
   mult three two => ... =>
   six
 ```

## 4.3. Write a function that finds the sum of applying a function `fun` to the numbers between `n` and `zero`.

 ```haskell
 def fun_sum1 f fun n =
   if iszero n
   then fun n
   else add (fun n) (f fun (pred n))

 def fun_sum = recursive fun_sum1
 ```

## 4.4. Difine a function to fine the sum of applying a function `fun` to the numbers between `n` and `zero` in steps of `s`.
 ```haskell
 rec fun_sum_step fun n s =
   if not (greater n zero)
   then zero
   else add (fun n) (fun_sum_step fun (sub n s) s)
 ```

 or
 ```haskell
 def fun_sum_step = recursive fun_sum_step1
 def fun_sum_step1 f fun n s =
   if not (greater n zero)
   then zero
   else add (fun n) (f fun (sub n s) s)
 ```

 Evaluate:
 ```haskell
 def double x = add x x

 fun_sum_step double five two ==

 recursive fun_sum_step1 double five two ==
 λf.(λs.(f (s s)) λs.(f (s s))) fun_sum_step1 doube five two =>
 λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s)) doube five two =>
 fun_sum_step1 (λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s))) doube five two ==

 (λf.λfun.λn.λs.
   if not (greater n zero)
   then zero
   else add (fun n) (f fun (sub n s) s)
 ) (λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s))) doube five two =>

 (λfun.λn.λs.
   if not (greater n zero)
   then zero
   else add (fun n) ((λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s))) fun (sub n s) s)
 ) doube five two =>

 (λn.λs.
   if not (greater n zero)
   then zero
   else add (double n) ((λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s))) double (sub n s) s)
 ) five two => ... =>

 if not (greater five zero)
   then zero
   else add (double five) ((λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s))) double (sub five two) two) => ... =>

 add (double five) ((λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s))) double (sub five two) two) ->

 add (double five) ((fun_sum_step1 (λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s)))) double (sub five two) two) -> ... ->

 add ten ((fun_sum_step1 (λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s)))) double (sub five two) two) -> ... ->
 add ten ((fun_sum_step1 (λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s)))) double three two) -> ... ->

 add ten (((λf.λfun.λn.λs.
   if not (greater n zero)
   then zero
   else add (fun n) (f fun (sub n s) s)
 ) (λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s)))) double three two) ->

 add ten ((λfun.λn.λs.
   if not (greater n zero)
   then zero
   else add (fun n) ((λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s))) fun (sub n s) s)
 ) double three two) -> ... ->

 add ten (
   if not (greater three zero)
   then zero
   else add (double three) ((λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s))) double (sub three two) two)) -> ... ->

 add ten (add (double three)
              ((λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s))) double (sub three two) two)) -> ... ->

 add ten (add six ((λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s))) double one two)) ->

 add ten (add six ((fun_sum_step1 (λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s)))) double one two)) ==

 add ten (add six (((λf.λfun.λn.λs.
   if not (greater n zero)
    then zero
    else add (fun n) (f fun (sub n s) s)
 ) (λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s)))) double one two)) -> ... ->

 add ten (add six (
    if not (greater one zero)
    then zero
    else add (double one) ((λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s))) double (sub one two) two)
 )) -> ... ->

 add ten (add six (
    add (double one)
        ((λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s))) double (sub one two) two)
 )) -> ... ->

 add ten (add six ( add one
        ((λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s))) double (sub one two) two)
 )) ->

 add ten (add six ( add one
        ((fun_sum_step1 (λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s)))) double (sub one two) two)
 )) -> ... ->

 add ten (add six ( add one
        ((fun_sum_step1 (λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s)))) double zero two)
 )) ==

 add ten (add six ( add one (((λf.λfun.λn.λf
   if not (greater n zero)
   then zero
   else add (fun n) (f fun (sub n s) s)
 ) (λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s)))) double zero two))) -> ... ->

 add ten (add six ( add one (
   if not (greater zero zero)
   then zero
   else add (double zero) ((λs.(fun_sum_step1 (s s)) λs.(fun_sum_step1 (s s))) double (sub zero two) two)
 ))) -> ... ->

 add ten (add six (add one zero)) -> ... ->
 add ten (add six one) -> ... ->
 add ten seven =>
 seventeen
 ```

## 4.5. Define functions `less` and `less_or_equal`.

  `def less = not greater_or_equal`
  `def less_or_equal = not greater`

 Or independently from `greater`:
  `def less x y = not (iszero (sub y x))`
  `def less_or_equal x y = iszero (sub x y)`

## 4.6. Define a function to find the reminder on dividing one number by another.
 `def mod x y = sub (x (div x y))`

# Chapter 5
## 5.1. Evaluate fully the following expressions:
  1. ISBOOL 3
  ```haskell
  ISBOOL 3 ==
  MAKE_BOOL (isbool 3) ==
  MAKE_BOOL (istype bool_type 3) ==
  MAKE_BOOL (λt.λobj.(equal (type obj) t) bool_type 3) -> ... ->
  MAKE_BOOL (equal (type 3) bool_type) -> ... ->
  MAKE_BOOL (equal (λobj.(obj select_first) 3) bool_type) ->
  MAKE_BOOL (equal (3 select_first) bool_type) ==
  MAKE_BOOL (equal (MAKE_NUMB three select_first) bool_type) ==
  MAKE_BOOL (equal (make_obj numb_type three select_first) bool_type) ==
  MAKE_BOOL (equal (λt.λobj.λs.(s t obj) numb_type three select_first) bool_type) -> ... ->
  MAKE_BOOL (equal (λs.(s numb_type three) select_first) bool_type) ->
  MAKE_BOOL (equal (select_first numb_type three) bool_type) -> ... ->
  MAKE_BOOL (equal numb_type bool_type) -> ... ->
  MAKE_BOOL false ==
  FALSE
  ```
  3. NOT 1
  ```haskell
  NOT 1 ==

  if isbool 1
  then MAKE_BOOL (not (value 1))
  else BOOL_ERROR
  ```

  ```haskell
  isbool 1 ==
  istype bool_type 1
  λt.λobj.(equal (type obj) t) bool_type 1 => ... =>
  equal (type 1) bool_type -> ... ->
  equal numb_type bool_type => ... =>
  false
  ```

  ```haskell
  NOT 1 => ... =>
  BOOL_ERROR
  ```

  5. 2 + TRUE
  ```haskell
  2 + TRUE ==
  + 2 TRUE ==

  if both_nums 2 TRUE
  then MAKE_NUMB (add (value 2) (value TRUE))
  else NUMB_ERROR
  ```

  ```haskell
  both_nums 2 TRUE ==
  and (isnumb 2) (isnumb TRUE) -> ... ->
  and true false => ... =>
  false
  ```

  ```haskell
  2 + TRUE => ... =>
  NUMB_ERROR
  ```

## 5.2. Signed numbers might be introduced as a new type with an extra layer of ‘pairing’ so that the value of a number is preceded by a boolean to indicate whether or not the number is positive or negative:

  ```haskell
  def signed_type = ...
  def SIGNED_ERROR = MAKE_ERROR signed_type
  def POS = TRUE
  def NEG = FALSE
  def MAKE_SIGNED N SIGN = make_obj signed_type (make_obj SIGN N)
  ```

  So:
  ```haskell
  +<number> == MAKE_SIGNED <number> POS
  -<number> == MAKE_SIGNED <number> NEG
  ```

  An arbitrary number:
  ```haskell
  MAKE_SIGNED <n> <s> => ... =>
  make_obj signed_type (make_obj <s> <n>) ==
  λt.λo.λs.(s t o) signed_type (make_obj <s> <n>) => ... =>
  λs.(s signed_type (make_obj <s> <n>)) ==
  λs.(s signed_type (λt.λo.λs.(s t o) <s> <n>)) -> ... ->
  λs.(s signed_type λs.(s <s> <n>))
  ```

  1. Defined tester and selector functions for signed numbers:
  * issigned
  ```haskell
  def issigned N = istype signed_type
  ```

  * ISSIGNED
  ```haskell
  def ISSIGNED N = MAKE_BOOL (issigned N)
  ```

  * sign
  ```haskell
  def sign N = value ((value N) select_first)
  ```
  Let's check how `sign` works:
  ```haskell
  sign λs.(s signed_type λs.(s <s> <n>)) ==
  value ((value λs.(s signed_type λs.(s <s> <n>))) select_first) -> ... ->
  value ((select_second signed_type λs.(s <s> <n>)) select_first) -> ... ->
  value (λs.(s <s> <n>) select_first) -> ... ->
  value (select_first <s> <n>) -> ... ->
  value <s>
  ```

  * SIGN
  ```haskell
  def SIGN N =
    if issigned N
    then ((value N) select_first)
    else SIGNED_ERROR
  ```

  * sign_value
  ```haskell
  def sign_value N = value ((value N) select_second)
  ```

  * VALUE N
  ```haskell
  def VALUE N =
    if ISSIGNED N
    then ((value N) select_second)
    else SIGNED_ERROR
  ```

  * sign_iszero N
  ```haskell
  def sign_iszero N = iszero (sign_value N)
  ```

  2. Defined signed versions of `ISZERO`, `SUCC` and `PRED`
  * SIGN_ISZERO
  ```haskell
  def SIGN_ISZERO N
    if ISSIGNED N
    then MAKE_BOOL (sign_iszero N)
    else SIGNED_ERROR
  ```
  * SIGN_SUCC
  ```haskell
  def SIGN_SUCC N =
    IF SIGN_ISZERO N
    THEN +1
    ELSE
      if sign N
      then MAKE_SIGNED POS (MAKE_NUMB (succ (sign_value N)))
      else MAKE_SIGNED NEG (MAKE_NUMB (pred (sign_value N)))
  ```

  * SIGN_PRED
  ```haskell
  def SIGN_PRED N =
    IF SIGN_ISZERO N
    THEN -1
    ELSE
      if sign N
      then MAKE_SIGNED POS (MAKE_NUMB (pred (sign_value N)))
      else MAKE_SIGNED NEG (MAKE_NUMB (succ (sign_value N)))
  ```

  3. Define a signed version of `+`

  ```haskell
  def SIGN_+ X Y =
    if and (issigned X) (issigned Y)
    then
      if equiv (sign X) (sign Y)
      then MAKE_SIGNED (SIGN X) ((VALUE X) + (VALUE Y))
      else
        if less (sign_value X) (sign_value Y)
        then MAKE_SIGNED (SIGN Y) ((VALUE Y) - (VALUE X))
        else MAKE_SIGNED (SIGN X) ((VALUE X) - (VALUE Y))
    else SIGNED_ERROR
  ```
