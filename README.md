# Erlang-Internals-Info

Links to blog posts, specifications and other documentation about the Erlang/BEAM internals which I've gleaned recently.


## BEAM (Bogdan/Björn's Erlang Abstract Machine) and ERTS (Erlang RunTime System):

1. BEAM book:

https://happi.github.io/theBeamBook/

Very good book which covers almost all the aspects of the Erlang RunTime System and BEAM subsystems.

2. A very interesting part from the aforementioned book about the BEAM code loader and transformation from generic to specific instructions:

https://happi.github.io/theBeamBook/#CH-Beam_loader

Basically the Erlang bytecode loader does a large amount of work rewriting the generic "transport format" bytecode in the object files into the concrete internal bytecode operations that are actually executed. This recognizes common sequences and replaces them with optimized single-opcode versions. 

3. A peak into the Erlang compiler and BEAM bytecode:
 
http://gomoripeti.github.io/beam_by_example/

This is an overview of all the intermediate representations of the Erlang code. Steps described in the article are as follows:

Erlang source code --> Abstract Syntax Tree ('P') --> expanded AST ('E') --> Core Erlang ('to_core') --> BEAM byte-code.

However I would also add two missing steps namely BEAM generic bytecode --> BEAM specific bytecode and Core Erlang --> Kernel Erlang.

So to sum up, the main compilation stages for Erlang are:
  - preprocessing (macros, conditional compilation, includes)
  - source-level expansions, such as records to tuples
  - translation to Core Erlang + optimizations and optional inlining
  - translation to "Kernel Erlang" + pattern matching compilation
    and further optimizations
  - translation to Beam assembler + some more optimizations
  - encoding as Beam bytecode

4. StackOverflow question with an interesting answer which contains some details about the Erlang pattern match compiler:

https://stackoverflow.com/questions/587996/erlang-beam-bytecode

5. Source code of the Kernel Erlang compiler (`v3_kernel.erl`) with useful comments:

https://github.com/erlang/otp/blob/master/lib/compiler/src/v3_kernel.erl#L20

6. A nice thread descibing compilation to BEAM bytecode via `EAF` (in the context of Elixir). It also contains useful links:

https://elixirforum.com/t/getting-each-stage-of-elixirs-compilation-all-the-way-to-the-beam-bytecode/1873

7. Erlang `compile` module docs:

http://erlang.org/doc/man/compile.html#file-2

8. BEAM file format:

http://rnyingma.synrc.com/publications/cat/Functional%20Languages/Erlang/BEAM.pdf

9. Erlang abstract format docs:

http://erlang.org/doc/apps/erts/absform.html

10. The Erlang BEAM Virtual Machine Specification by one of its designers (Björn in the BEAM):

http://www.cs-lab.org/historical_beam_instruction_set.html

A little bit outdated but nonetheless useful

11. A Peek Inside the Erlang Compiler:

http://prog21.dadgum.com/127.html

12. BEAM wisdoms.

BEAM file format:

http://beam-wisdoms.clau.se/en/latest/indepth-beam-file.html#

BEAM instructions:

http://beam-wisdoms.clau.se/en/latest/indepth-beam-instructions.html#

13. Also a nice description of the BEAM instruction set:

http://erlangonxen.org/more/beam

14. Thesis which contains some useful information about the Erlang implementation:

http://uu.diva-portal.org/smash/get/diva2:428121/FULLTEXT01.pdf

15. Code of the BEAM loader which was described above:

https://github.com/erlang/otp/blob/master/erts/emulator/beam/beam_load.c

You might be particularly interested in the `static int transform_engine(LoaderState* st)` function.

16. Internal doc in the erts/emulator is a treasure trove of the underlying implementation details:
https://github.com/erlang/otp/tree/master/erts/emulator/internal_doc

17. Will put reference to the Erlang garbage collection doc as a separate link:
https://github.com/erlang/otp/blob/master/erts/emulator/internal_doc/GarbageCollection.md


## Optimisations and monitoring

1. Internal OTP doc which contains useful insights regaring the process managements, lock and run queues:
https://github.com/erlang/otp/blob/master/erts/emulator/internal_doc/ProcessManagementOptimizations.md

