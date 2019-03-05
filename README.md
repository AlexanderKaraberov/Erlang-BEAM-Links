# Erlang-Internals-Info

Links to blog posts, specifications and other documentation about the Erlang/BEAM internals which I've gleaned recently. Perhaps in future I'll produce several detailed blog posts based on all this information.


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

## ETS Table

When access an ETS table, there are wo locks that need to be taken:
1) A lock to access the meta table, to convert the numeric table identifier to a pointer to the actual table.
2) The lock for the table itself (either a read or write lock).
read and write_concurrency
http://erlang.org/doc/man/ets.html#new_2_write_concurrency
The default locking is on table-level, allowing only one update operation at a time per table. Table option write_concurrency will enable locking on a more fine grained level, allowing concurrent update operations. In current implementation 16 locks per table is used, which result in a probability of 1/16 that two random keys will collide on the same lock. Option write_concurrency have no effect on ordered_set's.
ETS Scalability paper:
http://winsh.me/papers/erlang_workshop_2013.pdf
ETS meta table source code:
https://github.com/erlang/otp/blob/master/erts/emulator/beam/erl_db.c#L324

## BEAM internal code references


P.S. New links will be added in future if i spot something interesting and relevant.
