# 1.1
Answer: Reason you get two different answers is because of "precedence".
```fsharp
> open System
- Math.Sqrt(1.0 + 3.0);;
val it : float = 2.0

> Math.Sqrt 1.0 + 3.0;;
val it : float = 4.0
```

# 1.2
NOTE: exclaim is maybe less domain specific but may serve useful in more
contexts than alluding to the implementation detail that `enthusiasticGreet`
somehow calls the greet function.

```fsharp
> let greet : string -> string = fun str => str + "Hello";

  let greet : string -> string = fun str => str + "Hello";
  ---------------------------------------^^

~/code/stdin(18,40): error FS0010: Unexpected infix operator in lambda expression. Expected '->' or other token.

- let greet : string -> string = fun str -> str + "Hello";
- ;
- ;;
> let greet : string -> string = fun str -> str + "Hello";;
val greet : str:string -> string

> greet "Stuff"
- ;;
val it : string = "StuffHello"

> let greet : string -> string = fun str -> str + " Hello";;
val greet : str:string -> string

> greet "Stuff";;
val it : string = "Stuff Hello"

> let exclaim : string -> string = fun str -> greet str + "!";;
val exclaim : str:string -> string

> exclaim "Sup"
- ;;
val it : string = "Sup Hello!"

> let greet : string -> string = fun str -> "Hello " + str;;
val greet : str:string -> string

> exclaim "Sup"
- ;;
val it : string = "Sup Hello!"

> let exclaim : string -> string = fun str -> greet str + "!";;
val exclaim : str:string -> string

> exclaim "Sup";;
val it : string = "Hello Sup!"

> exclaim "World";;
val it : string = "Hello World!"
```

# 1.3
Answer: Likely a type error.
```fsharp
> not(2 + 3 = 6 * 1);;
val it : bool = true
```

Wat???
```fsharp
> 2 + 3 = 6 * 1;;
val it : bool = false

> 2 + 3 == 6 * 1;;

  2 + 3 == 6 * 1;;
  ------^^

~/code/stdin(38,7): error FS0043: The type 'int' does not support the operator '=='

> 2 + 3 = 6 * 1;;
```

Didn't realize `=` worked both for matching and assignment in F# cool?
```fsharp
> let bar = true = false;;
val bar : bool = false
```

# 1.4
```fsharp
> (\str -> "Hello " + str) "F#";;

  (\str -> "Hello " + str) "F#";;
  -^

~/code/stdin(46,2): error FS0010: Unexpected character '\' in interaction

> (fun str -> "Hello " + str) "F#";;
val it : string = "Hello F#"
```

# 1.5
```fsharp
> let q x = x ** 2 + 4 * x + 4;;

  let q x = x ** 2 + 4 * x + 4;;
  ---------------------^

~/code/stdin(48,22): error FS0043: The type 'int' does not support the operator 'Pow'

> let q x = (x ** 2) + (4 * x) + 4;;

  let q x = (x ** 2) + (4 * x) + 4;;
  ------------------------^

~/code/stdin(49,25): error FS0043: The type 'int' does not support the operator 'Pow'

> let q x = (x * 2) + (4 x) + 4;;

  let q x = (x * 2) + (4 x) + 4;;
  ---------------------^

~/code/stdin(50,22): error FS0003: This value is not a function and cannot be applied.
```
One sec... lookin' up Multiplication in the F# docs...
https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/symbol-and-operator-reference/arithmetic-operators
^ suggests I should be able to use `*` as multiplication... and `**` as exponentiation...

```fsharp
> let q x = (x ** 2:int64) + (4:int64 * x) + 4:int64;;

  let q x = (x ** 2:int64) + (4:int64 * x) + 4:int64;;
  -----------^^^^^^

~/code/stdin(51,12): error FS0001: The type 'int64' does not support the operator 'Pow'

> let q x = (x ** 2:float) + (4:float * x) + 4:float;;

  let q x = (x ** 2:float) + (4:float * x) + 4:float;;
  ----------------^

~/code/stdin(52,17): error FS0001: This expression was expected to have type
    'float'
but here has type
    'int'

> let q x = (x ** 2.0) + (4.0 * x) + 4.0;;
val q : x:float -> float

> q -2
- ;;

  q -2
  --^^

~/code/stdin(54,3): error FS0001: This expression was expected to have type
    'float'
but here has type
    'int'

> q -2.0;;
val it : float = 0.0
```
Not sure I'm a fan of using floats here but I don't have a CS degree but from the docs...
> Summary of Binary Arithmetic Operators:
> The following table summarizes the binary arithmetic operators 
> that are available for unboxed integral and floating-point types.

