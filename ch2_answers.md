# 2.1
```fsharp
> let one (x,y) = true;;
val one : x:'a * y:'b -> bool

> let one (x:string,y:int) = true;;
val one : x:string * y:int -> bool

> let two = (x:string,(y:int -> bool));;

  let two = (x:string,(y:int -> bool));;
  -------------------^

~/code/fpm_book/stdin(3,20): error FS0010: Unexpected symbol ',' in expression

> let two = ("blah", fun (y:int) -> true);;
val two : string * (int -> bool) = ("blah", <fun:two@4>)
```
Answer: Does F# have "kinds"? EG: `* -> *` like in Haskell? 
If not, then parens play a pretty significant role in what a type 'means' for
values to be able to adhere to a given shape? It's the same as it is in Math.


# 2.2
```fsharp
> let three = (2.0, fun (x:string, y:int) -> true);;
val three : float * (string * int -> bool) = (2.0, <fun:three@6>)
```

# 2.3
NOTE: tuple of string * int is not how I'd model a card game where there's only
52 cards in a deck but I'll play along for simplicities sake...
```fsharp
> let cardName (card: Card) =
-   match card with
-   | (suit, 1) -> "ace of " + suit
-   | (suit, 10) -> "jack of " + suit
-   | (suit, 11) -> "queen of " + suit
-   | (suit, 12) -> "king of " + suit
-   | (suit, num) when 2 <= num && num <= 9  -> num + " of " + suit
-   | _ -> "Joker";;

    | (suit, num) when 2 <= num && num <= 9  -> num + " of " + suit
  ----------------------------------------------------^^^^^^

~/code/fpm_book/stdin(39,53): error FS0001: The type 'string' does not match the type 'int'

> let cardName (card: Card) =
-   match card with
-   | (suit, 1) -> "ace of " + suit
-   | (suit, 10) -> "jack of " + suit
-   | (suit, 11) -> "queen of " + suit
-   | (suit, 12) -> "king of " + suit
-   | (suit, num) when 2 <= num && num <= 9  -> string(num) + " of " + suit
-   | _ -> "Joker";;
val cardName : string * int -> string

> cardName ("diamonds", 3);;
val it : string = "3 of diamonds"

> cardName ("diamonds", 15);;
val it : string = "Joker"

> cardName ("diamonds", 1);;
val it : string = "ace of diamonds"

> cardName ("spades", 1);;
val it : string = "ace of spades"

> cardName ("spades", 10);;
val it : string = "jack of spades"

> cardName ("spades", 11);;
val it : string = "queen of spades"

> cardName ("spades", 12);;
val it : string = "king of spades"

> cardName ("spades", 13);;
val it : string = "Joker"
```

# 2.4
```fsharp
> let fst (f, _) = fst;;
val fst : f:'a * 'b -> ('c * 'd -> 'c)

> let snd (_, snd) = snd;;
val snd : 'a * snd:'b -> 'b

> fst (1, 2);;

  fst (1, 2);;
  ^^^^^^^^^^

~/code/fpm_book/stdin(59,1): error FS0030: Value restriction. The value 'it' has been inferred to have generic type
    val it : ('_a * '_b -> '_a)
Either make the arguments to 'it' explicit or, if you do not intend for it to be generic, add a type annotation.

> let fst (f, _) = f;;
val fst : f:'a * 'b -> 'a

> fst (1, 2);;
val it : int = 1

> let snd (_, s) = s;;
val snd : 'a * s:'b -> 'b

> snd (1, 2)
- ;;
val it : int = 2
```

# 2.5
```fsharp
> let diag x = (x, x);;
val diag : x:'a -> 'a * 'a
```

# 2.6
NOTE: Generally I wouldn't write these sort of functions because an Array is a better
data structure for accessing an Nth element. Assuming we're not getting into the
dependant domain of Heterogenous Lists and such...
```fsharp
> let proj1of3 (one, _, _) = one;;
val proj1of3 : one:'a * 'b * 'c -> 'a

> let proj2of3 (_, two, _) = two;;
val proj2of3 : 'a * two:'b * 'c -> 'b

> let proj3of3 (_, _, three) = three;;
val proj3of3 : 'a * 'b * three:'c -> 'c
```

# 2.7
```fsharp
> let thrupleToTupleFst (a,b,c) = ((a,b),c);;
val thrupleToTupleFst : a:'a * b:'b * c:'c -> ('a * 'b) * 'c

> thrupleToTupleSnd (a,b,c) = (a,(b,c));;

  thrupleToTupleSnd (a,b,c) = (a,(b,c));;
  ^^^^^^^^^^^^^^^^^

~/code/fpm_book/stdin(70,1): error FS0039: The value or constructor 'thrupleToTupleSnd' is not defined. Maybe you want one of the following:
   thrupleToTupleFst

> let thrupleToTupleSnd (a,b,c) = (a,(b,c));;
val thrupleToTupleSnd : a:'a * b:'b * c:'c -> 'a * ('b * 'c)

> let tupleFstToThruple ((a,b),c) = (a,b,c);;
val tupleFstToThruple : ('a * 'b) * c:'c -> 'a * 'b * 'c

> let tupleSndToThruple (a,(b,c)) = (a,b,c);;
val tupleSndToThruple : a:'a * ('b * 'c) -> 'a * 'b * 'c

> let tupleFstToTupleSnd ((a,b), c) = (a, (b,c));;
val tupleFstToTupleSnd : ('a * 'b) * c:'c -> 'a * ('b * 'c)

> let tupleSndToTupleFst (a,(b,c)) = ((a,b),c);;
val tupleSndToTupleFst : a:'a * ('b * 'c) -> ('a * 'b) * 'c
```

