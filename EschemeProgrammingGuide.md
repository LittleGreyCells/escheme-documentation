escheme Programming Guide
========================

## Literals
```
    character := #\<char> | #\space | #\newline | #\tab
    string := "<char>..."
    number := [(-|+)]<digit>...[.][<digit>...][[(-|+)](e|E)]<digit>...
    number := #\b<binary-digit>... | #\B<binary-digit>...
    number := #\q<quarnary-digit>... | #\Q<quarnary-digit>...
    number := #\o<octal-digit>... | #\O<octal-digit>...
    number := #\d<decimal-digit>... | #\D<decimal-digit>...
    number := #\x<hex-digit>... | #\<hex-digit>...
    boolean := #\t | #\f | #!true | #!false
    vector := #([<sexpr>...])
    list := ([<sexpr>...]) | #!null
    symbol := <not-anyting-else>
    
```

## Special Forms

```
    (quote <sexpr>)
    (define <symbol> <sexpr>)
    (define (<symbol> [<formal>...]) [<sexpr>...])
    (lambda [(<formal> ... [ . <rest>]) [<sexpr>...])
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
   (gc)
   (mm)
   (fs)
   (%object-address)
```

### List Functions
```
   (car)
   (cdr)
   (cxxr)
   (cxxxr)
   (cxxxxr)
   (cons)
   (list)
   (list*)
   (length)
   (set-car!)
   (set-cdr!)
   (append)
   (reverse)
   (last-pair)
   (list-tail)
```

### Vector Functions
```
   (vector)
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

