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
* vectors -- both symbolic expression and byte
* environments -- both frame-based and associative
* primitive functions -- system defined
* closures -- user constructed with lambda
* continuations -- execution contexts
* ports -- stream input/output/update including strings
* promises -- delayed evaluation
* code objects -- compiled objects
* dictionaries -- python-like dicts where any object can be a key

## Meta-grammar
A simple EBNF that describes lexical and syntacitcal items:
```
   <lhs> := <rhs> -- <rhs> can replace <lhs>
   {<item>}* -- zero or more <item>s
   {<item>}+ -- one or more <item>s
   [<item>] -- optional <item>
   <item1> | <item2> -- <item1> or <item2>
   <item1> .. <itemN> -- one of sequence described by [<item1>,<itemN>]
```

## Literals
```
   <char> := ascii character set 
   <string> := "{<char>}*"
   <number> := [(-|+)]{<decimal-digit>}+[.{<decimal-digit>}*][[(-|+)](e|E)]{<decimal-digit>}*
   <number> := #\b{<binary-digit>}+ | #\B{<binary-digit>}+
   <number> := #\q{<quarnary-digit>}+ | #\Q{<quarnary-digit>}+
   <number> := #\o{<octal-digit>}+ | #\O{<octal-digit>}+
   <number> := #\d{<decimal-digit>}+ | #\D{<decimal-digit>}+
   <number> := #\x{<hex-digit>}+ | #\X{<hex-digit>}+
   <boolean> := #t | #f
   <symbol> := any non-number string of chars delimited by ' ', '(', ')', '[', ']', ';'
   <special> := #\space | #\newline | #\tab | #t | #f | #!true | #!false | #!null
   <vector> := #({<sexpr>}*)
   <list> := ({<sexpr>}*)

   Where:
     <alpha> := ascii alpha characters
     <punct> := ascii punctuation characters
     <numeric> := ascii numeric characters
     <binary-digit> := 0|1
     <quarnary-digit> := 0..3
     <octal-digit> := 0..7
     <decimal-digit> := 0..9
     <hex-digit> := 0..9 | a..f | A..F
     <sexpr> := any symbolic expression
```

## Special Forms

### Suppress evaluation
```
   (quote <sexpr>)
   (delay <sexpr>) -> <promise>
```
Quote suppresses evaluation of its expression. Delay defers evalution unti the \<promise\> is forced.


### Symbol definition
```   
   (define <symbol> <sexpr>)
   
   (define (<symbol> {<formal>}*)
       {<sexpr>}*)
       
   (define (<symbol> {<formal>}+ . <rest>)
       {<sexpr>}*)

   Where:
     <formal> := <symbol>
     <rest> := <symbol>
```
Define \<symbol\> with \<sexpr\> value in the current environment.
The second and third forms bind closures to the \<symbol\>.

### Closure construction
```   
   (lambda <formal>
       {<sexpr>}*)
       
   (lambda ({<formal>}*)
       {<sexpr>}*)
       
   (lambda ({<formal>}+ . <rest>)
       {<sexpr>}*)
```
The first form binds all actual arguments to \<formal\>.
The second form accepts a fixed number of actual arguments.
The third form accepts one or more actuals and binds a list of the remainder to \<rest\>.

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
       {<cond-clause>}*
       [else {<sexpr>}*])
	
   (case <sexpr>
       {<case-clause>}*
       [else {<sexpr>}+])

   Where:
      <cond-clause> := (<condition-sexpr> {<consequent>}*)
      <cause-clause> := (<match-list> {<consequent>}*)
      <match-list> := ({<sexpr>}+)
      <consequent> := <sexpr>
```

### Short circuit boolean evaluation
```
   (and {<sexpr>}*)
   (or {<sexpr>}*)
```
#f and nil are considered false. All other values are considered true.

### Frame-based environment creation
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
Although tail-recursion is the natural and efficient way to implement looping, two additional forms are provided.

### Template expansion
```
   (quasiquote <template-sexpr>)
   (unquote <sexpr>)
   (unquotesplicing <sexpr>)