# 2.8
```fsharp
> let f = fst >> snd >> fst;;

  let f = fst >> snd >> fst;;
  ----^

~/code/fpm_book/stdin(76,5): error FS0030: Value restriction. The value 'f' has been inferred to have generic type
    val f : (('_a * ('_b * '_c)) * '_d -> '_b)
Either make the arguments to 'f' explicit or, if you do not intend for it to be generic, add a type annotation.
```
NOTE: Running into the value restriction apparently inherited from OCaml...
looking into how to add explicit forall annotations to keep the type parameters
fully generic.

NOTE: Okay does fsharp not have explicit universal quanitfication? In which case
the exercise is a touch misleading... but I'm probably just missing something simple.

```fsharp
> (>>)(fst);;

  (>>)(fst);;
  -^^^^^^^^

~/code/fpm_book/stdin(82,2): error FS0030: Value restriction. The value 'it' has been inferred to have generic type
    val it : (('_a -> '_b) -> '_a * '_c -> '_b)
Either make the arguments to 'it' explicit or, if you do not intend for it to be generic, add a type annotation.

> (>>)(fst)(snd);;

  (>>)(fst)(snd);;
  -^^^^^^^^^^^^^

~/code/fpm_book/stdin(83,2): error FS0030: Value restriction. The value 'it' has been inferred to have generic type
    val it : (('_a * '_b) * '_c -> '_b)
Either make the arguments to 'it' explicit or, if you do not intend for it to be generic, add a type annotation.

> (>>)fst snd;;

  (>>)fst snd;;
  -^^^^^^^^^^

~/code/fpm_book/stdin(84,2): error FS0030: Value restriction. The value 'it' has been inferred to have generic type
    val it : (('_a * '_b) * '_c -> '_b)
Either make the arguments to 'it' explicit or, if you do not intend for it to be generic, add a type annotation.

> (>>) fst snd;;

  (>>) fst snd;;
  -^^^^^^^^^^^

~/code/fpm_book/stdin(85,2): error FS0030: Value restriction. The value 'it' has been inferred to have generic type
    val it : (('_a * '_b) * '_c -> '_b)
Either make the arguments to 'it' explicit or, if you do not intend for it to be generic, add a type annotation.

> fst << snd
- ;;

  fst << snd
  ^^^^^^^^^^

~/code/fpm_book/stdin(86,1): error FS0030: Value restriction. The value 'it' has been inferred to have generic type
    val it : ('_a * ('_b * '_c) -> '_b)
Either make the arguments to 'it' explicit or, if you do not intend for it to be generic, add a type annotation.

> let fst ((a:A), (b: B)): A = a;;

  let fst ((a:A), (b: B)): A = a;;
  ------------^

~/code/fpm_book/stdin(88,13): error FS0039: The type 'A' is not defined.

> let fst ((a: 'A), (b: 'B)): 'A = a;;
val fst : a:'A * b:'B -> 'A

> let snd ((a: 'A), (b: 'B)): 'B = b;;
val snd : a:'A * b:'B -> 'B

> fst >> snd;;

  fst >> snd;;
  ^^^^^^^^^^

~/code/fpm_book/stdin(91,1): error FS0030: Value restriction. The value 'it' has been inferred to have generic type
    val it : (('_a * '_b) * '_c -> '_b)
Either make the arguments to 'it' explicit or, if you do not intend for it to be generic, add a type annotation.

> fst;;
val it : ('a * 'b -> 'a)

> (>>);;
val it : (('a -> 'b) -> ('b -> 'c) -> 'a -> 'c)

> (fst >> snd: val it : (('a -> 'b) -> ('b -> 'c) -> 'a -> 'c))

  (fst >> snd: val it : (('a -> 'b) -> ('b -> 'c) -> 'a -> 'c))
  -------------^^^

~/code/fpm_book/stdin(94,14): error FS0010: Unexpected keyword 'val' in interaction
  let f: (('a * ('b * 'c)) * 'd) -> 'b = fst >> snd;;

  let f: (('a * ('b * 'c)) * 'd) -> 'b = fst >> snd;;
  ----------------------------------------------^^^

~/code/fpm_book/stdin(101,47): error FS0001: Type mismatch. Expecting a
    ''a * ('b * 'c) -> 'b'
but given a
    ''a * ('b * 'c) -> 'b * 'c'
The types ''b' and ''b * 'c' cannot be unified.

> let f: (('a * ('b * 'c)) * 'd) -> 'b * 'c = fst >> snd;;

  let f: (('a * ('b * 'c)) * 'd) -> 'b * 'c = fst >> snd;;
  ----^

~/code/fpm_book/stdin(102,5): error FS0030: Value restriction. The value 'f' has been inferred to have generic type
    val f : (('_a * ('_b * '_c)) * '_d -> '_b * '_c)
Either make the arguments to 'f' explicit or, if you do not intend for it to be generic, add a type annotation.

> let f: ('a * 'b) * 'c -> 'b = fst >> snd;;
val f : ((int * (int * int)) * int -> int * int)
```

