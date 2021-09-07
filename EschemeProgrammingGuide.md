escheme Programming Guide
=========================

## Primitive Types

Escheme supports the following primitive types:
* characters -- the ascii characer set
* booleans -- #t, #f
* fixnums -- 64-bit signed numbers
* flonums -- double precision floating point
* strings -- ascii strings
* symbols -- with value cell and property list
* lists -- the principal structured type
* vectors -- both sexpr and byte vectors
* environments -- both frame-based and associative
* primitive functions -- system defined
* closures -- user constructed with lambda
* continuations -- execution contexts
* ports -- in/out/inout including string ports
* promises -- delayed evaluation
* code objects -- compiled objects
* dictionaries -- python-like dicts where any object can be a key

## Meta-grammar
A simple EBNF that describes lexical and syntacitcal items:
```
   <lhs> := <rhs> -- <rhs> can replace <lhs>
   {<item>}* -- zero or more <item>
   {<item>}+ -- one or more <items>
   [<item>] -- optional <item>
   (<item1> | <item2>) -- <item1> or <item2>
   <item1> .. <itemN> -- one of sequence described by [<item1>,<itemN>]
```

## Literals
```
   <sexpr> := any symbolic expression
   <alpha> := ascii alpha characters
   <punct> := ascii punctuation characters
   <binary-digit> := 0|1
   <quarnary-digit> := 0..3
   <octal-digit> := 0..7
   <decimal-digit> := 0..9
   <hex-digit> := 0..9 | a..f | A..F
   <char> := #\<alpha> | #\space | #\newline | #\tab
   <string> := "{<char>}*"
   <number> := [(-|+)]{<decimal-digit>}+[.{<decimal-digit>}*][[(-|+)](e|E)]{<decimal-digit}*
   <number> := #\b{<binary-digit>}+ | #\B{<binary-digit>}+
   <number> := #\q{<quarnary-digit>}+ | #\Q{<quarnary-digit>}+
   <number> := #\o{<octal-digit>}+ | #\O{<octal-digit>}+
   <number> := #\d{<decimal-digit>}+ | #\D{<decimal-digit>}+
   <number> := #\x{<hex-digit>}+ | #\X{<hex-digit>}+
   <boolean> := #\t | #\f | #!true | #!false
   <vector> := #({<sexpr>}*)
   <list> := ({<sexpr>}*) | #!null
   <symbol> := any non-number string of chars delimited by ' ', '(', ')', '[', ']', ';'
    
```

## Special Forms

### No or delayed evaluation
```
   (quote <sexpr>)
   (delay <sexpr>)
```

### Symbol definition
```   
   <formal> := <symbol>
   <rest> := <symbol>
   
   (define <symbol> <sexpr>)
   
   (define (<symbol> {<formal>}*)
       {<sexpr>}*)
       
   (define (<symbol> {<formal>}+ . <rest>)
       {<sexpr>}*)
```

### Closure construction
```   
   (lambda <formal>
       {<sexpr>}*)
       
   (lambda ({<formal>}*)
       {<sexpr>}*)
       
   (lambda ({<formal>}+ . <rest>)
       {<sexpr>}*)
```

### Symbol accessing and setting
```   
   (access <symbol> <env-sexpr>)
   (set! <symbol> <sexpr>)
   (set! (access <symbol> <env-sexpr>) <sexpr>)
```

### Conditional evaluation
```   
   (if <condition-sexpr>
       <then-sexpr>
       [<else-sexpr>])
       
   (cond
       {({<sexpr>}*)}* [else
       {<sexpr>}*])
	
   (case <sexpr>
       {(({<sexpr>}+) <sexpr>)}*
       [else {<sexpr>}+])
```

### Short curcuit boolean evaluation
```   
   (and {<sexpr>}*)
   (or {<sexpr>}*)
```
### Frame-based environement creation
```   
   (let {(<symbol> | (<symbol> <expr>))}+
       {<sexpr>}*)
       
   (let* {(<symbol> | (<symbol> <expr>))}+
       {<sexpr>}*)
       
   (letrec {(<symbol> | (<symbol> <expr>))}+
       {<sexpr>}*)
```

