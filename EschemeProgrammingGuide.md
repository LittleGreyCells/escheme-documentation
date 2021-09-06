escheme Programming Guide
=========================

## Primitive Types

Escheme supports the following primitive types:
* characters -- the ascii characer set
* booleans -- #t, #f
* fixnums -- 64-bit signed numbers
* flonums -- double pricision floating point
* strings -- ascii strings
* symbols -- with value cell and property list
* lists -- the principal structured type
* vectors -- both sexpr and byte vectors
* environments -- both frame-based and associative
* primitive functions -- system defined
* closures -- user constructed with lambda
* continuations -- execution contexts
* ports -- including string ports
* promises -- delayed evaluation
* code objects -- compiled objects
* dictionaries -- Python-like dicts

## Literals
A simple EBNF that describes lexical items:
```
   <alpha> := manifest
   <punct> := manifest
   <binary-digit> := 0|1
   <quarnary-digit> := 0..3
   <octal-digit> := 0..7
   <decimal-digit> := 0..9
   <hex-digit> := 0..9 | a..f | A..F
   <char> := #\<alpha> | #\space | #\newline | #\tab
   <string> := "<char>..."
   <number> := [(-|+)]{<decimal-digit>}+[.{<decimal-digit>}*][[(-|+)](e|E)]{<decimal-digit}*
   <number> := #\b{<binary-digit>}+ | #\B{<binary-digit>}+
   <number> := #\q{<quarnary-digit>}+ | #\Q{<quarnary-digit>}+
   <number> := #\o{<octal-digit>}+ | #\O{<octal-digit>}+
   <number> := #\d{<decimal-digit>}+ | #\D{<decimal-digit>}+
   <number> := #\x{<hex-digit>}+ | #\X{<hex-digit>}+
   <boolean> := #\t | #\f | #!true | #!false
   <vector> := #({<sexpr>}*)
   <list> := ([<sexpr>...]) | #!null
   <symbol> := any non-number string of chars delimited by ' ', '(', ')', '[', ']', ';'
    
```

## Special Forms

```
   <sexpr> := any symbolic expression
   <formal> := <symbol>
   <rest> := <symbol>
   
   (quote <sexpr>)
   (define <symbol> <sexpr>)
   (define (<symbol> {<formal>}*) {<sexpr>}*)
   <== resume here <================================================
   (lambda (<formal> . <rest>]) [<sexpr>...])
   (set! <symbol> <sexpr>)
   (set! (access <symbol> <env-sexpr>) <sexpr>)
   (if <condition-sexpr> <then-sexpr> [<else-sexpr>])
   (cond [(<sexpr>...) ...] [else <sexpr>...])
   (case <sexpr> [((<sexpr> ...) <sexpr>) ...] [(else <sexpr>...)])
   (and [<sexpr> ...])
   (or [<sexpr> ...])
   (let [(<symbol> | (<symbol> <expr>)) ...] [<sexpr>...])
   (let* [(<symbol> | (<symbol> <expr>)) ...] [<sexpr>...])
   (letrec [(<symbol> | (<symbol> <expr>)) ...] [<sexpr>...])
   (begin [<sexpr> ...])	
   (do ((<symbol> <init-sexpr> <step-sexpr>) ...) (<test-sexpr> <sexpr> ...) <sexpr> ...)
   (delay <sexpr>)
   (access <symbol> <env-sexpr>)
   (while [<sexpr> ...])
   (quasiquote <template-sexpr>)
   (unquote <sexpr>)
   (unquotesplicing <sexpr>)

```

## Functions

### System Functions
```
   (exit)
   (gc) -> <vector>
   (mm) -> <vector>
   (fs) -> <vector>
   (%object-address <object>) -> <fixnum>
```

### List Functions
```
   (car <list>) -> <sexpr>
   (cdr <list>) -> <list>
   (cxxr)
   (cxxxr)
   (cxxxxr)
   (cons <sexpr1> <sexpr2>) -> (<sexpr1> . <sexpr2>)
   (list [<sexpr1> <sexpr2> ...]) -> (<sexpr1> <sexpr2> ...)
   (list* [<sexpr1> <sexpr2> ... <sexprN>]) -> (<sexpr1> <sexpr2> ... . <sexprN>)
   (length <list>) -> <fixnum>
   (set-car! (<sexpr1> . <sexpr2>) <newcar>) -> (<newcar> . <sexpr2>)
   (set-cdr! (<sexpr1> . <sexpr2>) <newcdr>) -> (<sexpr1> . <newcdr>)
   (append [<list> ...]) -> <list>
   (reverse <list>) -> <list>
   (last-pair <list>) -> (<pair> | nil)
   (list-tail <list> <n>) -> (<list> | nil))
```

### Vector Functions
```
   (vector {<sexp>}*) -> <vector>)
   (make-vector)
   (vector-ref)
   (vector-set!)
   (vector-length)
   (vector-fill!)
   (vector-copy!)
```

### Byte Vector Functions
```
   (byte-vector)
   (make-byte-vector)
   (byte-vector-ref)
   (byte-vector-set!)
   (byte-vector-length)
```