```
Quasiquote (`) introduces a symbolic expression which serves as a template.
Within the template expression an unquote (,) or unquotesplicing (,@) will force evaluation of the unquoted expression.
Unquote substitutes the result of the evaluation. If the result of evaluation is a list, unquotesplicing can be
used to substitute list contents.

## Functions

### Exit Function
```
   (exit)
```
Exit the interpreter by function. Or type ^D or ^C.

### List Functions
```
   (car <list>) -> <pair-sexpr1>
   (cdr <list>) -> <pair-sexpr2>
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

   Where:
     <plist> := ({<propery-sexpr> <property-value-sexpr>}*)
     <property> := <sexpr>
     <property-value> := <sexpr>
```

### Application Control Functions
```
   (apply <fn> {<sexpr>}+) -> <fn-result-sexpr>
   (call/cc <closure>) -> <fn-result-sexpr>
   (eval <sexp> [<anyenv>]) -> <evaluated-sexpr>
   (map <fn> {<list>}+) -> <list-of-fn-results>
   (for-each <fn> {<list>}+) -> nil
   (force <promise>) -> <evaluated-delay-sexpr>

   Where:
     <fn> := any callable
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
   (the-environment) -> <anyenv>
   (procedure-environment <closure>) -> <anyenv>
   (environment-bindings <anyenv>) -> <bindings>
   (environment-parent <anyenv>) -> <anyenv>
   (%make-environment <bindings> <anyenv) -> <env>
   (the-global-environment) -> <global-env>
   (%make-assoc-env [<anyenv>]) -> <assoc-env>
   (%assoc-env-has? <assoc-env> <symbol>) -> <boolean>
   (%assoc-env-ref <assoc-env> <symbol>) -> <value-sexpr>
   (%assoc-env-set! <assoc-env> <symbol> <value-sexpr>) -> <value-sexpr>

   Where:
     <global-env> := the global environment
     <env> := frame-based environment
     <assoc-env> := associative environment
     <anyenv> := <global-env> | <env> | <assoc-env>
     <binding> := (<symbol> . <value-sexpr>)
     <bindings> := ({<binding>}*)
   
```

### Byte Code Functions (Compiled)
```
   (%make-code <byte-vector> <sexpr-vector>) -> <code>
   (%get-bcodes <code>) -> <byte-vector>
   (%get-sexprs <code>) -> <sexpr-vector>
   (compile <sexpr> [<anyenv>]) -> <code>
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
   (member <sexpr> <list>) -> (<sexpr> | nil)
   (memq <sexpr> <list>) -> (<sexpr> | nil)
   (memv <sexpr> <list>) -> (<sexpr> | nil)
   (assoc <key-sexpr> <assoc-alist>) -> (<pair> | nil)
   (assq <key-sexpr> <assoc-list>) -> (<pair> | nil)
   (assv <key-sexpr> <assoc-list>) -> (<pair> | nil)

   Where:
     <pair> := (<key-sexpr> . <value-sexpr)
     <assoc-list> := ({<pair>}*)   
```

### Closure Functions
```
   (%closure-benv <closure>) -> <anyenv>
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

### Memory Management Functions
```
   (gc) -> <mm-statistics-vector>
   (mm) -> <mm-statistics-vector>
   (fs) -> <fs-statistics-vector>
```
(gc) forces garbage collection. (mm) simply returns memory managment statisitcs. (fs) returns frame store statistics.

### Socket Functions
```
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

   Where:
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
```

## Extensions

Escheme has been extended with a number of useful packages which allow
advantageous structuring of code: modules and object systems.

### Escheme Module System (EMS)

EMS is an approximate implemenation of the module
system provided by STklos.

See escheme-extensions/ems.

### Escheme Object System (EOS)

EOS is an Dylan inspired object system employing classes and generic 
functions. EOS uses multi-dispatch to select the generic function implemenation
for dispatch.

See escheme-extensions/eos.

### "X" Object System (XOS)

XOS is an xscheme inspired object system that is metaclass based. For those familiar 
with Python it should feel familiar -- classes, methods, inheritance -- but with an s-expression syntax.

See escheme-extensions/xos.