### Sequence evaluation
```   
   (sequence {<sexpr>}*)	
   (begin {<sexpr>}*)
```

### Looping
```
   (do ({(<symbol> <init-sexpr> <step-sexpr>)}+)
       (<test-sexpr> {<sexpr>}+)
       {<sexpr>}+)
       
   (while <condition-sexpr>
       {<sexpr>}*)
```

### Template expansion
```
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
   (car <list>) -> <head>
   (cdr <list>) -> <tail>
   (cxyr <sexpr>) -> (cxr (cyr <sexpr>)) where xy permute {a,d}
   (cxyzr <sexpr>) -> (cxr (cyr (czr <sexpr>))) where xyz permute {a,d}
   (cxyzwr <sexpr>) ->  (cxr (cyr (czr (cwr <sexpr>)))) where xyzw permute {a,d}
   (cons <sexpr1> <sexpr2>) -> (<sexpr1> . <sexpr2>)
   (list {<sexpr>}*) -> <list>
   (list* {<sexpr>}*) -> <list>
   (length <list>) -> <fixnum>
   (set-car! <list> <newcar-sexpr>) -> <list>
   (set-cdr! <list> <newcdr-sexpr>) -> <list>
   (append {<list>}*) -> <list>
   (reverse <list>) -> <list>
   (last-pair <list>) -> (<pair> | nil)
   (list-tail <list> <n>) -> (<list> | nil))
```

### Vector Functions
```
   (vector {<sexp>}*) -> <vector>
   (make-vector <size-fixnum>) -> <vector>
   (vector-ref <vector> <index-fixnum>) -> <sexpr>
   (vector-set! <vector> <index-fixnum> <sexpr>) -> <sexpr>
   (vector-length <vector>) -> <fixnum>
   (vector-fill! <vector> <sexpr>) -> <vector>
   (vector-copy! <dest-vector> <dest-start> <src-vector> [<src-start> <src-end>]) -> <dest-vector>
```

### Byte Vector Functions
```
   (byte-vector {<fixnum>}*) -> <byte-vector>
   (make-byte-vector <size-fixnum>) -> <byte-vector>
   (byte-vector-ref <byte-vector> <index-fixnum>) -> <fixnum>
   (byte-vector-set! <byte-vector> <index-fixnum> <value-fixnum>) -> <value-fixnum>
   (byte-vector-length <byte-vector>) -> <size-fixnum>
```

### Symbol Functions
```
   <plist> := ({<propery-sexpr> <property-value-sexpr>}*)
   <property> := <sexpr>
   <property-value> := <sexpr>
   
   (gensym (<symbol>|<string>|<fixnum>)) -> <symbol>
   (%symbol-value <symbol>) -> <value-sexpr>
   (%set-symbol-value! <symbol> <value-sexpr>) -> <value-sexpr>
   (symbol-plist <symbol>) -> <plist>
   (set-symbol-plist! <symbol> <plist>) -> <plist>
   (get <symbol> <property>) -> <property-value>
   (put <symbol> <property> <property-value>) -> <property>
   (remprop <symbol> <property>) -> <property-value>
   (bound? <symbol>) -> <boolean>
   (all-symbols) -> <symbol-list>
```

### Application Control Functions
```
   <fn> := any callable
   
   (apply <fn> {<sexpr>}+) -> <fn-result-sexpr>
   (call/cc <fn>) -> <fn-result-sexpr>
   (eval <sexp> (<env-expr>)) -> <evaluated-sexpr>
   (map <fn> {<list>}+) -> <list-of-fn-results>
   (for-each <fn> {<list>}+) -> nil
   (force <promise>) -> <evaluated-delay-sexpr>
```

