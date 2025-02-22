> [!danger] FIXME
> - Some of the identifier used in an assembly program are declared only after in the program (e.g. `A = label\nlabel:`). My program crash because it tries to read in the Hashtable containing the identifiers but finds nothing.
> 	- Maybe I should iterate twice : one for the directive (equivalent of preprocessor in C) and one for the actual instructions > Even this solution is not ideal because to assign a value to each label identifier we need to know the actual address in the program ... 
> 	- I finally addressed this problem by letting the identifier string in the bit_stream and making a function `replace_identifier` that is called at the end of the parsing function
> - Instruction of the type `A = D` are making my program to crash because the parser is expecting a `Token::Operation` after `D`. The tricky part is that in order to detect this case we must read the token after `D`. Making the program to have unexpected behavior after that because the first token after `D` is already consumed.
> 	- A fix may be to reinsert the token we read at the beginning of our iterable but I doubt it is easily possible.
> 	- A cleaner approach might be to stop reading token after the sequence `Register Assignement Register` and let our main loop consume the remaining token. Of course we will need to match for `Token::Operation` in our main pattern matching structure. And we will need to partially accumulate the bitstream ...
> 	- > see `iter.peek()` (aka 'lookahead') even better: `multipeek()`