# 2.9
```fsharp
> type Card = {Suit : string, Rank : int};;

  type Card = {Suit : string, Rank : int};;
  --------------------------^

~/code/fpm_book/stdin(114,27): error FS0010: Unexpected symbol ',' in type definition. Expected '}' or other token.

> type Card = {Suit : string\n Rank : int};;

  type Card = {Suit : string\n Rank : int};;
  --------------------------^

~/code/fpm_book/stdin(115,27): error FS0010: Unexpected character '\' in type definition. Expected '}' or other token.

> type Card = {Suit : string Rank : int};;

  type Card = {Suit : string Rank : int};;
  --------------------------------^

~/code/fpm_book/stdin(116,33): error FS0010: Unexpected symbol ':' in type definition. Expected '}' or other token.

> type Card = {Suit : string
- Rank : int};;

  Rank : int};;
  -----^

~/code/fpm_book/stdin(118,6): error FS0010: Unexpected symbol ':' in type definition. Expected '}' or other token.

> type Card =
-   {
-     Suit : string
-     Rank : int
-   };;
type Card =
  { Suit: string
    Rank: int }
    | {Suit; Rank} when Suit in ["hearts", "spades", "clubs", "diamonds"] && Rank in [1..- 12] -> true

    | {Suit; Rank} when Suit in ["hearts", "spades", "clubs", "diamonds"] && Rank in [1..12] -> true
  --^

~/code/fpm_book/stdin(133,3): error FS0010: Unexpected symbol '|' in interaction

- let isValid: Card -> bool = function
-   | {Suit; Rank} when Suit in ["hearts", "spades", "clubs", "diamonds"] && Rank in [1..12] -> true
-   | _ -> false
- ;;
> isValid
- ;;

  isValid
  ^^^^^^^

~/code/fpm_book/stdin(138,1): error FS0039: The value or constructor 'isValid' is not defined. Maybe you want one of the following:
   invalidOp

> isValid;;

  isValid;;
  ^^^^^^^

~/code/fpm_book/stdin(140,1): error FS0039: The value or constructor 'isValid' is not defined. Maybe you want one of the following:
   invalidOp

> let isValid ({Rank, Suit}:Card): bool = Suit in ["hearts", "spades", "clubs", "diamonds"] && Rank in [1..12];;

  let isValid ({Rank, Suit}:Card): bool = Suit in ["hearts", "spades", "clubs", "diamonds"] && Rank in [1..12];;
  ------------------^

~/code/fpm_book/stdin(141,19): error FS0010: Unexpected symbol ',' in pattern. Expected '.', '=' or other token.

> let isValid ({Rank; Suit}:Card): bool = Suit in ["hearts", "spades", "clubs", "diamonds"] && Rank in [1..12];;

  let isValid ({Rank; Suit}:Card): bool = Suit in ["hearts", "spades", "clubs", "diamonds"] && Rank in [1..12];;
  ------------------^

~/code/fpm_book/stdin(142,19): error FS0010: Unexpected symbol ';' in pattern. Expected '.', '=' or other token.

> let isValid ({Rank. Suit}:Card): bool = Suit in ["hearts", "spades", "clubs", "diamonds"] && Rank in [1..12];;

  let isValid ({Rank. Suit}:Card): bool = Suit in ["hearts", "spades", "clubs", "diamonds"] && Rank in [1..12];;
  ------------------------^

~/code/fpm_book/stdin(143,25): error FS0010: Unexpected symbol '}' in pattern. Expected '.', '=' or other token.

> let isValid (c:Card): bool = c.Suit in ["hearts", "spades", "clubs", "diamonds"] && c.Rank in [1..12];;

  let isValid (c:Card): bool = c.Suit in ["hearts", "spades", "clubs", "diamonds"] && c.Rank in [1..12];;
  -------------------------------------------------------------------------------------------^^

~/code/fpm_book/stdin(144,92): error FS0010: Unexpected keyword 'in' in interaction. Expected incomplete structured construct at or before this point, ';', ';;' or other token.

> let isValid (c:Card): bool = c.Suit in ["hearts", "spades", "clubs", "diamonds"] && c.Rank in [1...12];;

  let isValid (c:Card): bool = List.exists ((==) c.Suit) ["hearts", "spades", "clubs", "diamonds"] && List.exists ((==) c.Rank) [1.
.12];;
  let isValid (c:Card): bool = List.exists ((==) c.Suit) ["hearts", "spades", "clubs", "diamonds"] && List.exists ((==) c.Rank) [1..12];;
  --------------------------------------------------------^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

~/code/fpm_book/stdin(152,57): error FS0001: Expecting a type supporting the operator '==' but given a tuple type

> let isValid (c:Card): bool = (List.exists ((==) c.Suit) ["hearts", "spades", "clubs", "diamonds"]) && (List.exists ((==) c.Rank) - [1..12]);;
  ["stuff"];;
val it : string list = ["stuff"]List.exists ((==) c.Suit) ["hearts", "spades", "clubs", "diamonds"]) && (List.exists ((==) c.Rank) [1..12]);;
  ---------------------------------------------------------^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
> List.exists;;
val it : (('a -> bool) -> 'a list -> bool)

> List.exists ((=) "stuff");;
val it : (string list -> bool) = <fun:it@162-9>

> List.exists ((=) "stuff") ["stuff"];;
val it : bool = true

> List.exists ((=) "stuff") ["stuff", "things"];;

  List.exists ((=) "stuff") ["stuff", "things"];;
  ---------------------------^^^^^^^^^^^^^^^^^

~/code/fpm_book/stdin(164,28): error FS0001: This expression was expected to have type
    'string'
but here has type
    ''a * 'b'

> List.exists ((=) "stuff") ["stuff"; "things"];;
val it : bool = true

> let isValid (c:Card): bool = (List.exists ((=) c.Suit) ["hearts"; "spades"; "clubs"; "diamonds"]) && (List.exists ((=) c.Rank) [1
val isValid : c:Card -> bool

> isValid {Suit="Stuff";Rank=1};;
val it : bool = false

> isValid {Suit="spades";Rank=1};;
val it : bool = true
```
NOTE: Just gettin' the hang of how concepts from Haskell et al. map to F# :/