### Symbol Functions
```
   (gensym)
   (%symbol-value)
   (%set-symbol-value!)
   (symbol-plist)
   (set-symbol-plist!)
   (get)
   (put)
   (remprop)
   (bound?)
   (all-symbols)
```

### Equality Functions
```
   (eq?)
   (eqv?)
   (equal?)
```

### I/O Functions
```
   (read)
   (print)
   (write)
   (display)
   (newline)
   (read-char)
   (write-char)
   (open-input-file)
   (open-output-file)
   (open-append-file)
   (open-update-file)
   (get-file-position)
   (set-file-position)
   (close-port)
   (close-input-port)
   (close-output-port)
   (flush-output)
   (open-input-string)
   (open-output-string)
   (get-output-string)
```

### Math Functions
```
   (+)
   (-)
   (*)
   (/)
   (=)
   (<)
   (<=)
   (>)
   (>=)
   (truncate)
   (floor)
   (ceiling)
   (round)
   (1+)
   (1-)
   (-1+)
   (inc)
   (dec)
   (abs)
   (gcd)
   (random)
   (quotient)
   (remainder)
   (min)
   (max)
   (logand)
   (logior)
   (logxor)
   (lognot)
   (shift-right)
   (shift-left)
   (shift-right-arithmetic)
```

### Environment Functions
```
   (the-environment)
   (procedure-environment)
   (environment-bindings)
   (environment-parent)
   (%make-environment)
   (the-global-environment)
```

### Associative Environment Functions
```
   (%make-assoc-env)
   (%assoc-env-has?)
   (%assoc-env-ref)
   (%assoc-env-set!)
```

### Byte Code Functions
```
   (%make-code)
   (%get-bcodes)
   (%get-sexprs)
   (assemble)
   (disassemble)
```

### Logical Functions
```
   (not)
```

### Predicate Functions
```
   (null?)
   (atom?)
   (list?)
   (number?)
   (boolean?)
   (pair?)
   (symbol?)
   (real?)
   (integer?)
   (char?)
   (string?)
   (vector?)
   (byte-vector?)
   (closure?)
   (procedure?)
   (environment?)
   (continuation?)
   (port?)
   (input-port?)
   (output-port?)
   (string-port?)
   (input-string-port?)
   (output-string-port?)
   (eof-object?)
   (zero?)
   (positive?)
   (negative?)
   (odd?)
   (even?)
   (exact?)
   (inexact?)
   (promise?)
   (code?)
   (string-null?)
   (dict?)
   (assoc-env?)
```

### String Functions
```
   (make-string)
   (string-length)
   (string-append)
   (string-ref)
   (string-set!)
   (string-fill!)
   (string-copy!)
   (substring)
   (string-find)
   (string-dup)
   (string-trim)
   (string-trim-left)
   (string-trim-right)
   (string-downcase!)
   (string-upcase!)
   (string-pad-left)
   (string-pad-right)
   (string=?)
   (string<?)
   (string<=?)
   (string>?)
   (string>=?)
   (string-ci=?)
   (string-ci<?)
   (string-ci<=?)
   (string-ci>?)
   (string-ci>=?)

```

### Character Functions
```
   (char=?)
   (char<?)
   (char<=?)
   (char>?)
   (char>=?)
   (char-ci=?)
   (char-ci<?)
   (char-ci<=?)
   (char-ci>?)
   (char-ci>=?)
   (char-alphabetic?)
   (char-numeric?)
   (char-whitespace?)
   (char-upper-case?)
   (char-lower-case?)
   (char-upcase)
   (char-downcase)
```

### Member Functions
```
   (member)
   (memv)
   (memq)
   (assoc)
   (assv)
   (assq)
```

### Closure Functions
```
   (%closure-code)
   (%closure-benv)
   (%closure-vars)
   (%closure-numv)
   (%closure-rest)
   (%closure-code-set!)
```

### Dictionary Functions
```
   (make-dict)
   (has-key?)
   (dict-ref)
   (dict-set!)
   (dict-items)
   (dict-rem!)
   (dict-empty!)
```

### Conversion Functions
```
   (list->vector)
   (vector->list)
   (list->string)
   (string->list)
   (symbol->string)
   (string->symbol)
   (integer->char)
   (char->integer)
   (integer->string)
   (string->integer)
```

### Transcript Functions
```
   (transcript-on)
   (transcript-off)
```

### History Functions
```
   (add-history)
   (show-history)
   (clear-history)
   (set-prompt)
```

### Linux/Unix Functions
```
   (system)
   (getargs)
   (getenv)
   (setenv)
   (unsetenv)
   (gettime)
   (chdir)
   (getcwd)
   (getenv)
```

### Socket Functions
```
   (socket-read)
   (socket-write)
   (socket-recvfrom)
   (socket-recv)
   (socket-sendto)
   (socket-create-tcp)
   (socket-create-udp)
   (socket-bind)
   (socket-bind-address)
   (socket-create-address)
   (socket-listen)
   (socket-accept)
   (socket-connect)
   (socket-disconnect)
   (socket-close)
   (read-select)
```

