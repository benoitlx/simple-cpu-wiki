An instruction set describe the [[Assembly]] instruction that your computer can understand. Your instruction set will describe how much your computer is [[Expressivity|expressive]] (ie. the richness of the language with whom you talk to your computer).

## Famous Instruction Set
- [[x86-64]] (Il y a un plugin obsidian pour afficher un graphe de flow (en tant que canvas) à partir d'un fichier assembleur)
- [[ARM]]
- [[RISC]]

## My Instruction Set v1

#todo tester la fréquence maximale d’exécution de chaque instruction

Instructions using A or B share homologue instruction with immediate left and right. These instruction are suffixed with the letter L or R or the combination.

Instructions with the (opt) symbols are optional for the project. It's a bonus if they are implemented.

### ALU

| Cycles | Instruction(8) A(8) B(8) R(8) | Description | Immediate |
| ------ | ----------------------------- | ----------- | --------- |
| 1      | ADD A B R                     | R <- A + B  | LR        |
| 1      | SUB A B R                     | R <- A - B  | LR        |
| 1      | AND A B R                     | R <- A & B  | LR        |
| 1      | OR A B R                      | R <- A \| B | LR        |
| 1      | NOT A _ R                     | R <- ~A     | L         |
| 1      | XOR A B R                     | R <- A ^ B  | LR        |
| 1      | SHIFT A B R (opt)             | R <- A >> B | LR        |
Maybe multiplication ???
### COND

| Cycles | Instruction(8) A(8) B(8) R(8) | Description                                                                                                                                                                                                                                                        | Immediate                  |
| ------ | ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------- |
| 1      | JMP Adr(16) _                 | Jump to the RAM address denoted by Adr (modify the PC to Adr)                                                                                                                                                                                                      | default for left and right |
| 2      |                               | complete conditional jump : see instructions below                                                                                                                                                                                                                 |                            |
| 1      | BEQ A B _                     | Test if A = B. IF true, do nothing, ELSE Jump over the next instruction (PC<-PC+2). If you want to jump to a certain address only when condition is met you must follow this instruction by a *JMP* instruction, it will then takes two cycle to do what you want. | LR                         |
| 1      | BNEQ A B _ (opt)              | Same with condition A != B                                                                                                                                                                                                                                         | LR                         |
| 1      | BLE A B _ (opt)               | Same with condition A <= B                                                                                                                                                                                                                                         | LR                         |
| 1      | BGE A B _ (opt)               | Same with condition A >= B                                                                                                                                                                                                                                         | LR                         |
| 1      | BL A B _ (opt)                | Same with condition A < B                                                                                                                                                                                                                                          | LR                         |
| 1      | BG A B _ (opt)                | Same with condition A > B                                                                                                                                                                                                                                          | LR                         |


### CTRL

Control the behavior of the computer

| Cycles   | Instruction(8) A(8) B(8) R(8) | Description                                                                           | Immediate | BitCode  |
| -------- | ----------------------------- | ------------------------------------------------------------------------------------- | --------- | -------- |
| 1        | NOP _ _ _                     | Do nothing for one cycle                                                              | None      | 00000000 |
| $\infty$ | HALT _ _ _ (opt)              | Stop the computer from running next instruction, maybe by cutting the clock to the PC | None      |          |
|          | INTER (opt)                   | #todo interruption                                                                    |           |          |

HINT for interruption:
INTER Adr(16) R
Where Adr is the address of the function to call when an interrupt is triggered
Where R indicate how an interrupt is triggered (eg: R -> XAAAAAAA X indicate wheter to detect when input goes from 0 to 1 or from 1 to 0 the A bits is a mask that indicate the pin in the input register to watch)

### Memory

The *RAM* and *STK* section share the memory with one block of ram also used to store the program and used by graphic card. You must be careful when addressing in this block not to overwrite things you don't want like the program section, the stack section or the frame buffer.

#### RAM

| Cycles | Instruction(8) A(8) B(8) R(8) | Description                                                                                                                                                                                                          | Immediate                  |
| ------ | ----------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------- |
| 4      | complete load from RAM        | see instruction below                                                                                                                                                                                                |                            |
| 1      | LDa Adr(16) R                 | Load data from RAM to R register. Because registers are only 8 bits, if you want to load the complete data stored in the RAM, you must write 4 instructions (LDa Adr reg1; LDb Adr reg2; LDc Adr reg3; LDd Adr reg4) | default for left and right |
| 4      | complete store in RAM         | see instruction below                                                                                                                                                                                                |                            |
| 1      | STa Adr(16) R                 | Store the data from register R to RAM. The same principle than LDa instruction is applied                                                                                                                            | default for left and right |


#### STK

#todo figure and explanation for functions

| Cycles | Instruction(8) A(8) B(8) R(8) | Description                                                                                            | Immediate                  |
| ------ | ----------------------------- | ------------------------------------------------------------------------------------------------------ | -------------------------- |
| 1      | SINIT Adr _ (opt)             | Store Adr in the stack pointer register (describe the beginning of the stack in memory)                | default for left and right |
| 1      | CALL Adr _ (opt)              | Push current address+1 (in the PC) into the stack and  jump to address Adr. Useful to call a function. | default for left and right |
| 2      | RET _ _ _ #todo (opt)         | Get N (the number of arguments) with a pop. Decrement the stack pointer by N. Pop into the PC          | None                       |
| 1      | PUSH A _ _ (opt)              | push value into the stack                                                                              | L                          |
| 1      | POP _ _ R (opt)               | pop value to register R                                                                                | None                       |

#todo make a program that check ROM of microcode in order to prevent close-circuit and duplicated instruction.


## My Instruction Set v2

The instructions are 16 bits wide. The bit 15 determines the instruction mode (Data or ALU).

The following registers are accessible :

- `A` which is also the RAM address
- `V` which is also the vRAM address
- `*A` which is the content of the RAM at address `A` accessible like any other register
- `*V` same for the vRAM
- `D` a temporary register

> [!tips] 
> Si le temps presse et que je veux quand même faire de la vidéo, je peux loger un frame buffer dans la RAM


### DATA Mode

The data Mode is enabled when the bit 15 of the instruction is set to 0.

In this mode the data given in the following 15 bits is directly sent to the A register. 

| Cycles | Instruction Pattern<br>Mode \[15\] Data \[14-0\] | Description |
| ------ | ------------------------------------------------ | ----------- |
| 1      | 0                X                               | A <- X      |
> [!note]
> With this technique it is only possible to transfer in one cycle numbers from 0 up to 32767 from the ROM into a register
### ALU Mode

The data Mode is enabled when the bit 15 of the instruction is set to 1.

In this mode an operation specified by the opcode will be calculated using the `srcA` and `srcB` register and will be stored into the `dest` register.

| Cycles | Instruction Pattern<br>- Mode \[15\]<br>- opcode/condition \[14-12\]<br>- not used \[11-9\]<br>- A \[8-6\]<br>- B \[5-3\]<br>- C \[2-0\] | Description                                                  |
| ------ | ---------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| 1      | 1 1$\diamond$ xx A B C                                                                                                                   | **Operation**<br>C <- A $\diamond$ B                         |
| 1      | 1 0$\star$ xx A B xxx                                                                                                                    | **Condition**<br>if $* \neq JMP$ PC <- PC + 1 if A $\star$ B |
The $\diamond$ operation is given by the following table :

| Code | Name | Description                               |
| ---- | ---- | ----------------------------------------- |
| 000  | ADD  | Addition                                  |
| 001  | SUB  | Substraction                              |
| 010  | AND  | logical AND                               |
| 011  | OR   | logical OR                                |
| 100  | XOR  | exclusive logical OR                      |
| 101  | NOT  | Complement (takes only the first operand) |
| 110  | SHL  | logical bitshift to the left              |
| 111  | SHR  | logical bitshift to the right             |
 The $\star$ relation is given by the following table : 

| Code<br>tl eq gt | Relation $\star$ |
| ---------------- | ---------------- |
| 000              | Never            |
| 001              | >                |
| 010              | =                |
| 011              | >=               |
| 100              | <                |
| 101              | !=               |
| 110              | <=               |
| 111              | Always           |

The different registers are indexed with this table : 

| Code | Register          |
| ---- | ----------------- |
| 000  | A                 |
| 001  | *A                |
| 010  | V                 |
| 011  | *V                |
| 100  | D                 |
| 101  | not addressed     |
| 110  | 0 (zero constant) |
| 111  | 1 (one constant)  |

#todo réfléchir si des exclusions sont nécessaire par exemple entre les registres `A` et `*A`

> [!note]
> With this implementation, performing a jump takes 2 cycles : one for loading the jump address into `A` and one to perform the jump.

#todo make a program that takes 16 bits and give a description of what the instruction is doing

#todo interrupt, no op, multiplication, division