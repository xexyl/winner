## To build:

``` <!---sh-->
    make all
```

There is an [alternate version](#alternate-code) that is supposed to win all the
time.


### Bugs and (Mis)features:

The current status of this entry is:

> **STATUS: INABIAF - please DO NOT fix**

For more detailed information see [1991/westley in bugs.html](../../bugs.html#1991_westley).


## To use:

**NOTE**: we have provided a script to perform the below procedure which we include
for the interested. See the [try](#try) section if you just want to play.

Make and then run as follows:


``` <!---sh-->
    ./westley [move_location] | tee nextmove.c
```

where the `move_location` is a digit from 1 to 9 that represents
a move on a tic-tac-toe board:

```
    1 2 3
    4 5 6
    7 8 9
```

If you omit `move_location`, then the computer moves first.  For your
next move, recompile `nextmove.c` and play it again:

``` <!---sh-->
    make nextmove
    ./nextmove move_location | tee nextmove.c
```


## Try:

We have provided the shell script, [ttt.sh](%%REPO_URL%%/1991/westley/ttt.sh), to make it easier to play the
game:

``` <!---sh-->
    make ttt
    ./ttt [first_move]
    ./ttt next_move
```

The shell script determines when the game is over, and automatically replaces
`merlyn.c` (copied from [westley.c](%%REPO_URL%%/1991/westley/westley.c) with an improved version after a
loss).  The [ttt](%%REPO_URL%%/1991/westley/ttt.sh) script has two other modes:

``` <!---sh-->
     # cancel any game in progress,
     # revert to merlyn.c:
    ./ttt quitgame
     # remove all game files,
     # revert to original source:
    ./ttt clobber
```

For example here is a potentially losing game:

``` <!---sh-->
    ./ttt 1
    ./ttt 4
    ./ttt 6
```

To see what it might look like, try it out.

The script redirects stderr to `/dev/null` by default. If you need errors or
warnings use `-e`.


## Alternate code:

This version is based on the author's remarks. It is not supposed to lose but it
can lose if you cheat. That's supposed to happen. Can you win any other time?


### Alternate build:

``` <!---sh-->
    make alt
```


### Alternate use:

Rather than use `ttt` try the script `ttt.alt` instead.


## Judges' remarks:

Can you figure out why the board looks different than the
[westley.c](%%REPO_URL%%/1991/westley/westley.c)?

What happens if you make a move already made?

Now go try and draw or better yet win! Why is win better than draw? Because
everyone knows how to draw:

> **Professor Falken**: Did you ever play **tic-tac-toe**?
>
> **Jennifer Mack**: Yeah. Of course.
>
> **Falken**: But you don't any more.
>
> **Jennifer**: No.
>
> **Falken**: Why?
>
> **Jennifer**: Because it's a boring game. It's always a tie.
>
> **Falken**: Exactly. There's no way to win. The game itself is pointless.  But
> back at the [International Obfuscated C Code Contest judges' room](../../judges.html) they believe
> that you can win **tic-tac-toe**, that there can be acceptable draws.

Try not to cheat (even though it's very easy :-) ), the computer has not learned
how to catch you doing it.


## Author's remarks:

A tic-tac-toe self-modifying program that "learns".  This C program
plays standard tic-tac-toe on itself.  The code is written as a
tic-tac-toe grid; moves are carried out within the code.

The human is `X`, the computer `0` [sic, "zero", not letter "O"].

The program, when run, reproduces itself with both the player's
move and the computer's move added.  Recompile THIS program (using
the same compile line) and repeat until the game is finished.

If the computer wins, the "straight face" in the upper right-hand corner, ":-|",
will change into a happy face, ":-)".  If it is a draw, the face does not
change.  If the computer loses, the board is blanked and the face changes into a
sad face, ":-(".  If this happens, the blank board should replace the original
program; the computer changes its play and will continue to do so until it no
longer loses games.

If you want a program that never loses, simply replace the string
`"9999999999  :-| "` with `"9883857753  :-| "`.

### How it works

The vertical "bars" of the grid are identical functions, with the name of the
function as `a()`, `b()`, or `c()`.  After each function is called, the global
variables `X` and `O` contain bitmasks of where all the `X`s and `O`s are placed.
The statement:

``` <!---c-->
    X=-   <char>   -1;
```

evaluates to `1`, `-1`, or `-2` when `X` is `1` and `<char>` is one of `"X"`,
`"0"`, or `" "`.  The function `S(&X)` sets the appropriate bits in (global
vars) `X` and `O` and resets the value of `&X` to `1`.  This is how the current
position is computed.

The player's move is bitwise ORed into the `X` mask, and (if it is the opening
move) the strategy string's first character is set to the player's first move
(or `5` if the computer moves first).

The evaluation function `y(M)` checks if the mask has a winning pattern (any of
the first eight bitmasks of the `w[N]` array), and sets the variable `q` to `0`
if it does.

The player's move is evaluated to see if the human has won.  The smiley is set
to ":-)" or ":-(" depending on this.  Also, if the human has won, the digit in
the strategy string is decremented to change the computer's play for the next
round.

If the human has NOT won, the `e(N,M)` function evaluates if ANY move
not in mask `N` can produce a win in mask `M`.  The first evaluation
`e(X,O)` checks if `O` can make a legal winning move.  If so, the
computer has a winning move and makes it.  If not, the smiley is
set to ":-|", and a non-winning move is generated.

A non-winning move first checks if `X` can win in one move; if so,
this move is selected, blocking the win.  Otherwise, a move is
generated by looking down the list of "move templates".  The first
move template that generates a move that is NOT already occupied by
an `X` or `O` is returned.

The "move templates" are static tic-tac-toe positions that generate
possible moves by making the `e()` function return "winning" moves,
e.g. the template:

```
    . . .
    O , O    (this is the value 40; 2^3+2^5)
    . . .    ("O"s are 1 bits,
              "." and "," are 0 bits)
```

...will make the `e()` function return a center move, as this is the only move
that produces three in a row.  Notice that the computer does not consider which
cells contain `X`s and `O`s when making a template move, only which cells are
empty and not-empty.  This makes for unusual play.

The templates are carefully chosen to eventually block all traps that `X` can
try, by just blundering in the way.

Also note that the winning & strategy templates are part of the
compile line, making it possible to change the rules of the game by
changing the compile line.

The templates are:

```
    O , O
    , , ,
    O , O

    , . .
    . O .
    . . O

    . . .
    O , O
    . . .

    . . .
    O O ,
    . . .

    . . .
    O , O
    . . .

    . , ,
    . O .
    O O ,

    O , O
    , , .
    O . .

    , . O
    O , O
    O . ,

    O . ,
    O , O
    , . O

    O , O
    , , ,
    O , O
```

A digit of `9` means start with the first template, `8` starts at
the second, etc.  The templates are scanned until a legal move is
found.  A template is scanned from bottom to top, right to left
(i.e.  move `9` is tested first, then `8`, down to move `1`).


<!--

    Copyright © 1984-2024 by Landon Curt Noll. All Rights Reserved.

    You are free to share and adapt this file under the terms of this license:

        Creative Commons Attribution-ShareAlike 4.0 International (CC BY-SA 4.0)

    For more information, see:

        https://creativecommons.org/licenses/by-sa/4.0/

-->
