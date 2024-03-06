# OCaml Main Project
Write an OCaml program that performs pattern matching on strings, where patterns are expressed using only the concatenation, alternation (“|”), and optional (“?”) operators of regular expressions (no loops/”*”, no escape characters), and the tokens are letters and digits, plus period (“.”) to mean any letter. Each run of the program should accept a pattern, and then any number of strings, reporting only whether they match. Your program should represent expressions as trees and evaluate on the inputs, without using any regular expressions or OCaml’s regular expression libraries except for matching the individual alphanumeric characters, if you’d like. For example:

```
pattern? ((h|j)ell. worl?d)|(42)
string? hello world
match
string? jello word
match
string? jelly word
match
string? 42
match
string? 24
no match
string? hello world42
no match
```
```
pattern? I (like|love|hate)( (cat|dog))? people
string? I like cat people
match
string? I love dog people
match
string? I hate people
match
string? I likelovehate people
no match
string? I people
no match
```
## Bootstrap
An ambiguous grammar for patterns is:
```ocaml
S -: E$  
E -: C | EE | E'|'E | E'?' | '(' E ')'
C -: '0' | '1' | ... | '9'| 'a' | 'b' | ... | 'z' | '.'
```
To reflect that option(‘?’) has highest precedence, then concatenation, then alternation(‘|’), the following unambiguous grammar can be created:

```ocaml
 S  -: E$
 E  -: T '|' E | T
 T  -: F T | F
 F  -: A '?' | A
 A  -: C | '(' E ')'
 C  -: Alphanumeric characters plus '.'
```
As with the microproject, you will build an AST. This means that instead of building a node for E in your tree, which represents alternation (or perhaps the absence of alternation), you will build an Alternation node only if necessary. I recommend using the following types to get started:
```ocaml

(* Scanner Types *)
type token = Tok_Char of char 
  | Tok_OR
  | Tok_Q
  | Tok_LPAREN
  | Tok_RPAREN
  | Tok_END
 
(* AST Types *)
type re = C of char
    | Concat of re * re
    | Optional of re
    | Alternation of re * re
```
You must implement a recursive descent parser yourself to build the AST from the input string. Remember not to use any regular expression processing other than for the individual characters (either built in or in external libraries)!

## Hints

This project is broken up roughly into three parts – the scanner, the parser, and the matcher. The parser will require you to write the most code, but the type checker should help immensely. The most difficult part of the project is probably the matcher, which requires more thinking!

Some sample parser output to help you (but remember, precedence is handled by the grammar, so don’t think too hard about that part!):

```ocaml

parse "ab";;
- : re = Concat (C 'a', C 'b')
parse "a|b";;
- : re = Alternation (C 'a', C 'b')
parse "ab?";;
- : re = Concat (C 'a', Optional (C 'b'))
parse "(ab)?";;
- : re = Optional (Concat (C 'a', C 'b'))
parse "ab|cd?";;
- : re = Alternation (Concat (C 'a', C 'b'), Concat (C 'c', Optional (C 'd')))
```
## Suggested Development Environment
VS Code + OCaml Platform Extension

