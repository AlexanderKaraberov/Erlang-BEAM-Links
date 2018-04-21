# Erlang-Internals-Info

Links to blog posts, specifications and other documentation about the Erlang/BEAM internals which I've gleaned recently.

## General

1. Erlang Efficiency Guide. Not specifically related but nevertheless is a very important piece of information I believe:

http://erlang.org/doc/efficiency_guide/introduction.html

2. Implementation-specific details of how much memory native Erlang data types occupy:

http://erlang.org/doc/efficiency_guide/advanced.html#id71365

3. Evolution of the Erlang VM:

http://www.erlang-factory.com/upload/presentations/247/erlang_vm_1.pdf

4. How Erlang does scheduling:

http://jlouisramblings.blogspot.ch/2013/01/how-erlang-does-scheduling.html

5. A History of Erlang by Joe Armstrong

http://webcem01.cem.itesm.mx:8005/erlang/cd/downloads/hopl_erlang.pdf

Nice article by one of the original creators describing the evolution of Erlang.

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


P.S. New links will be added in future if i spot something interesting and relevant.
