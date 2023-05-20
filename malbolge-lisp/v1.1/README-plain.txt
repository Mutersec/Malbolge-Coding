                     ===--------------------===
                     ==[ MALBOLGELISP v 1.1 ]==
                     ===--------------------===
#MalbolgeLISP v1.1



Discord:https://discord.gg/WRWEk6jKG7



==[ 1. What's MalbolgeLISP?

        MalbolgeLISP is a LISP interpreter written in Malbolge.
It's as of 2020 and now 2021, the most advanced, usable Malbolge
program ever created. It supports everything LISPs generally tend
to support (like `cond', `let', `lambda', etc...). The v1.1 release
greatly improved the performance and reduced the code size, while
adding a few features.

==[ 2. What are these files?

        The release bundle includes interpreter binaries for Windows
(x64, one is optimised for memory consumption and one for speed),
and a few interpreters for Linux (the names should be self explanatory,
also x64). If you're not running a x64 machine, you'll have to compile
`fast20.c' yourself. From my observations, the best results were yielded
by GCC and the code has been tuned to perform well when compiled with
it. `malbolgelisp-v1.1.mb' is the source code for the interpreter.

==[ 3. How to use MalbolgeLISP?

        If you use Windows, drag the source file on one of the
interpreters and wait for it to display a banner. If you use Linux, launch
the interpreter from a shell session - `./fast20 malbolgelisp-v1.1.mb'.
If you built `fast20' yourself, you should know well how to launch it :).

==[ 4. Using the REPL

        After you start the interpreter, you should be greeted with a
banner (containing the version, dot commands, etc..). The following dot
commands are currently available:
  -  .F  - display all the features recognized by the interpreter
  -  .A  - display the information about the interpreter's author
  -  .R  - reset the memory
  -  .M  - display the information about memory consumption
  -  Any other command (and sending EOF - ^Z on Windows, ^D on UNIXes
     will terminate the interpreter).

        After entering an expression (for example - `(+ 2 2)'), the
interpreter should print something akin to this:

   % (+ 2 2)
   ..............|.....
   4
   %

        The dots are used to signal that the code is being parsed
(before the pipe character) or evaluated (after the pipe character).
The amount of dots doesn't correspond to any consistent amount of time.

==[ 5. The language

        The interpreter supports the following features that will be
discussed in this document:

   MALBOLGELISP V1.1 (2020-2021, PALAIOLOGOS)
   DOT COMMANDS:  .F(eatures)  .A(uthor)  .R(eset)  .M(emory)
   % .F
   ' + - < > = ! & | * / define defun lambda
   cond print atom cons car cdr if let
   iota size nth

==[ 6. Arithmetics on numbers

        A few examples of performing arithmetics in MalbolgeLISP
are presented below. Note: Signed arithmetics aren't supported.

   % (+ 2 -1)
   ..............|.....
   1
   % (* 3 3)
   ..............|.....
   9
   % (% 5 2)
   ..............|.....
   1
   % (> 5 6)
   ..............|.....
   0
   % (< 5 6)
   ..............|.....
   1
   % (= 6 6)
   ..............|......
   1
   % (! (= 6 7))
   ......................|.........
   1
   % (/ 25 5)
   ..............|.....
   5
   % (- 4 3)
   ..............|.....
   1
   % (& (= 2 2) (= 3 3))
   ....................................|...............
   1

        There is no `>=' or `<=', it has to be implemented by negating
respectively `<' and `>'. A single `&' and `|' are respectively logical
AND and OR.

==[ 7. `define', `defun' and `lambda'.

        In MalbolgeLISP, you can introduce constants using the `define'
function. For example:

   % (define x 5)
   ................|..
   5
   % (+ x 1)
   ................|.....
   6

        MalbolgeLISP also supports lexically scoped (a function sees it's
lexical ancestor's environment, not it's callers) lambdas. For instance:

   % ((lambda (n) (* n n)) 3)
   ........................................|.........
   9
   % ((lambda (m) ((lambda (n) (+ m n)) 2)) 2)
   ........................................................|.............
   4

        How to define functions then? Well, a logical conclusion can be
drawn from the examples above:

   % (define succ (lambda (x) (+ x 1)))
   .............................................|..
   (lambda (x) (+ x 1))
   % (succ 2)
   ...........|.........
   3

        Because `define lambda' is a bit annoying to type down and doesn't
look all that well, we can use a nicer syntax for that - `defun':

   % (defun succ (x) (+ x 1))
   .....................................|.
   (lambda (x) (+ x 1))
   % (succ 2)
   ...........|.........
   3

==[ 8. Operations on lists and atoms

        The equals function (`=') also works on atoms and lists. Although,
you can't just introduce a list in your code, since an attempt of evaluating
it would be made by an interpreter. For this reason, you have to use quoting,
like so:

   % (= test test)
   ..................|......
   1
   % (= '(1 2 3) '(1 2 3))
   ..................................|.........
   1
   %

        The `iota' function generates natural numbers from 0 to N-1 (inclusive).
For instance:

   % (iota 5)
   ...........|....
   (0 1 2 3 4)

        `size' yields the size of a list passed as an argument. Using `size' and
`iota', we can make a really inefficient identity function for numbers:

   % (defun id (x) (size (iota x)))
   ..........................................|.
   (lambda (x) (size (iota x)))
   % (id 6)
   ...........|...........
   6
   % .M
   233B USED
   % # That's a lot of memory wasted!

       `nth' yields the N-th element of a list. It uses the same indexing as `iota',
as demonstrated below:

   % (nth 3 (iota 5))
   ......................|........
   3

        Let's try using atoms now. We can print an atom or a list using `print', and
we can check if something is an atom using the `atom' function. Notice that `print'
is an identity function with a side effect of printing an expression's value:

   % (atom null)
   ...........|....
   1
   % (atom '(1 2 3))
   .....................|....
   0
   % (print hello)
   .............|....
   hello

   hello
   % (print '(1 2 3))
   .....................|....
   (1 2 3)

   (1 2 3)
   %

        Let's look at how `car' and `cdr' functions work. `car' takes the head (first
element) of a list, and `cdr' takes the tail (everything _except_ the first elemen) of
a list:

   % (define list '(1 2 3))
   ..........................|..
   (1 2 3)
   % (car list)
   .............|....
   1
   % (cdr list)
   .............|....
   (2 3)

        Finally, `cons' can be used to prepend someting to a list. Like this:

   % (cons 3 '(1 2))
   .....................|.....
   (3 1 2)

==[ 9. `if', `cond' and `let'

        The `if' function requires exactly 3 parameters - the condition, expression
to evaluate if the condition is true, and the expression to evaluate if the condition
is false. A small example:

   % (defun gt10 (x) (if (> x 10) (print yes) (print no)))
   .......................................................................|.
   (lambda (x) (if (> x 10) (print yes) (print no)))
   % (gt10 5)
   ...........|...............
   no

   no
   % (gt10 16)
   ...........|...............
   yes

   yes

        If you need a lot of `if`s in one place, you can consider using `cond`. It takes
a list of lists `(condition result)', optionally including a `(result)' list. Using `cond',
we can make a three-way-comparison function (that returns `2 1 0' for `> = <').

   % (defun <=> (x y) (cond ((> x y) 2) ((< x y) 0) (1)))
   ....................................................................................|.
   (lambda (x y) (cond ((> x y) 2) ((< x y) 0) (1)))
   % (<=> 5 6)
   ..............|.....................
   0

        The final builtin function is `let'. It allows us to bind names to expressions (so
that they're not re-evaluated at a later time). For example:

   % (let (x 5 y 6) (+ x y))
   .............................................|.............
   11

==[ 10. Example programs

# Quicksort (@La Condizione)
(defun filter (f l) (cond ((= l null) null) ((f (car l)) (cons (car l) (filter f (cdr l)))) (1 (filter f (cdr l)))))
(defun append (a b) (if (= null a) b (cons (car a) (append (cdr a) b) )))
(defun append3 (a b c) (append a (append b c)))
(defun list>= (m list) (filter (lambda (n) (! (< n m))) list))
(defun list< (m list) (filter (lambda (n) (< n m)) list))
(defun qsort (l) (if (= null l) null (append3 (qsort (list< (car l) (cdr l))) (cons (car l) null) (qsort (list>= (car l) (cdr l))))))
(qsort '(6 8 1 0 6 8 2))

# "Dumb" sort (@umnikos)
(defun insert (i l) (if (= null l) (cons i l) (if (> i (car l)) (cons (car l) (insert i (cdr l))) (cons i l))))
(defun insertsort (l) (if (= null l) null (insert (car l) (insertsort (cdr l)))))
(insertsort '(6 8 1 0 6 8 2))

# And a bunch of testing programs I wrote myself:

# Counter
(defun counter (x) (cond ((< x 10) (counter (print (+ 1 x)))) (print ok)))
(counter 1)

# Power
(defun power (x y) (if (= y 0) 1 (* x (power x (- y 1)))))
(power 3 5)

# Factorial
(defun fac (n) (if (= n 0) 1 (* n (fac (- n 1)))))
(fac 6)

# Maximum
(defun max (l) (if (= null l) 0 (let (m (max (cdr l))) (if (> (car l) m) (car l) m))))
(max '(2 5 1 9 10))