2. Insights by one of the original designers of BEAM SMP support (Rickard Green) into the scheduler [compaction of load mode](http://erlang.org/doc/man/erl.html#+scl) taken from this email thread: http://erlang.org/pipermail/erlang-questions/2012-October/069585.html

> The runtime system tries to compact the load on as few schedulers as possible without getting run-queues that build up. The runtime system wont wake up new schedulers unless some overload has accumulated. This overload either show up as a quickly growing run-queue or a small run-queue over a longer time. The +swt flags sets the threshold that is used for determining when enough overload has accumulated to wake up another scheduler. This compaction of load onto fewer schedulers is there in order to reduce communication overhead when there aren't enough work to fully utilize all schedulers. The performance gain of this compaction depends on the hardware. We have gotten reports about problems with this functionality, but we have not found any bugs in this functionality. We have only found that it behaves as expected. That is, if more schedulers aren't woken this is 
due to not enough accumulated overload. The +swt switch was introduced in order to give the user the possibility do define what is enough overload for his or her taste. The currently used wakeup strategy is very quick to forget about previously accumulated overload that has disappeared. Maybe even too quick for my taste when "+swt very_low" is used. I've therefore  implemented an alternative strategy that most likely will be the default in R16. As of R15B02 you can try this strategy out by passing "+sws proposal" as a command line argument. In combination with "+swt very_low", the runtime system should be even more eager to wake up schedulers than when only using "+swt very_low".
 

## BEAM internal code references

1. In the context of Erlang garbage collection it is very important I believe to put a link to the GC implementation itself:
https://github.com/erlang/otp/blob/0183ddffb112d898d9b8c0396afa7f2210b9e169/erts/emulator/beam/erl_gc.c#L665

2. `erl_create_process` function:
https://github.com/erlang/otp/blob/d423a7af502227afcdcf7d2a1efecded85ea95fb/erts/emulator/beam/erl_process.c#L11724

Be prepared to wait a liitle bit because `erl_process.c` file has almost 13500 lines of C code. This is a cornerstone of the whole Erlang platform I think.

3. We always use BIFs in Erlang such as `element` or `spawn_link` or whatnot in our code. `gen` and `gen_server` are based on `erlang:spawn` BIF, hence I think it's inportant to know how BIFs are actually implemented.
https://github.com/erlang/otp/blob/7a19de1bf0eb863e0d0febf7e7e5e555c8628575/lib/stdlib/src/erl_internal.erl#L61

`stdlib/erl_internal` here is just a BIF-proxy because all the functions are implemented natively in C inside the ERTS/BEAM. But still it's useul to see a full list of allowed in guards BIFs and other ones.
And the core C implementation resides here:
https://github.com/erlang/otp/blob/7a19de1bf0eb863e0d0febf7e7e5e555c8628575/erts/emulator/beam/bif.c#L69

And this is also I think one of the most important parts of the Erlang.

4. The most important parts from the code of the Erlang code purger. Vital part of the ERTS as well.

https://github.com/erlang/otp/blob/1526eaead833b3bdcd3555a12e2af62c359e7868/erts/preloaded/src/erts_code_purger.erl#L61

^ `pending_purge_lambda`, `purge`, `soft_purge`, `check_proc_code` are the functions you might be particularly interested in.
Comments contain very important piece of information.

5. Erlang lists:

https://github.com/erlang/otp/blob/master/lib/stdlib/src/lists.erl#L20

Most of the `lists` moudle functionality is incapsulated here ^. At first you will see some of the functions like `keyfind/3`, `keymember/3`, etc. which are implemented as BIFs and defined in a separate C file inside of BEAM:

https://github.com/erlang/otp/blob/1526eaead833b3bdcd3555a12e2af62c359e7868/erts/emulator/beam/erl_bif_lists.c#L21


6. Core native functionality of the Erlang message passing:

https://github.com/erlang/otp/blob/1526eaead833b3bdcd3555a12e2af62c359e7868/erts/emulator/beam/erl_message.c#L48

7. Internal BEAM emulator docs:
https://github.com/erlang/otp/tree/master/erts/emulator/internal_doc

P.S. New links will be added in future if i spot something interesting and relevant.
