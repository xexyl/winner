## To build:

``` <!---sh-->
    make
```

**NOTE**: this program requires the tool `tcpserver` to use. If you do not have this
program see the
FAQ on "[tcpserver](../../faq.html#tcpserver)".


## To use:

``` <!---sh-->
    tcpserver -v 127.0.0.1 10333 ./prog ioccc.txt
```

Then connect to http://localhost:10333 with your browser.


## Judges' remarks:

[How did the leopard get its
spots](https://www.livescience.com/950-leopard-spots.html)? Why are a [zebra's
stripes](https://africafreak.com/zebra-stripes) unique?

In case you didn't recognise the portrait of [Alan
Turing](https://en.wikipedia.org/wiki/Alan_Turing), there are some
pointers to his features like eyes with "`stdeye-o`" and "`<--mouth`". Can you
find any other tributes?


## Author's remarks:

What does "[Alan Turing](https://en.wikipedia.org/wiki/Alan_Turing)" remind you
of? [Cryptanalysis](https://en.wikipedia.org/wiki/Cryptanalysis) of the [Enigma
machine](https://en.wikipedia.org/wiki/Enigma_machine)?  [Turing
machine](https://en.wikipedia.org/wiki/Turing_machine)? [Turing
test](https://en.wikipedia.org/wiki/Turing_test)? This program is also based on
his work.


### Whirlwind tour

Compile the program as follows:

``` <!---sh-->
    cc -O3 -std=c99 \
           -Wall -Wextra -pedantic -o prog prog.c \
           -Delta_u="Du*laplacian(u)-u*v*v+F*(1-u)" \
           -Delta_v="Dv*laplacian(v)+u*v*v-v*(F+K)" \
           -DF=0.040 -DK=0.060 -DDu=0.200 -DDv=0.100 \
           -DUV_BACKGROUND=1,0 -DUV_DROP=-0.5,0.5 \
           -DTIMESTEP=1.0 -DSPEED=2 -DRGB=255:128:192
```

Then, run it with the `tcpserver` command in the
[ucspi-tcp](http://cr.yp.to/ucspi-tcp.html) package, which was created by
[Daniel J. Bernstein](../../authors.html#Daniel_J_Bernstein)'s
[1991/brndstd](../../1991/brnstnd/index.html) "Best of Show".

``` <!---sh-->
    tcpserver -v 127.0.0.1 10333 ./prog ioccc.txt
```

Finally, open `http://127.0.0.1:10333/` in a modern browser supporting HTML5.
You can use the mouse cursor to place drops into the [Petri
dish](https://en.wikipedia.org/wiki/Petri_dish) interactively.


### Description

This is a simulator of [reaction-diffusion
systems](https://en.wikipedia.org/wiki/Reaction-diffusion_system).

Reaction-diffusion systems, proposed by Alan Turing in [^1], are mathematical
models in which two chemical substances are transformed into each other (*local
chemical reactions*) and spread out (*diffusion*).  Their interactions sometimes
form non-trivial patterns, such as spots, spiral, dappling, and labyrinths.

The systems are generally formulated as follows:

```
    du/dt = F(u, v) + Du * laplacian(u)
    dv/dt = G(u, v) + Dv * laplacian(v)
```

The functions `F(u, v)` and `G(u, v)` represent reactions of each substance, and
the parameters `Du` and `Dv` are diffusion coefficients.  By changing the
functions and parameters, you can see a wide range of behaviors.

See [the Wikipedia article of "Reaction-diffusion
system"](https://en.wikipedia.org/wiki/Reaction%E2%80%93diffusion_system) in
detail.

This program simulates the systems.  The field consists of 128 x 128 cells.
Each cell includes information of an amount of the two substances, and interacts
with neighbor cells (in terms of [von Neumann
neighborhood](https://en.wikipedia.org/wiki/Von_Neumann_neighborhood) lattice) in each frame.


### Configuration

You can change the model, initial pattern, simulation speed, and color via macro
definition.


#### Model: `-Delta_u=<expression> -Delta_v=<expression>`

The definitions represent the equations of reaction-diffusion system.  For
example, the following is [the Gray-Scott
model](http://groups.csail.mit.edu/mac/projects/amorphous/GrayScott/) in TeX
notation.

```
    \frac{\partial u}{\partial t} = D_u \Delta u - u v^2 + F (1 - u)
    \frac{\partial v}{\partial t} = D_v \Delta v + u v^2 - v (F + K)
```

You can specify this model as follows:

```
    -Delta_u="Du*laplacian(u)-u*v*v+F*(1-u)" \
    -Delta_v="Dv*laplacian(v)+u*v*v-v*(F+K)"
```

`Du`, `Dv`, `F`, and `K` are parameters of that model.


#### Initial pattern: `-DUV_BACKGROUND=<u0>,<v0> -DUV_DROP=<u1>,<v1>`

The definitions specifies the initial amount of the two substances `u` and `v`.
All cells have `(u0, v0)` by default.

You can specify a pattern file when the executable is invoked (not
compile-time).  The file should include a sequence of points, see
[ioccc.txt](ioccc.txt),
[center.txt](center.txt),
or [line.txt](line.txt) as
examples.  Each amount in cells near to the point included in the file, will be
added by `(u1, v1)`.


#### Simulation speed: `-DTIMESTEP=ts -DSPEED=spd`

The definitions are for changing the simulation speed.

`TIMESTEP` is a simulation time step.  The bigger, the faster but more unstable.

`SPEED` is a frame skip parameter.  The bigger, the faster but more sluggish.


#### Rendering color: `-DRGB=r:g:b`

This specifies a color used for rendering.


### Examples

Some interesting parameters are included in `Makefile`.  Do one of the following
commands and open `http://127.0.0.1:10333`.

```
    make gray-scott-1 && tcpserver -v 127.0.0.1 10333 ./gray-scott-1 ioccc.txt
    make gray-scott-1 && tcpserver -v 127.0.0.1 10333 ./gray-scott-1 center.txt
    make gray-scott-1 && tcpserver -v 127.0.0.1 10333 ./gray-scott-1 line.txt
    make gray-scott-2 && tcpserver -v 127.0.0.1 10333 ./gray-scott-2 ioccc.txt
    make gray-scott-3 && tcpserver -v 127.0.0.1 10333 ./gray-scott-3 ioccc.txt
    make gray-scott-4 && tcpserver -v 127.0.0.1 10333 ./gray-scott-4 ioccc.txt
    make fitzhugh-nagumo-1 && tcpserver -v 127.0.0.1 10333 ./fitzhugh-nagumo-1 ioccc.txt
    make fitzhugh-nagumo-2 && tcpserver -v 127.0.0.1 10333 ./fitzhugh-nagumo-2 ioccc.txt
    make oregonator && tcpserver -v 127.0.0.1 10333 ./oregonator ioccc.txt
```

These examples are created by drawing (and modifying) the pattern files of
[Ready](https://github.com/GollyGang/ready), a cross-platform implementation of
various reaction-diffusion systems [^2].


### Compatibility


#### C part

This program compiles clean with no warnings under `-std=c99 -Wall -Wextra
-pedantic`.  Since it uses `long long` for 64bit integers, C99 is required.


#### Browser part

You must use a modern browser that supports the latest specification of
WebSocket [^3].  All the following browsers worked for me:

* Google Chrome 45
* Mozilla Firefox 39
* Microsoft Internet Explorer 11 (works but sluggish)

I think Safari 6 will work but I haven't tested.  (I have no recent macOS.)

NOTE from the judges in 2023: recent versions of Safari also work.


#### tcpserver alternatives

I think you may use `inetd` and `xinetd` instead of `tcpserver`.  Here is an
example `inetd` configuration.

```
    10333 stream tcp nowait nobody /path/to/prog /path/to/prog /path/to/initial-pattern.txt
```

But note that I didn't test well.


### Internal

1. The C program accepts HTTP connection from the browser, and sends a HTML
document.
2. The browser tries to upgrade the connection to WebSocket.
3. The C program accepts WebSocket upgrade.  This negotiation process requires
SHA1 hash [^4] and Base64 encoding [^5].  The C program calculates them without
the aid of any external libraries.
4. The C program simulates the reaction-diffusion system, and then send the
field data to the browser on each frame.
4. The browser receives and renders the field data onto the HTML5 canvas.


### References

* [^1]: Turing, A. M., "The Chemical Basis of Morphogenesis", Philosophical Transactions of the Royal Society, 1952.
* [^2]: The Ready Bunch, "Ready", A cross-platform implementation of various reaction-diffusion systems, since 2011, <https://github.com/GollyGang/ready>.
* [^3]: Fette, I. and A. Melnikov, "The WebSocket Protocol", RFC 6455, DOI 10.17487/RFC6455, December 2011, <http://www.rfc-editor.org/info/rfc6455>.
* [^4]: Eastlake 3rd, D. and P. Jones, "US Secure Hash Algorithm 1 (SHA1)", RFC 3174, DOI 10.17487/RFC3174, September 2001, <http://www.rfc-editor.org/info/rfc3174>.
* [^5]: Josefsson, S., "The Base16, Base32, and Base64 Data Encodings", RFC 4648, DOI 10.17487/RFC4648, October 2006, <http://www.rfc-editor.org/info/rfc4648>.

<!--

    Copyright © 1984-2024 by Landon Curt Noll. All Rights Reserved.

    You are free to share and adapt this file under the terms of this license:

        Creative Commons Attribution-ShareAlike 4.0 International (CC BY-SA 4.0)

    For more information, see:

        https://creativecommons.org/licenses/by-sa/4.0/

-->
