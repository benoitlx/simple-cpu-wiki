The assembler is the program responsible for the transformation of assembly source code into machine code, by following the [[Instruction Set|specification]] of our cpu.

Like a compiler, an assembler is composed of two majors parts :
- The [[Lexer]]
- The [[Parser]] (in my implementation, the parser is responsible for transforming a list of tokens into a bit stream)

I implemented an assembler for my [[Instruction Set#My Instruction Set v2|second instruction set]] with the programming language [Rust](https://www.rust-lang.org/). All of the source code is available on my [github](https://github.com/benoitlx/simple-assembler).