And then digging into https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/basic-types
it's not immediately clear which int type is "unboxed" or integral for that matter.

Moving on.


# 1.6
Guestimation: Not sure, never used F# beyond simple examples so I'm not super familiar with the semantics of it's syntax.
```fsharp
> let f x = x = 3;;
val f : x:int -> bool

>
```
Answer: oh derp, assignment and equality are overloaded (?)...
soon as I saw the type it becomes pretty obvious what it does.
Compares the input for equality with 3 and returns a boolean.

# 1.7
NOTE: I can't remember exactly how factorial is implemented so
https://stackoverflow.com/a/59450526

```haskell
fact n = if n == 0 then 1 else n * fact(n-1)
fact n = foldl(*) 1 [1..n]
fact n = product [1..n]
```

```fsharp
> let fact n = if n = 0 then 1 else n * fact(n-1);;

  let fact n = if n = 0 then 1 else n * fact(n-1);;
  --------------------------------------^^^^

~/code/stdin(58,39): error FS0039: The value or constructor 'fact' is not defined.

> let fact n = if n = 0 then 1 else n * fact (n - 1);;

  let fact n = if n = 0 then 1 else n * fact (n - 1);;
  --------------------------------------^^^^

~/code/stdin(59,39): error FS0039: The value or constructor 'fact' is not defined.

> fun fact n = if n = 0 then 1 else n * fact (n - 1);;

  fun fact n = if n = 0 then 1 else n * fact (n - 1);;
  -----------^

~/code/stdin(60,12): error FS0010: Unexpected symbol '=' in lambda expression. Expected '->' or other token.

> let fact = fun n -> if n = 0 then 1 else n * fact (n - 1);;

  let fact = fun n -> if n = 0 then 1 else n * fact (n - 1);;
  ---------------------------------------------^^^^

~/code/stdin(61,46): error FS0039: The value or constructor 'fact' is not defined.

> let rec fact n = if n = 0 then 1 else n * fact (n - 1);;
val fact : n:int -> int

> let fact n = fold (*) 1 [1..n];;

  let fact n = fold (*) 1 [1..n];;
  -------------^^^^

~/code/stdin(63,14): error FS0039: The value or constructor 'fold' is not defined.

> let fact n = List.fold2 (*) 1 [1..n];;

  let fact n = List.fold2 (*) 1 [1..n];;
  -------------------------------^

~/code/stdin(64,32): error FS0001: The type ''a -> int' does not match the type 'int'

> let fact n = List.fold2 1 [1..n] (*);;

  let fact n = List.fold2 1 [1..n] (*);;
  ------------------------^

~/code/stdin(65,25): error FS0001: This expression was expected to have type
    ''a -> 'b -> 'c -> 'a'
but here has type
    'int'

> List.fold
- ;;
val it : (('a -> 'b -> 'a) -> 'a -> 'b list -> 'a)

> let fact n = List.fold (*) 1 [1..n];;
val fact : n:int -> int

> List.product
- ;;

  List.product
  -----^^^^^^^

~/code/stdin(69,6): error FS0039: The value, constructor, namespace or type 'product' is not defined.

> product
- ;;

  product
  ^^^^^^^

~/code/stdin(71,1): error FS0039: The value or constructor 'product' is not defined.
```