# 2.10
```fsharp
> let map = Map.ofList [1, "Ace"; 10, "Jack"; 11, "Queen"; 12, "King"];;
val map : Map<int,string> =
  map [(1, "Ace"); (10, "Jack"); (11, "Queen"); (12, "King")]

> blah [1,"asdf";2,"qwerr"];;

  blah [1,"asdf";2,"qwerr"];;
  ^^^^

~/code/fpm_book/stdin(170,1): error FS0039: The value or constructor 'blah' is not defined.

> map [1,"asdf";2,"qwerr"];;

  map [1,"asdf";2,"qwerr"];;
  ^^^

~/code/fpm_book/stdin(171,1): error FS3217: This value is not a function and cannot be applied. Did you intend to access the indexer via map.[index] instead?

> let cardName ({Rank}: Card) = List.exists ((=) Rank) [2..9] ? Rank : (Map.tryFind Rank map |> Option.defaultWith("Not a Card"));;-

  let cardName ({Rank}: Card) = List.exists ((=) Rank) [2..9] ? Rank : (Map.tryFind Rank map |> Option.defaultWith("Not a Card"));;
  -------------------^

~/code/fpm_book/stdin(172,20): error FS0010: Unexpected symbol '}' in pattern. Expected '.', '=' or other token.

> let cardName { Rank } = List.exists ((=) Rank) [2..9] ? Rank : (Map.tryFind Rank map |> Option.defaultWith("Not a Card"));;

  let cardName { Rank } = List.exists ((=) Rank) [2..9] ? Rank : (Map.tryFind Rank map |> Option.defaultWith("Not a Card"));;
  --------------------^

~/code/fpm_book/stdin(173,21): error FS0010: Unexpected symbol '}' in pattern. Expected '.', '=' or other token.

> let cardName { Rank=r } = List.exists ((=) r) [2..9] ? r : (Map.tryFind r map |> Option.defaultWith("Not a Card"));;

  let cardName { Rank=r } = List.exists ((=) r) [2..9] ? r : (Map.tryFind r map |> Option.defaultWith("Not a Card"));;
  ------------------------------------------------------------------------------^^

~/code/fpm_book/stdin(174,79): error FS0010: Unexpected infix operator in binding. Expected ',' or other token.

> let cardName { Rank=r } = List.exists ((=) r) [2..9] ? r : ((Map.tryFind r map) |> Option.defaultWith("Not a Card"));;

  let cardName { Rank=r } = List.exists ((=) r) [2..9] ? r : ((Map.tryFind r map) |> Option.defaultWith("Not a Card"));;
  --------------------------------------------------------------------------------^^

~/code/fpm_book/stdin(175,81): error FS0010: Unexpected infix operator in binding. Expected ',' or other token.

> Option.defaultWith;;
val it : ((unit -> 'a) -> 'a option -> 'a)

> Option.get;;
val it : ('a option -> 'a)

> Option.default;;

  Option.default;;
  ------^

~/code/fpm_book/stdin(178,7): error FS0599: Missing qualification after '.'

> Option.defaultWith;;
val it : ((unit -> 'a) -> 'a option -> 'a)

> let cardName { Rank=r } = List.exists ((=) r) [2..9] ? r : ((Map.tryFind r map) |> Option.defaultWith(() => "Not a Card"));;

  let cardName { Rank=r } = List.exists ((=) r) [2..9] ? r : ((Map.tryFind r map) |> Option.defaultWith(() => "Not a Card"));;
  --------------------------------------------------------------------------------^^

/Ulet cardName { Rank=r } = List.exists ((=) r) [2..9] ? string(r) : Option.defaultWith (fun () -> "Not a Card") (Map.tryFind r map
- );;
  let cardName { Rank=r } = List.exists ((=) r) [2..9] ? string(r) : Option.defaultWith (fun () -> "Not a Card") (Map.tryFind r map);;
  ----------------------------------------------^^^^^^^^^^^^^^^^^^

~/code/fpm_book/stdin(188,47): error FS0597: Successive arguments should be separated by spaces or tupled, and arguments involving function or method applications should be parenthesized

> let cardName { Rank=r } = (List.exists ((=) r) [2..9]) ? string(r) : Option.defaultWith (fun () -> "Not a Card") (Map.tryFind r m
- ap);;
  let cardName { Rank=r } = (List.exists ((=) r) [2..9]) ? string(r) : Option.defaultWith (fun () -> "Not a Card") (Map.tryFind r map);;
  ----------------------------------------------------------------------------------------^

~/code/fpm_book/stdin(189,89): error FS0010: Unexpected symbol '(' in binding. Expected incomplete structured construct at or before this point or other token.

> let cardName { Rank=r } = (List.exists ((=) r) [2..9]) ? (string r) : Option.defaultWith (fun () -> "Not a Card") (Map.tryFind r
- map);;
  let cardName { Rank=r } = (List.exists ((=) r) [2..9]) ? (string r) : Option.defaultWith (fun () -> "Not a Card") (Map.tryFind r map);;
  -----------------------------------------------------------------------------------------^

~/code/fpm_book/stdin(190,90): error FS0010: Unexpected symbol '(' in binding. Expected incomplete structured construct at or before this point or other token.

> string 1
- ;;
val it : string = "1"

> let cardName { Rank=r } = if List.exists ((=) r) [2..9] then (string r) else Option.defaultWith (fun () -> "Not a Card") (Map.try
val cardName : Card -> string

> cardName {Suit="diamonds"; Rank=20};;
val it : string = "Not a Card"

> cardName {Suit="diamonds"; Rank=2};;
val it : string = "2"

> cardName {Suit="diamonds"; Rank=1};;
val it : string = "Ace"

> cardName {Suit="diamonds"; Rank=10};;
val it : string = "Jack"

> cardName {Suit="diamonds"; Rank=11};;
val it : string = "Queen"

> cardName {Suit="diamonds"; Rank=12};;
val it : string = "King"

> cardName {Suit="diamonds"; Rank=13};;
val it : string = "Not a Card"

> let cardName { Rank=r } = if List.exists ((=) r) [2..9] then (string r) else Map.tryFind r map |> Option.defaultWith (fun () -> "Not a Card");;
val cardName : Card -> string

> let cardName { Rank=r, Suit=s } = if List.exists ((=) r) [2..9] then s + " of " + (string r) else Map.tryFind r map |> Option.map
-  (fun rnk -> rnk + " of " + s) |> Option.defaultWith (fun () -> "Not a Card");;
  let cardName { Rank=r, Suit=s } = if List.exists ((=) r) [2..9] then s + " of " + (string r) else Map.tryFind r map |> Option.map (fun rnk -> rnk + " of " + s) |> Option.defaultWith (fun () -> "Not a Card");;
  ---------------------------^

~/code/fpm_book/stdin(202,28): error FS0010: Unexpected symbol '=' in pattern. Expected '}' or other token.

> let cardName { Rank=r; Suit=s } = if List.exists ((=) r) [2..9] then s + " of " + (string r) else Map.tryFind r map |> Option.map
val cardName : Card -> string s) |> Option.defaultWith (fun () -> "Not a Card");;

> let cardName: Card -> string = function
-   | {Rank=r; Suit=s} when s in ["hearts"; "clubs"; "diamonds"; "spades"] && r in [1..12] -> if List.exists ((=) r) [2..9] then s - + " of " + (string r) else Map.tryFind r map |> Option.map

    | {Rank=r; Suit=s} when s in ["hearts"; "clubs"; "diamonds"; "spades"] && r in [1..12] -> if List.exists ((=) r) [2..9] then s + " of " + (string r) else Map.tryFind r map |> Option.map
  ----------------------------^^

~/code/fpm_book/stdin(205,29): error FS0010: Incomplete structured construct at or before this point in pattern matching. Expected '->' or other token.

- let cardName: Card -> string = function
-   | {Rank=r; Suit=s} when s in ["hearts"; "clubs"; "diamonds"; "spades"] && r in [1..12] -> if List.exists ((=) r) [2..9] then s - + " of " + (string r) else Map.tryFind r map |> Option.map (fun rnk -> rnk + " of " + s) |> Option.defaultWith (fun () -> "Not a Card")
-   | _ -> "Not a Card";;
> cardName;;
val it : (Card -> string) = <fun:it@209-16>

> cardName {Suit="blah";Rank=12};;
val it : string = "King of blah"

> "sting" in ["sting"; "other"];;

  "sting" in ["sting"; "other"];;
  --------^^

~/code/fpm_book/stdin(211,9): error FS0010: Unexpected keyword 'in' in interaction. Expected incomplete structured construct at or before this point, ';', ';;' or other token.

> ["sting"; "other"].Contains("sting");;

  ["sting"; "other"].Contains("sting");;
  -------------------^^^^^^^^

~/code/fpm_book/stdin(212,20): error FS0039: The type 'List<T>' does not define the field, constructor or member 'Contains'. Maybe you want one of the following:
   Cons

> let cardNomenclature: Card -> string = function
-   | {Rank=r; Suit=s} when List.contains s ["hearts"; "clubs"; "diamonds"; "spades"] && List.contains r [1..12] -> if List.contain- s r [2..9] then (string r) + " of " s else Map.tryFind r map |> Option.map (fun rnk -> rnk + " of " + s) |> Option.defaultWith (f- un () -> "Not a Card")
-   | _ -> "Not a Card";;

    | {Rank=r; Suit=s} when List.contains s ["hearts"; "clubs"; "diamonds"; "spades"] && List.contains r [1..12] -> if List.contains r [2..9] then (string r) + " of " s else Map.tryFind r map |> Option.map (fun rnk -> rnk + " of " + s) |> Option.defaultWith (fun () -> "Not a Card")
  --------------------------------------------------------------------------------------------------------------------------------------------------------------^^^^^^

~/code/fpm_book/stdin(229,159): error FS0003: This value is not a function and cannot be applied.

> let cardNomenclature: Card -> string = function
-   | {Rank=r; Suit=s} when List.contains s ["hearts"; "clubs"; "diamonds"; "spades"] && List.contains r [1..12] -> if List.contain- s r [2..9] then (string r) + " of " + s else Map.tryFind r map |> Option.map (fun rnk -> rnk + " of " + s) |> Option.defaultWith
-   | _ -> "Not a Card";;)
val cardNomenclature : _arg1:Card -> string

> cardNomenclature {Suit="diamonds";Rank=2};;
val it : string = "2 of diamonds"

> cardNomenclature {Suit="diamonds";Rank=12};;
val it : string = "King of diamonds"

> cardNomenclature {Suit="diamonds";Rank=1};;
val it : string = "Ace of diamonds"

> cardNomenclature {Suit="spades";Rank=1};;
val it : string = "Ace of spades"
```
Newlines got a little tangled... meh.