### I/O Functions
```
   (read [<port>]) -> <sexpr>
   (print <sexpr> [<port>]) -> #t
   (write <sexpr> [<port>]) -> #t
   (display <sexpr> [<port>]) -> #t
   (newline [<port>]) -> #t
   (read-char [<port>]) -> <char>
   (write-char <char> [<port>]) -> #t
   (open-input-file <file-path-string>) -> <port>
   (open-output-file <file-path-string>) -> <port>
   (open-append-file <file-path-string>) -> <port>
   (open-update-file <file-path-string>) -> <port>
   (get-file-position <port>) -> <pos-fixnum>
   (set-file-position <port> <pos-fixnum>) -> #t
   (close-port <port>) -> #t
   (close-input-port <port>) -> #t
   (close-output-port <port>) -> #t
   (flush-output <port>) -> #t
   (open-input-string <string>) -> <string-port>
   (open-output-string) -> <string-port>
   (get-output-string <string-port>) -> <string>
```

### Math Functions
#### Arithmetic
```
   <number> := <fixnum> | <flonum>
   
   (+ {<number>}*) -> <number>
   (- {<number>}+) -> <number>
   (* {<number>}*) -> <number>
   (/ {<number>}*) -> <number>
```
#### Logical
```
   (not <sexpr>) -> <boolean>
   (=  {<number>}+) -> <boolean)
   (<  {<number>}+) -> <boolean)
   (<= {<number>}+) -> <boolean)
   (>  {<number>}+) -> <boolean)
   (>= {<number>}+) -> <boolean)
```
#### Miscellaneous
```
   (truncate <number>) -> <fixnum>
   (floor <number>) -> <fixnum>
   (ceiling <number>) -> <fixnum>
   (round <number>) -> <fixnum>
   (1+ <number>) -> <number>
   (1- <number>) -> <number>
   (-1+ <number>) -> <number>
   (inc <number>) -> <number>
   (dec <number>) -> <number>
   (abs <number>) -> <number>
   (gcd {<fixnum>}*) -> <fixnum>
   (random <fixnum>) -> <fixnum>
   (quotient {<fixnum>}+) -> <fixnum>
   (remainder {<fixnum>}+) -> <fixnum>
   (min {<fixnum>}+) -> <fixnum>
   (max {<fixnum>}+) -> <fixnum>
   (logand {<fixnum>}+) -> <fixnum>
   (logior {<fixnum>}+) -> <fixnum>
   (logxor {<fixnum>}+) -> <fixnum>
   (lognot <fixnum>) -> <fixnum>
   (shift-right <fixnum> <count-fixnum>) -> <fixnum>
   (shift-left <fixnum> <count-fixnum>) -> <fixnum>
   (shift-right-arithmetic <fixnum> <count-fixnum>) -> <fixnum>
```

### Environment Functions
```
   <binding> := (<symbol> . <value-sexpr>)
   <bindings> := ({<binding>}*)
   
   (the-environment) -> <env>
   (procedure-environment <closure>) -> <env>
   (environment-bindings <env>) -> <bindings>
   (environment-parent <env>) -> <env>
   (%make-environment <bindings> <parent-env>) -> <env>
   (the-global-environment) -> <null-env>
   (%make-assoc-env [<parent-env>]) -> <assoc-env>
   (%assoc-env-has? <assoc-env> <symbol>) -> <boolean>
   (%assoc-env-ref <assoc-env> <symbol>) -> <value-sexpr>
   (%assoc-env-set! <assoc-env> <symbol> <value-sexpr>) -> <value-sexpr>
```

### Byte Code Functions (Compiled)
```
   (%make-code <byte-vector> <sexpr-vector>) -> <code>
   (%get-bcodes <code>) -> <byte-vector>
   (%get-sexprs <code>) -> <sexpr-vector>
   (compile <sexpr> [<env>]) -> <code>
   (disassemble <code>) -> nil
```

