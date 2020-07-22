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