# 2.11
```fsharp
> let pocketPair ({Rank=r1}, {Rank=r2}) = r1 = r2;;
val pocketPair : Card * Card -> bool

> pocketPair ({Rank=1; Suit="diamonds"}, {Rank=1; Suit="whatever"});;
val it : bool = true
```

# 2.12
```fsharp
> type Iso1 = (string * int) * bool;;
type Iso1 = (string * int) * bool

> type Iso2 = string * int * bool;;
type Iso2 = string * int * bool

> type IsoRecord = {One: string; Two: int; Three: bool};;
type IsoRecord =
  { One: string
    Two: int
    Three: bool }

> let iso1To2: Iso1 -> Iso2 = fun ((str, integer), bule) -> str, integer, bule;;
val iso1To2 : (string * int) * bule:bool -> Iso2

> let iso2To1: Iso2 -> Iso1 = fun (str, integer, bule) -> (str, integer), bule;;
val iso2To1 : str:string * integer:int * bule:bool -> Iso1

> (iso1To2 >> iso2To1) (("stuff", 1), true) = (("stuff", 1), true);;
val it : bool = true

> let iso1ToRecord: Iso1 -> IsoRecord = fun ((str, integer), bule) -> {One=str;Two=integer;Three=bule};;
val iso1ToRecord : (string * int) * bule:bool -> IsoRecord

> let isoRecordTo1: IsoRecord -> Iso1 = fun {One=str;Two=integer;Three=bule} -> (str, integer), bule;;
val isoRecordTo1 : IsoRecord -> Iso1

> (iso1ToRecord >> isoRecordTo1) (("stuff", 1), false) = (("stuff", 1), false);;
val it : bool = true

> let iso2ToRecord : Iso2 -> IsoRecord = fun (str, integer, bule) -> {One=str;Two=integer;Three=bule};;
val iso2ToRecord : str:string * integer:int * bule:bool -> IsoRecord

> let isoRecordTo2: IsoRecord -> Iso2 = fun {One=str;Two=integer;Three=bule} -> str, integer, bule;;
val isoRecordTo2 : IsoRecord -> Iso2

> (iso2ToRecord >> isoRecordTo2) ("stuff", 1, false) = ("stuff", 1, false);;
val it : bool = true
```

