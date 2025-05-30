## To build:

``` <!---sh-->
    make
```


### Bugs and (Mis)features:

The current status of this entry is:

> **STATUS: INABIAF - please DO NOT fix**

For more detailed information see [2005/mikeash in bugs.html](../../bugs.html#2005_mikeash).


## To use:

``` <!---sh-->
    ./mikeash
```

**NOTE**: the author stated that they tested this under i386 machines but this works
under 64-bit (x86_64, arm64) machines too.


## Try:

``` <!---sh-->
    ./try.sh
```


## Judges' remarks:

Having problems speaking code?  Do you [LISP][]?  Parenthetically
speaking, this entry takes advantage of C99 features, while
speaking parenthetically.  Best of all, it self-reproduces!

John McCarthy might not have imagined it quite like this!  :-)

[LISP]: https://en.wikipedia.org/wiki/Lisp_(programming_language)


## Author's remarks:

This program is a self-reproducing program, which is on the IOCCC's list
of discouraged program types. Worse, it's a self-reproducing program
written in [Common Lisp](https://en.wikipedia.org/wiki/Common_Lisp). What's this
doing as an entry to the IOCCC?

Start over: this program, when compiled with a C99 compiler, is an
interpreter for an extremely limited and highly broken subset of [Common
Lisp](https://en.wikipedia.org/wiki/Common_Lisp).

It requires C99 because of `C99`'s ability to declare variables in the
initializer of a `for` loop. Unfortunately, C99 mode typically warns about
implicitly declared functions, and preprocessor directives prevent the program
from being valid [Common Lisp](https://en.wikipedia.org/wiki/Common_Lisp). As a
result, several warnings will be printed about implicit declarations of
functions. There are also warnings about unknown escape sequences in the
constant string, due to different string escape semantics in C and Lisp, and
some implicit integer-to-pointer conversions due to the implicitly-declared
functions.

Try:

``` <!---sh-->
    echo '(format t "~s" (+ 2 2 ))' | ./mikeash
```

You should see `4"` echoed on your command prompt. The `"` after the 4 and
the lack of a newline are due to the somewhat broken implementation of
the format function. More sophisticated arithmetic expressions may be
used:

``` <!---sh-->
    echo '(format t "~s" (* (+ 2 2 ) (- 5 (/ 9 3 ))))' | ./mikeash
```

This evaluates the expression `(2+2) * (5 - 9/3)` giving the correct result,
`8"` (with the same issue of no newline after the `"`).  Note that due to the
highly ad-hoc nature of the tokenizer, the spaces before the closing parentheses
are mandatory.

The program can also be run in an interactive mode, by simply executing
`./mikeash` and then typing lisp expressions, but this is difficult due to the
lack of proper editing facilities and the total lack of error recovery in the
interpreter. Typos will most likely crash the program.

Aside from being required to put spaces between tokens and closing parentheses,
there are other limitations on the dialect of [Common
Lisp](https://en.wikipedia.org/wiki/Common_Lisp) that this program understands:

* The only built-in functions are `defvar`, `format`, `substitute`,
`char-upcase`, `+`, `-`, `*`, and `/`.

* Only one variable is available. This variable is called `q`, although any
symbol name starting with the letter `q` will be mapped to this variable.

* Only the first letter of a function name is significant. For example,
`format`, `f`, `farm`, and various other words starting with `f` will all map to
the `format` function.

* The `defvar` function completely ignores its first parameter. It will always
store the result of evaluating its second parameter in `q`. Unlike in [Common
Lisp](https://en.wikipedia.org/wiki/Common_Lisp), multiple invocations of
`defvar` on the same variable will overwrite old values.

* Character constants are limited to `#\Newline` and single-character
constants such as `#\x`.

* The `char-upcase` function will give strange results if passed something
other than a lowercase character.

* The `format` function takes exactly three parameters. The first parameter is
ignored; output always goes to `stdout`. The second parameter must have exactly
one `~s` format specifier in it, and no other format specifiers are permitted.

* The arithmetic functions take exactly two parameters.

* All tokens must be separated from a following `)` by whitespace.

* A lot of other things. Notably, the language which this program interprets is
nowhere near
[Turing-complete](https://en.wikipedia.org/wiki/Turing_completeness).

Basically, the [LISP][] interpreter is good for some basic math operations, and
for running itself. To do the latter, try this:

``` <!---sh-->
    ./mikeash < mikeash.c > mikeash2.c
    diff mikeash.c mikeash2.c
```

The program can also be run from an implementation of [common
lisp](https://en.wikipedia.org/wiki/Common_Lisp). To do
this, run the common lisp compiler and then type the following command:

```
    (load "mikeash.c")
```

The source code of [mikeash.c](%%REPO_URL%%/2005/mikeash/mikeash.c) will be printed.


### Obfuscation

`Lispob` is obfuscated in several ways. First, it's actually two programs
in one, which actually share a small amount of code. Running a C code
beautifier will destroy the [LISP][] program contained within. Second and
more importantly, it is very badly written, which will presumably
confuse people who are used to looking at well-implemented interpreters.

The program comes in at just under 2k of significant characters.  However, this
size is exaggerated, because it contains a complete copy of itself for the
self-reproducing [LISP](https://en.wikipedia.org/wiki/Turing_completeness)
program, making it twice the size. This doubling effect made it something of a
challenge to fit inside the IOCCC's size limit.

This code should be highly portable. It was tested under Mac OS X and
Linux on i386, but it should run elsewhere. It requires ASCII due to
hardcoding various character constants. Other than this requirement, it
should run anywhere there's a conforming C99 compiler and a standard
C library.


<!--

    Copyright © 1984-2024 by Landon Curt Noll. All Rights Reserved.

    You are free to share and adapt this file under the terms of this license:

        Creative Commons Attribution-ShareAlike 4.0 International (CC BY-SA 4.0)

    For more information, see:

        https://creativecommons.org/licenses/by-sa/4.0/

-->
