# purescript-verbal-expressions

A free monad implementation of [Verbal Expressions](https://github.com/VerbalExpressions/JSVerbalExpressions) for [PureScript](https://github.com/purescript/purescript).

## Examples

### Using do-notation to construct verbal expressions
``` purs
number :: VerEx
number = do
  startOfLine
  possibly (anyOf "+-")
  some digit
  possibly do
    find "."
    some digit
  endOfLine

> test number "42"
true

> test number "+3.14"
true

> test number "3."
false
```

### Capture groups and back references
The monadic interface allows us to bind the indices of capture groups to named expressions:
``` purs
pattern :: VerEx
pattern = do
  firstWord <- capture word
  whitespace
  capture word
  whitespace
  findAgain firstWord
```
This VerEx matches "foo bar *foo*" but not "foo bar *baz*".

### Matching
``` purs
number = match do
  startOfLine
  intPart <- capture (some digit)
  floatPart <- possibly do
    find "."
    capture (some digit)
  endOfLine
  return [intPart, floatPart]

> match "3.14"
Just [Just "3", Just "14"]

> match "42"
Just [Just "42", Nothing]

> test number "."
false
```

### Replacing with named groups
Here, we use the result of the monadic action to return a replacement string with 'named' capture groups:
``` purs
swapWords :: String -> String
swapWords = replace do
  first  <- capture word
  blank  <- capture (some whitespace)
  second <- capture word

  replaceWith (insert second <> insert blank <> insert first)

> swapWords "Foo   Bar"
"Bar   Foo"
```
Note that `replaceWith` is just an alias for `return`.

For more examples, see the [tests](test/Main.purs).

## Installation
```
bower install purescript-verbal-expressions
```