# 1.8
NOTE: Unless you care to dig into Recursion Schemes as mode of computation
I generally percieve low level recursion like this an antipattern compared to
higher order functions.
```fsharp
> let rec laugh n = "Ha" + laugh n - 1;;

  let rec laugh n = "Ha" + laugh n - 1;;
  ---------------------------------^

~/code/stdin(75,34): error FS0043: The type 'int' does not match the type 'string'

> let rec laugh n = "Ha" + (laugh n - 1);;

  let rec laugh n = "Ha" + (laugh n - 1);;
  -----------------------^

~/code/stdin(76,24): error FS0043: The type 'int' does not match the type 'string'

> let rec laugh n = "Ha" ++ (laugh n - 1);;

  let rec laugh n = "Ha" ++ (laugh n - 1);;
  -----------------------^^

~/code/stdin(77,24): error FS0043: None of the types 'string, int' support the operator '++'

> "Ha " + "Ha "
- ;;
val it : string = "Ha Ha "

> let rec laugh n = "Ha" + (laugh (n - 1));;
val laugh : n:int -> string

> laugh 2
- ;;
Stack overflow.
╭ /code
╰ 134  dotnet fsi --readline

Microsoft (R) F# Interactive version 10.9.1.0 for F# 4.7
Copyright (c) Microsoft Corporation. All Rights Reserved.

For help type #help;;

> > let rec laugh n = "Ha" + (laugh (n - 1));;

  > let rec laugh n = "Ha" + (laugh (n - 1));;
  ^

~/code/stdin(1,1): error FS0010: Unexpected symbol '>' in interaction

> let rec laugh n = if n <= 0 then "" else "Ha" + (laugh (n - 1));;
val laugh : n:int -> string

> laugh 3
- ;;
val it : string = "HaHaHa"

> laugh -5;;
val it : string = ""
```

# 1.9
```fsharp
> let intToBool x = x = 1;;
val intToBool : x:int -> bool

> let strToBool x = x = "";;
val strToBool : x:string -> bool

> let boolToFloat x = x = true ? 1.0 : 0;;

  let boolToFloat x = x = true ? 1.0 : 0;;
  -------------------------------^^^

~/code/stdin(8,32): error FS0010: Unexpected floating point literal in expression. Expected identifier, '(' or other token.

> let boolToFloat x = if x = true then 1.0 else 0;;

  let boolToFloat x = if x = true then 1.0 else 0;;
  ----------------------------------------------^

~/code/stdin(9,47): error FS0001: All branches of an 'if' expression must return values of the same type as the first branch, which here is 'float'. This branch returns a value of type 'int'.

> let boolToFloat x = if x = true then 1.0 else 0.0;;
val boolToFloat : x:bool -> float
```

# 1.10
Answer: String.length in every other language I've used should spit out the number of characters in a given string.
```fsharp
> String.length "Stuff"
- ;;
val it : int = 5
```
Yup...

# 1.11
```fsharp
> let ignore x = ();;
val ignore : x:'a -> unit
```

# 1.12
```fsharp
> let stringOfInt = string : int -> string;;
val stringOfInt : (int -> string)
```

# 1.13
Not sure if float has the same weird 0.1 + 0.2 thing that 
javascripts float thing has but baring and min/maxBounds `float(int(x))`
theoretically should return an equivalent float representation for all ints.

The same is not true for `int(float(x))` either it'll error out or `int` defines
some clamping mechanism on the input either `floor`, `ceil` or `round`?

I'd write a property based test as proof of my hypothesis but here's me just
dinkin' around in the repl...
```fsharp
> float(int(21));;
val it : float = 21.0

> float(int(-0))
- ;;
val it : float = 0.0

> float(int(-1000000000000000000000000000000000000000));;

  float(int(-1000000000000000000000000000000000000000));;
  -----------^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

~/code/stdin(18,12): error FS1147: This number is outside the allowable range for 32-bit signed integers

> float(int(1000000000000000000000000000000000000000));;

  float(int(1000000000000000000000000000000000000000));;
  ----------^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

~/code/stdin(19,11): error FS1147: This number is outside the allowable range for 32-bit signed integers

> float(int(1.0));;
val it : float = 1.0

> float(int(1.7));;
val it : float = 1.0

> float(int(1.7));;
```
Ah didn't infer that clamping would apply to floaty inputs to `float(int(x))`...
Also looks like the implied clamping is a `floor`
```fsharp
> int(float(1.7));;
val it : int = 1

> int(float(100000000000000000000000000000000000000000));;

  int(float(100000000000000000000000000000000000000000));;
  ----------^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

~/code/stdin(23,11): error FS1147: This number is outside the allowable range for 32-bit signed integers

> int(float(-5.12311312312312312312312312312312312312312399999999999999999999));;
val it : int = -5
```

# 1.14
Answer: identity!!! What does it do?! Besides not acting on it's input enables
ignoring the 'effect' of a higher order function in certain cases... also can be
lifted into the type system to provide isomorphisms between monad transformers
and their underlying concrete monads. If I'm remembering all this correctly.