### Predicate Functions
```
   (null? <object>) -> <boolean>
   (atom? <object>) -> <boolean>
   (list? <object>) -> <boolean>
   (number? <object>) -> <boolean>
   (boolean? <object>) -> <boolean>
   (pair? <object>) -> <boolean>
   (symbol? <object>) -> <boolean>
   (real? <object>) -> <boolean>
   (integer? <object>) -> <boolean>
   (char? <object>) -> <boolean>
   (string? <object>) -> <boolean>
   (vector? <object>) -> <boolean>
   (byte-vector? <object>) -> <boolean>
   (closure? <object>) -> <boolean>
   (procedure? <object>) -> <boolean>
   (environment? <object>) -> <boolean>
   (continuation? <object>) -> <boolean>
   (port? <object>) -> <boolean>
   (input-port? <object>) -> <boolean>
   (output-port? <object>) -> <boolean>
   (string-port? <object>) -> <boolean>
   (input-string-port? <object>) -> <boolean>
   (output-string-port? <object>) -> <boolean>
   (eof-object? <object>) -> <boolean>
   (zero? <object>) -> <boolean>
   (positive? <object>) -> <boolean>
   (negative? <object>) -> <boolean>
   (odd? <object>) -> <boolean>
   (even? <object>) -> <boolean>
   (exact? <object>) -> <boolean>
   (inexact? <object>) -> <boolean>
   (promise? <object>) -> <boolean>
   (code? <object>) -> <boolean>
   (string-null? <object>) -> <boolean>
   (dict? <object>) -> <boolean>
   (assoc-env? <object>) -> <boolean>
```

### String Functions
```
   (make-string <length> [<char>]) -> <string>
   (string-length <string>) -> <fixnum>
   (string-append {<string>}*) -> <string>
   (string-ref <string> <index>) -> <char>
   (string-set! <string> <index> <char>) -> <char>
   (string-fill! <string> <char>) -> <string>)
   (string-copy! <dest-string> <dest-start> <src-string> [<src-start> <src-end>]) -> <dest-string>
   (substring <string> <start> <end>) -> <string>
   (string-find <string1> <string22> [<start> [<end>]]) -> (<fixnum> | nil)
   (string-dup <string>) -> <string>
   (string-trim <string1> [<string2>]) -> <string>
   (string-trim-left <string1> [<string2>]) -> <string>
   (string-trim-right <string1> [<string2>]) -> <string>
   (string-downcase! <string>) -> <string>
   (string-upcase! <string>) -> <string>
   (string-pad-left <string> <k> [<char>]) -> <string>
   (string-pad-right <string> <k> [<char>]) -> <string>
   (string=? <string1> <string2>) -> <boolean>
   (string<? <string1> <string2>) -> <boolean>
   (string<=? <string1> <string2>) -> <boolean>
   (string>? <string1> <string2>) -> <boolean>
   (string>=? <string1> <string2>) -> <boolean>
   (string-ci=? <string1> <string2>) -> <boolean>
   (string-ci<? <string1> <string2>) -> <boolean>
   (string-ci<=? <string1> <string2>) -> <boolean>
   (string-ci>? <string1> <string2>) -> <boolean>
   (string-ci>=? <string1> <string2>) -> <boolean>

```

### Character Functions
```
   (char=? <char1> <char2>) -> <boolean>
   (char<? <char1> <char2>) -> <boolean>
   (char<=? <char1> <char2>) -> <boolean>
   (char>? <char1> <char2>) -> <boolean>
   (char>=? <char1> <char2>) -> <boolean>
   (char-ci=? <char1> <char2>) -> <boolean>
   (char-ci<? <char1> <char2>) -> <boolean>
   (char-ci<=? <char1> <char2>) -> <boolean>
   (char-ci>? <char1> <char2>) -> <boolean>
   (char-ci>=? <char1> <char2>) -> <boolean>
   (char-alphabetic? <char>) -> <boolean>
   (char-numeric? <char>) -> <boolean>
   (char-whitespace? <char>) -> <boolean>
   (char-upper-case? <char>) -> <boolean>
   (char-lower-case? <char>) -> <boolean>
   (char-upcase <char>) -> <char>
   (char-downcase <char>) -> <char>
```

### Equality Functions
```
   (eq? <sexpr1> <sexpr2>) -> <boolean>
   (eqv? <sexpr1> <sexpr2>) -> <boolean>
   (equal? <sexpr1> <sexpr2>) -> <boolean>
```

### Member/Association List Functions
```
   <pair> := (<key-sexpr> . <value-sexpr)
   <alist> := ({<pair>}*)
   
   (member <sexpr> <list>) -> (<sexpr> | nil)
   (memq <sexpr> <list>) -> (<sexpr> | nil)
   (memv <sexpr> <list>) -> (<sexpr> | nil)
   (assoc <key-sexpr> <alist>) -> (<pair> | nil)
   (assq <key-sexpr> <alist>) -> (<pair> | nil)
   (assv <key-sexpr> <alist>) -> (<pair> | nil)
```