# 2.13
```fsharp
> let swap (a, b) = b,a;;
val swap : a:'a * b:'b -> 'b * 'a

> (swap >> swap) (1, 2) = (1, 2);;
val it : bool = true

> (swap >> swap) ("stuff", 2) = ("stuff", 2);;
val it : bool = true

> (swap >> swap) ("stuff", 2.0) = ("stuff", 2.0);;
val it : bool = true
```

# 2.14
```fsharp
> id "string" = "string";;
val it : bool = true

> id 1 = 1;;
val it : bool = true

> id 1.0 = 1.0;;
val it : bool = true
```

# 2.15
```fsharp
```
NOTE: Skipping this because it feels like I should be able to intuit the answer
and I'm not sure aside from tinkering with the few fully polymorphic combinators
I know of to get a type `T -> T`...


# 2.16
NOTE: AHA!!! Finally an implementation taking advantage of Sum types to restrict
the space of possible values to only the valid set! Yay!
```fsharp
> type Suit = Hearts | Spades | Diamonds | Clubs;;
type Suit =
  | Hearts
  | Spades
  | Diamonds
  | Clubs

> type Rank = Ace | Two | Three | Four | Five | Six | Seven | Eight | Nine | Jack | Queen | King;;
type Rank =
  | Ace
  | Two
  | Three
  | Four
  | Five
  | Six
  | Seven
  | Eight
  | Nine
  | Jack
  | Queen
  | King

> type Card = { Rank: Rank; Suit: Suit };;
type Card =
  { Rank: Rank
    Suit: Suit }

> let nineOfDiamonds = {Rank=Nine; Suit=Diamonds};;
val nineOfDiamonds : Card = { Rank = Nine
                              Suit = Diamonds }

> let aceOfSpades = {Rank=Ace; Suit=Spades};;
val aceOfSpades : Card = { Rank = Ace
                           Suit = Spades }
```
ANSWER: It is not possible to construct an invalid Card value.

# 2.17
NOTE: Didn't I already do this in 2.16?
# 2.18
NOTE: What for? Just curious where this is going?

# 2.19
ANSWER: It'll just return false which may or may not align with the domain you're trying to model.

# 2.20
```fsharp
> type Shape = Circle float | Square float | Rectangle {Height: float; Width: float} | Triangle {Base: float; A: float; B: float};;-

  type Shape = Circle float | Square float | Rectangle {Height: float; Width: float} | Triangle {Base: float; A: float; B: float};;
  --------------------------^

~/code/fpm_book/stdin(7,27): error FS0010: Unexpected symbol '|' in member definition

> type Shape = | Circle of float | Square of float | Rectangle of {Height: float; Width: float} | Triangle of {Base: float; A: floa
- t; B: float};;
  type Shape = | Circle of float | Square of float | Rectangle of {Height: float; Width: float} | Triangle of {Base: float; A: float; B: float};;
  ----------------------------------------------------------------^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

~/code/fpm_book/stdin(8,65): error FS0035: This construct is deprecated: Consider using a separate record type instead

> type Rectangle = {Height: float; Width: float};;
type Rectangle =
  { Height: float
    Width: float }

> type Triangle = {Base: float; A: float; B: float};;
type Triangle =
  { Base: float
    A: float
    B: float }

> type Shape = | Circle of float | Square of float | Rectangle of Rectangle | Triangle of Triangle;;
type Shape =
  | Circle of float
  | Square of float
  | Rectangle of Rectangle
  | Triangle of Triangle

> let area: Shape -> float = function
-   | Circle radius -> System.Math.PI * radius ** 2.0
-   | Triangle sides -> System.Math.Sqrt();;

    | Triangle sides -> System.Math.Sqrt();;
  --------------------------------------^^

~/code/fpm_book/stdin(14,39): error FS0001: This expression was expected to have type
    'float'
but here has type
    'unit'

> let s a b c = (a + b + c) / 2;;
val s : a:int -> b:int -> c:int -> int

> let s (a:float) (b:float) (c:float) = (a + b + c) / 2;;

  let s (a:float) (b:float) (c:float) = (a + b + c) / 2;;
  ----------------------------------------------------^
  let area: Shape -> float = function
-   | Circle radius -> System.Math.PI * radius ** 2.0
-   | Triangle sides ->
-     let s' = s sides.Base sides.A sides.B
-     System.Math.Sqrt(s' * (s' - sides.Base) * (s' - sides.A) * (s' - sides.B))
-   | Square sideLength -> sideLength ** 2
-   | Rectangle { Height=h; Width=w } -> h * w;;

    | Square sideLength -> sideLength ** 2
  ---------------------------------------^

~/code/fpm_book/stdin(35,40): error FS0001: This expression was expected to have type
    'float'
but here has type
    'int'

> let area: Shape -> float = function
-   | Circle radius -> System.Math.PI * radius ** 2.0
-   | Triangle sides ->
-     let s' = s sides.Base sides.A sides.B
-     System.Math.Sqrt(s' * (s' - sides.Base) * (s' - sides.A) * (s' - sides.B))
-   | Square sideLength -> sideLength ** 2.0
-   | Rectangle { Height=h; Width=w } -> h * w;;
val area : _arg1:Shape -> float

> area (Circle 2.1);;
val it : float = 13.8544236

> area (Triangle {Base=0.1,A=0.2,B=0.3});;

  area (Triangle {Base=0.1,A=0.2,B=0.3});;
  ---------------------^^^^^^^^^^^^^^^

~/code/fpm_book/stdin(45,22): error FS0001: This expression was expected to have type
    'float'
but here has type
    ''a * 'b * 'c'
A ';' is used to separate field values in records. Consider replacing ',' with ';'.

> area (Triangle {Base=0.1;A=0.2;B=0.3});;
val it : float = 5.771194914e-10

> area (Rectangle {Height=0.1;Width=0.2});;
val it : float = 0.02

> area (Square 0.1);;
val it : float = 0.01
```

