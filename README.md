# Erlang-Internals-Info

## General

1. Not specifically related but I guess official Erlang Efficiency Guide is the most important piece of information:
http://erlang.org/doc/efficiency_guide/introduction.html
Implementation-specific details of how much memory native Erlang data types occupy from the same guide:
http://erlang.org/doc/efficiency_guide/advanced.html#id71365

2. Evolution of the Erlang VM presentation:
http://www.erlang-factory.com/upload/presentations/247/erlang_vm_1.pdf

3. How Erlang does scheduling
http://jlouisramblings.blogspot.ch/2013/01/how-erlang-does-scheduling.html

4. A History of Erlang by Joe Armstrong
http://webcem01.cem.itesm.mx:8005/erlang/cd/downloads/hopl_erlang.pdf
Nice article by one of the original creators describing the evoultion of Erlang.

## BEAM (Bogdan/Björn's Erlang Abstract Machine) and ERTS (Erlang RunTime System):

1. BEAM book
Very good book which covers almost all the aspects of the Erlang RunTime System and BEAM subsystems:
https://happi.github.io/theBeamBook/
A very interesting part from this book about BEAM loader and transformation from generic to specific instructions:
https://happi.github.io/theBeamBook/#CH-Beam_loader
Basically the Erlang bytecode loader does a large amount of work rewriting the generic "transport format" bytecode in the object files into the concrete internal bytecode operations that are actually executed. This recognizes common sequences and replaces them with optimized single-opcode versions. 

2. A peak into the Erlang compiler and BEAM bytecode. 
Overview of all the intermediate representations of Erlang code:
http://gomoripeti.github.io/beam_by_example/

Erlang source code --> Abstract Syntax Tree ('P') --> expanded AST ('E') --> Core Erlang ('to_core') --> BEAM byte-code
Although I would also add a step BEAM generic bytecode --> BEAM specific bytecode and Core Erlang --> Kernel Erlang.

So to sum up, the main compilation stages for Erlang are:
  - preprocessing (macros, conditional compilation, includes)
  - source-level expansions, such as records to tuples
  - translation to Core Erlang + optimizations and optional inlining
  - translation to "Kernel Erlang" + pattern matching compilation
    and further optimizations
  - translation to Beam assembler + some more optimizations
  - encoding as Beam bytecode

3. SO question with some details about the Erlang pattern match compiler
https://stackoverflow.com/questions/587996/erlang-beam-bytecode

4. Source code of the Kernel Erlang compiler (v3_kernel) with useful comments:
https://github.com/erlang/otp/blob/master/lib/compiler/src/v3_kernel.erl#L20

5. A nice thread descibing languages compiled to BEAM bytecode via EAF (in the context of Elixir).
https://elixirforum.com/t/getting-each-stage-of-elixirs-compilation-all-the-way-to-the-beam-bytecode/1873

6. Erlang `compile` module docs with useful info:
http://erlang.org/doc/man/compile.html#file-2

7. BEAM file format doc describing all the chunks:
http://rnyingma.synrc.com/publications/cat/Functional%20Languages/Erlang/BEAM.pdf

8. Erlang abstract format docs:
http://erlang.org/doc/apps/erts/absform.html

9. The Erlang BEAM Virtual Machine Specification by one of its designers (outadated but still useful):
http://www.cs-lab.org/historical_beam_instruction_set.html

10. A Peek Inside the Erlang Compiler:
http://prog21.dadgum.com/127.html

11. BEAM wisdoms:
BEAM file format:
http://beam-wisdoms.clau.se/en/latest/indepth-beam-file.html#
And instructions
http://beam-wisdoms.clau.se/en/latest/indepth-beam-instructions.html#

12. Also a nice description of the BEAM instruction set:
http://erlangonxen.org/more/beam

13. Thesis which contains some useful information about the Erlang implementation
http://uu.diva-portal.org/smash/get/diva2:428121/FULLTEXT01.pdf

14. Code of the BEAM loader which was described above:
https://github.com/erlang/otp/blob/master/erts/emulator/beam/beam_load.c
You might be particularly interested in the `static int transform_engine(LoaderState* st)` function.