### Closure Functions
```
   (%closure-benv <closure>) -> <env>
   (%closure-numv <closure>) -> <fixnum>
   (%closure-rest <closure>) -> <boolean>
   (%closure-vars <closure>) -> <list>
   (%closure-code <closure>) -> <list>                  ;; interpreted
   (%closure-code <closure>) -> <code>                  ;; compiled
   (%closure-code-set! <closure> <code>) -> <closure>   ;; compiled
```

### Dictionary Functions
```
   (make-dict [<size>]) -> <dict>
   (has-key? <dict> <key-sexpr>) -> <boolean>
   (dict-ref <dict> <key-sexpr>) -> <value-sexpr>
   (dict-set! <dict> <key-sexpr> <value-sexpr>) -> <value-sexpr>
   (dict-items <dict>) -> ({(<key-sexpr> . <value-sexpr>)}*)
   (dict-rem! <dict> <key-sexpr>) -> <key-sexpr>
   (dict-empty! <dict>) -> <dict>
```

### Conversion Functions
```
   (list->vector <list>) -> <vector>
   (vector->list <vector>) -> <list>
   (list->string <list-of-char>) -> <string>
   (string->list <string>) -> <list-of-char>
   (symbol->string <symbol>) -> <string>
   (string->symbol <string>) -> <symbol>
   (integer->char <fixnum>) -> <char>
   (char->integer <char>) -> <fixnum>
   (integer->string <fixnum>) -> <string>
   (string->integer <string>) -> <fixnum>
```

### Transcript Functions
```
   (transcript-on [<file-name-string>]) -> #t
   (transcript-off) -> #t
```

### History Functions
```
   (add-history <sexpr>) -> #t
   (show-history) -> #t
   (clear-history) -> #t
   (set-prompt) -> #t
```

### Linux/Unix Functions
```
   (system <string>) -> <return-code-fixnum>
   (getargs) -> #({<string>}+)
   (getenv <var-string>) -> <val-string>
   (setenv <var-string> <val-string>) -> <return-code-fixnum>
   (unsetenv <var-string>) -> <fixnum>
   (gettime) -> (<seconds-fixnum> . <nanoseconds-fixnum>)
   (chdir <string>) -> <return-code-fixnum>
   (getcwd) -> <string>
   (getenv <var-string>) -> <val-string>
```

### Socket Functions
```
   <sockfd> := <fixnum>
   <numbytes> := <fixnum>
   <from-addr> := <fixnum>
   <to-addr> := <fixnum>
   <server-flag> := <fixnum>
   <backlog> := <fixnum>
   <server-host-addr> := <string>
   <host-addr> := <string>
   <address> := <fixnum>
   <server-port> := <fixnum>
   <numtries> := <fixnum>
   <fd-list> := <list>
   <ready-list> := <list>
   
   (socket-read <sockfd> [<numbytes>]) -> <byte-vector>
   (socket-write <sockfd> <byte-vector>) -> <fixnum>
   (socket-recvfrom <sockfd> <numbytes> <from-addr>) -> <byte-vector>
   (socket-recv <sockfd> <numbytes>) -> <byte-vector>
   (socket-sendto <sockfd> <byte-vector> <to-addr>) -> <fixnum>
   (socket-create-tcp [<server-flag>]) -> <sockfd>
   (socket-create-udp) -> <sockfd>
   (socket-bind <sockfd> <host-addr> <port> ) -> <sockfd>
   (socket-bind-address <sockfd> <address> ) -> <sockfd>
   (socket-create-address <host-addr> <port> ) -> <address>
   (socket-listen <sockfd> [<backlog>]) -> <fixnum>
   (socket-accept <sockfd>) -> <sockfd>
   (socket-connect <sockfd> <server-host-addr> <server-port> [<numtries>=1] ) -> <sockfd>
   (socket-disconnect <sockfd>) -> <fixnum>
   (socket-close <sockfd>) -> <fixnum>
   (read-select <fd-list>) --> <ready-list>
```