# 1.15
```fsharp
> let f x = x ** 2;;

  let f x = x ** 2;;
  ------------^^

~/code/stdin(25,13): error FS0071: Type constraint mismatch when applying the default type 'float' for a type inference variable. The type 'int' does not match the type 'float' Consider adding further type constraints

> let f x = x ** 2:float;;

  let f x = x ** 2:float;;
  ---------------^

~/code/stdin(26,16): error FS0001: This expression was expected to have type
    'float'
but here has type
    'int'

> let f x = x:float ** 2;;

  let f x = x:float ** 2;;
  ------------------^^

~/code/stdin(27,19): error FS0010: Unexpected infix operator in binding. Expected incomplete structured construct at or before this point or other token.

> let f x:float = x ** 2;;

  let f x:float = x ** 2;;
  ---------------------^

~/code/stdin(28,22): error FS0001: This expression was expected to have type
    'float'
but here has type
    'int'

> let f x:float = x ** 2:float;;

  let f x:float = x ** 2:float;;
  ---------------------^

~/code/stdin(29,22): error FS0001: This expression was expected to have type
    'float'
but here has type
    'int'

> let f (x:float) = (x ** 2);;

  let f (x:float) = (x ** 2);;
  ------------------------^

~/code/stdin(30,25): error FS0001: This expression was expected to have type
    'float'
but here has type
    'int'

> let f x = (x ** 2);;

  let f x = (x ** 2);;
  -------------^^

~/code/stdin(31,14): error FS0071: Type constraint mismatch when applying the default type 'float' for a type inference variable. The type 'int' does not match the type 'float' Consider adding further type constraints

> f
- ;;

  f
  ^

~/code/stdin(32,1): error FS0039: The value or constructor 'f' is not defined.

> let f = (**) 2;;
val f : int = 2

> let f = (**)(2);;
val f : int = 2

> let f x = x * x;;
val f : x:int -> int

> let g x = x + 1;;
val g : x:int -> int

> f g 1 = g f 1;;

  f g 1 = g f 1;;
  ^^^

~/code/stdin(38,1): error FS0003: This value is not a function and cannot be applied.

> f (g 1) = g (f 1);;
val it : bool = false

> f (g 0) = g (f 0);;
val it : bool = true

> let p x = x - 6;;
val p : x:int -> int

> (p >> g) 0 = (p << g) 0;;
val it : bool = true

> (p >> g) 1 = (p << g) 1;;
val it : bool = true
```

# 1.16
Guess: 3
```fsharp
> let rec sumTo n = if n = 0 then 0 else n + sumTo(n-1);;
val sumTo : n:int -> int

> (sumTo >> sumTo) 3;;
val it : int = 21

> sumTo 3
- ;;
val it : int = 6
```
Note: Oh, okay it's more of a scan than a counter. Reasons I don't like recursion.


# 1.17
```fsharp
> let is3 = (=) 3;;
val is3 : (int -> bool)

> let strLen3 = String.length >> is3;;
val strLen3 : (string -> bool)

> strLen3 "";;
val it : bool = false

> strLen3 "asd"
- ;;
val it : bool = true
```

# 1.18
```fsharp
> let is3StrLen = is3 >> String.length;;

  let is3StrLen = is3 >> String.length;;
  -----------------------^^^^^^^^^^^^^

~/code/stdin(55,24): error FS0001: Type mismatch. Expecting a
    'bool -> 'a'
but given a
    'string -> int'
The type 'bool' does not match the type 'string'
```
Fill In the Blank: I did a quick search for a bool -> string func but google cmd-F
is failing me so `let boolToStr x:bool = "blah";;` should compose otherwise I'd
generally go with something like `is3 >> const "blah" >> String.length` although,
I'd never write that in production code.


# 1.19
```fsharp
> let lengthOfLaughs = laugh >> String.length;;
val lengthOfLaughs : (int -> int)

> lengthOfLaughs 3;;
val it : int = 6
```
???

# 1.20
Guess: `((*) 3.14): float -> float`, `((-) 4): int -> int`, `((=) false) : bool -> bool`
```fsharp
> ((*) 3.14)
- ;;
val it : (float -> float) = <fun:it@58>

> ((-) 4);;
val it : (int -> int) = <fun:it@60-1>

> ((=) false);;
val it : (bool -> bool) = <fun:it@61-2>
```
Follow Up: ???