# 2.21
```fsharp
> let area: Shape -> float = function
-   | Circle radius -> System.Math.PI * radius ** 2.0
-   | Triangle sides ->
-     let s' = s sides.Base sides.A sides.B
-     System.Math.Sqrt(s' * (s' - sides.Base) * (s' - sides.A) * (s' - sides.B))
-   | Square sideLength -> sideLength ** 2.0
-   | Rectangle { Height=h; Width=w } -> h * w
-   | Polygon { Sides=s; Length=l } ->
-     let num = (float s ** 2.) * l
-     let den = tan 4. * (180. / l)
-     num / den;;
-
val area : _arg1:Shape -> float

> area (Polygon {Sides=2; Length=1.0});;
val it : float = 0.01919313677


> let perimeter: Shape -> float = function
-   | Circle rad -> 2. * Math.PI * rad
-   | Triangle {Base=c; A=a; B=b} -> a+b+c
-   | Square len -> 4. * len
-   | Rectangle { Height=h; Width=w } -> 2. * (h + w)
-   | Polygon { Sides=s; Length=l } -> float s * l;;
val perimeter : _arg1:Shape -> float

> perimeter Polygon {Sides=5; Length=2.};;

  perimeter Polygon {Sides=5; Length=2.};;
  ^^^^^^^^^^^^^^^^^

~/code/fpm_book/stdin(187,1): error FS0003: This value is not a function and cannot be applied.

> perimeter (Polygon {Sides=5; Length=2.});;
val it : float = 10.0
```
Not sure I've got the right numbers...
But also without a primitive "Natural" number type, Polygon is screaming for an
Opaque Type to ensure `> 2` sides.


# 2.22
```fsharp
> type Rank = Ace=1 | Two=2 | Three=3 | Four=4 | Five=5 | Six=6 | Seven=7 | Eight=8 | Nine=9 | Jack=10 | Queen=11 | King=12;;
type Rank =
  | Ace = 1
  | Two = 2
  | Three = 3
  | Four = 4
  | Five = 5
  | Six = 6
  | Seven = 7
  | Eight = 8
  | Nine = 9
  | Jack = 10
  | Queen = 11
  | King = 12

> int King;;

  int King;;
  ----^^^^

~/code/fpm_book/stdin(2,5): error FS0039: The value or constructor 'King' is not defined. Maybe you want one of the following:
   int

> int Rank.King;;
val it : int = 12

> let higherRank (a: Rank) (b: Rank) : bool = int a > int b;;
val higherRank : a:Rank -> b:Rank -> bool

> higherRank Rank.King Rank.Ace;;
val it : bool = true

> higherRank King Ace;;

  higherRank King Ace;;
  -----------^^^^

~/code/fpm_book/stdin(6,12): error FS0039: The value or constructor 'King' is not defined. Maybe you want one of the following:
   int
```

