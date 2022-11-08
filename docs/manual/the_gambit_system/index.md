# The Gambit System

The Gambit programming system is a full implementation of the Scheme language
which conforms to the R4RS, R5RS, R7RS and IEEE Scheme standards. It consists of
two main programs: `gsi`, the Gambit Scheme interpreter, and `gsc`, the Gambit
Scheme compiler.

The Gambit Scheme compiler translates Scheme code to another target language,
currently C or JavaScript. The C target is the most mature and it offers
portability and fast execution. The JavaScript target allows writing web apps in
Scheme.

Most of the Gambit system, including the interpreter and compiler, is written in
Scheme and compiled to portable C code using the compiler. The high portability
of the generated C code allows the interpreter, compiler and user programs to be
easily compiled and executed on any platform for which a C compiler is
available. With appropriate declarations in the source code the compiled Scheme
programs run roughly as fast as equivalent C programs.

For the most up to date information on Gambit and related resources please visit
the Gambit web page at [https://gambitscheme.org](https://gambitscheme.org).
Issues should be reported on the github source code repository
[https://github.com/gambit/gambit](https://github.com/gambit/gambit).
