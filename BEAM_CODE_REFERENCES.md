## External Term Format

[Official Erlang documentation](http://erlang.org/doc/apps/erts/erl_ext_dist.html) on this topic

[term_to_binary/2](http://erlang.org/doc/man/erlang.html#term_to_binary-2) docs

[C implementation](https://github.com/erlang/otp/blob/master/erts/emulator/beam/external.c#L2061) of the term_to_binary in ERTS.


## Erlang GC

Garbage collect [C implementation](https://github.com/erlang/otp/blob/master/erts/emulator/beam/erl_gc.c#L664).


## Erlang processes

[erl_create_process](https://github.com/erlang/otp/blob/master/erts/emulator/beam/erl_process.c#L11391) function.

Be prepared to wait a liitle bit because `erl_process.c` file has almost 13500 lines of C code. This is a cornerstone of the whole Erlang platform I think.


## BIFs

We always use BIFs in Erlang such as `element` or `spawn_link` or whatnot in our code. 

`gen` and `gen_server` are based on the `erlang:spawn` BIF, hence I think it's important to know how BIFs are actually implemented.
https://github.com/erlang/otp/blob/master/lib/stdlib/src/erl_internal.erl#L22

`stdlib/erl_internal` here is just a BIF-proxy because all the functions are implemented natively in C inside the ERTS/BEAM. 

Core C implementation of BIFs [resides here](https://github.com/erlang/otp/blob/master/erts/emulator/beam/bif.c#L69)

## Code Purger

The most important parts from the [Erlang code purger](https://github.com/erlang/otp/blob/master/erts/preloaded/src/erts_code_purger.erl#L61) such as `purge`, `soft_purge`, etc.


## Standard Library

[Lists module](https://github.com/erlang/otp/blob/master/lib/stdlib/src/lists.erl#L20) functionality. Some of the functions such as `keyfind/3`, `keymember/3`, etc. are implemented as BIFs and defined in a separate [C file inside of BEAM](https://github.com/erlang/otp/blob/master/erts/emulator/beam/erl_bif_lists.c).

## Message Passing

Core functionality of the [Erlang message passing](https://github.com/erlang/otp/blob/master/erts/emulator/beam/erl_message.c)

## Internal BEAM emulator docs:
https://github.com/erlang/otp/tree/master/erts/emulator/internal_doc