# 2.23
```fsharp
> let add : Maybe<int> -> Maybe<int> -> Maybe<int> = fun x y ->
-   match (x, y) with
-   | (Success a, Success b) -> Success (a + b)
-   | _ -> Error;;
val add : x:Maybe<int> -> y:Maybe<int> -> Maybe<int>

> add (Success 1) (Success 2);;
val it : Maybe<int> = Success 3

> add (Success 1) (Error);;
val it : Maybe<int> = Error

> add (Success 1) (Error);;

> let subtract : Maybe<int> -> Maybe<int> -> Maybe<int> = fun x y ->
-   match (x, y) with
-   | (Success a, Success b) -> Success (a - b)
-   | _ -> Error;;
val subtract : x:Maybe<int> -> y:Maybe<int> -> Maybe<int>

> subtract (Success 1) (Error);;
val it : Maybe<int> = Error

> subtract (Success 1) (Success 2);;
val it : Maybe<int> = Success -1

> let multiply : Maybe<int> -> Maybe<int> -> Maybe<int> = fun x y ->
-   match (x, y) with
-   | (Success a, Success b) -> Success (a * b)
-   | _ -> Error;;
val multiply : x:Maybe<int> -> y:Maybe<int> -> Maybe<int>

> multiply (Success 1) (Success 2);;
val it : Maybe<int> = Success 2

> multiply (Success 1) Error;;
val it : Maybe<int> = Error
> let add: option<int> -> option<int> -> option<int> = Option.map2 (+);;
val add : (int option -> int option -> int option)

> let subtract: option<int> -> option<int> -> option<int> = Option.map2 (-);;
val subtract : (int option -> int option -> int option)

> let multiply: option<int> -> option<int> -> option<int> = Option.map2 (*);;
val multiply : (int option -> int option -> int option)

> add (Some 1) None;;
val it : int option = None

> add (Some 1) (Some 2);;
val it : int option = Some 3

> multiply (Some 1) (Some 2);;
val it : int option = Some 2

> multiply (Some 1) None;;
val it : int option = None

> subtract (Some 1) (Some 2);;
val it : int option = Some -1

> subtract (Some 1) None;;
val it : int option = None
```

# 2.24
```fsharp
> let describe ({Name=n; Value=v}: Named<Shape>): string = n + " has area of " + string (area v) + " and a perimeter of " + string (- perimeter v);;
val describe : Named<Shape> -> string

> let c = {Name= "Big Circle"; Value=Circle 100.};;
val c : Named<Shape> = { Name = "Big Circle"
                         Value = Circle 100.0 }

> describe c;;
val it : string =
  "Big Circle has area of 31415.926535897932 and a perimeter of 628.3185307179587"


> let t = {Name="Tiny Triangle"; Value=Triangle{A=0.3;B=0.4;C=0.5}};;
val t : Named<Shape> = { Name = "Tiny Triangle"
                         Value = Triangle { A = 0.3
                                            B = 0.4
                                            C = 0.5 } }

> describe t;;
val it : string =
  "Tiny Triangle has area of 0.059999999999999984 and a perimeter of 1.2"
```

# 2.25
```fsharp
> type Quintuple<'T> = 'T * 'T * 'T * 'T * 'T;;
type Quintuple<'T> = 'T * 'T * 'T * 'T * 'T

> let reorder (a,b,c,d,e) = (c,e,a,b,d);;
val reorder : a:'a * b:'b * c:'c * d:'d * e:'e -> 'c * 'e * 'a * 'b * 'd

> let reorder ((a,b,c,d,e): Quintuple<'T>): Quintuple<'T> = (c,e,a,b,d);;
val reorder : 'T * 'T * 'T * 'T * 'T -> Quintuple<'T>
```

# 2.26
```fsharp
> type Rank = Ace | Pip of int | Jack | Queen | King;;
type Rank =
  | Ace
  | Pip of int
  | Jack
  | Queen
  | King

> let cards: List<Rank> = [Ace; Pip 1; Pip 2; Pip 3; Pip 4; Pip 5; Pip 6; Pip 7; Pip 8; Pip 9; Jack; Queen; King];;
val cards : List<Rank> =
  [Ace; Pip 1; Pip 2; Pip 3; Pip 4; Pip 5; Pip 6; Pip 7; Pip 8; Pip 9; Jack;
   Queen; King]
```

# 2.27
```fsharp
> type Choice<'a,'b,'c> = | A of 'a | B of 'b | C of 'c;;
type Choice<'a,'b,'c> =
  | A of 'a
  | B of 'b
  | C of 'c

> let choices: List<Choice<int,bool,string>> = [A 1;B true; C "things"];;
val choices : List<Choice<int,bool,string>> = [A 1; B true; C "things"]
```

# 2.28
I get the point see above.

# 2.29
ANSWER: No they belong to different specializations of the Choice type parameter EG: int != float.

# 2.30
```fsharp
> type Choice<'T, 'U> = One of 'T | Two of 'U;;
type Choice<'T,'U> =
  | One of 'T
  | Two of 'U

> let swapChoice: Choice<'T, 'U> -> Choice<'U, 'T> = function
-   | One a -> Two a
-   | Two a -> One a;;
val swapChoice : _arg1:Choice<'T,'U> -> Choice<'U,'T>

> swapChoice (One 1);;

  swapChoice (One 1);;
  ^^^^^^^^^^^^^^^^^^

~/code/fpm_book/stdin(174,1): error FS0030: Value restriction. The value 'it' has been inferred to have generic type
    val it : Choice<'_a,int>
Either define 'it' as a simple data term, make it a function with explicit arguments or, if you do not intend for it to be generic, add a type annotation.

> swapChoice (One 1: Choice<int, string>);;
val it : Choice<string,int> = Two 1
```

# 2.31
ANSWER: `Choice<'B,'C,'A>`, `Choice<'C,'B,'A>`, `Choice<'B,'A,'C>`, `Choice<'A,'C,'B>`, etc...
3 ** 2 isomorphic types for any type with 3 polymorphic type variables.
Which leads to the intuition that for any sum type the number of isomorphisms is 
the square of the number of type parameters?

# 2.32
ANSWER:
- 6 values in a `Two * Three` product (hence the `*` syntax :laughing:),
- 5 values in a `Choice<Two, Three>` sum.

# 2.33

# 2.34
This function should be called `bimap` because pair is a BiFunctor and the `map` function
should only run over the left side.

# 2.35
Product type isomorphic to `Choice<'A,'A>` as long as the type parameters are the same
then you can use a tuple of `bool * 'A` to represent each case.
Of if you want to be kitchy `Choice<unit, unit> * 'A`