# 1.21
Guess: `c x : 'y -> 'x` for some given type of x... it's the `const` function.
```fsharp
let const_ x y = x;;
```
Answer: lol keywords.
```fsharp
> let const x y = x;;

  let const x y = x;;
  ----^^^^^

~/code/stdin(62,5): error FS0010: Unexpected keyword 'const' in binding

> const 1 2;;

  const 1 2;;
  ^^^^^

~/code/stdin(63,1): error FS0010: Unexpected keyword 'const' in interaction

> let const_ x y = x;;
val const_ : x:'a -> y:'b -> 'a

> const_ 1 2
- ;;
val it : int = 1

> const_ 1;;

  const_ 1;;
  ^^^^^^^^

~/code/stdin(67,1): error FS0030: Value restriction. The value 'it' has been inferred to have generic type
    val it : ('_a -> int)
Either make the arguments to 'it' explicit or, if you do not intend for it to be generic, add a type annotation.
```

# 1.22
NOTE: I hate recurssion so much.
```fsharp
Stack Overflow
```
:man_facepalming:
```fsharp
> let rec pown x p = if p = 0 then 1 else x * (pown x (p - 1));;
val pown : x:int -> p:int -> int

> pown 2 2;;
val it : int = 4

> pown 3 2;;
val it : int = 9
```
Got it.

# 1.23
```fsharp
> let revComp<'A, 'B, 'C> (f: ('A -> 'B)) (g: ('B -> 'C)) (x: 'A) : 'C = g(f(x));;
val revComp : f:('A -> 'B) -> g:('B -> 'C) -> x:'A -> 'C

> let revComp<'A, 'B, 'C> (f: ('B -> 'C)) (g: ('A -> 'B)) (x: 'A) : 'C = f(g(x));;
val revComp : f:('B -> 'C) -> g:('A -> 'B) -> x:'A -> 'C

> (>>);;
val it : (('a -> 'b) -> ('b -> 'c) -> 'a -> 'c)

> (<<);;
val it : (('a -> 'b) -> ('c -> 'a) -> 'c -> 'b)
```
NOTE: Confused about the different capitalizations of Generic types...
Seems like in F# you can use either C#-y or Haskell-y notations?

# 1.24
```fsharp
> let twice f x = f(f(x));;
val twice : f:('a -> 'a) -> x:'a -> 'a
```

# 1.25
Question: What happens if you call `repeat 0 f` or `repeat -1 f`?
Shouldn't `repeat` be `Nat -> ('a -> 'a) -> ???`
```fsharp
> let repeat f n = if n <= 0 then f else f >> repeat f (n - 1);;

  let repeat f n = if n <= 0 then f else f >> repeat f (n - 1);;
  --------------------------------------------^^^^^^

~/code/stdin(11,45): error FS0039: The value or constructor 'repeat' is not defined.
  (repeat ((+): int -> int) 2) 2 2;;

  (repeat ((+): int -> int) 2) 2 2;;
  ----------^

~/code/stdin(17,11): error FS0001: Type mismatch. Expecting a
    'int -> int'
but given a
    'int -> 'a -> 'b'
The type 'int' does not match the type ''a -> 'b'

> (repeat ((+): int -> int) 2) 2;;

  (repeat ((+): int -> int) 2) 2;;
  ----------^

~/code/stdin(18,11): error FS0001: Type mismatch. Expecting a
    'int -> int'
but given a
    'int -> 'a -> 'b'
The type 'int' does not match the type ''a -> 'b'

> let add (x:int) (y:int) = x + y;;
val add : x:int -> y:int -> int

> let incr (x: int) = x + 1;;
val incr : x:int -> int

> (repeat (incr) 2) 1;;
val it : int = 4
```

# 1.26
```fsharp
> let rec pown (x, p) = if p <= 0 then 1 else x * pown (x, p - 1);;
val pown : x:int * p:int -> int

> pown (2, 3);;
val it : int = 8
```

# 1.27
```fsharp
> let curry_ f x y = f (x, y);;
val curry_ : f:('a * 'b -> 'c) -> x:'a -> y:'b -> 'c
```

# 1.28
Answer: 1 & 4, 2 & 3 are equivalent.
