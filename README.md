# Mips Based Architecture
This project consists in a work from the **Computer Organization and Architecture's** subject of the **Computer Science's** degree at the **Federal University of Technology Parana (UTFPR-CM)**.
The objective of this work is to implement a MIPS based architecture, using the *Logisim* software. 

For starters, let's have a look at the Datapath of the architecture and its components:
![Datapath](Datapath%20Image.png) 

The components of the Datapath are:
- **PC**: Program Counter.
- **Instruction Memory**: Memory that stores the instructions.
- **Registers**: 32 bits memory where the data is stored and can be manipulated. 
- **ALU**: Arithmetic Logic Unit.
- **Data Memory**: Memory that stores the data.
- **Control Unit**: Unit that controls the flow of the data.
- **Signal Extender**: Unit that extends the signal of a 16 bits immediate to 32 bits.
- **Multiplexers**: Unit that selects the input to be sent to the output.
- **Shift << 2 concatenator**: Unit that shifts the address to the left by 2 bits and concatenates it with the PC address + 4.
- **Decoder**: Unit that decodes the instruction that came from the Instruction Memory.
- **Alu Control**: Unit that controls the ALU operation.
- **Register's bank**: Unit that stores the main registers of the architecture.
- **Exception Control Unit**: Unit that controls the exceptions of the architecture.

---

## Program Counter (PC):
The PC is a 32 bits register that stores the address of the next instruction to be executed. It is incremented by 4 in each cycle, so it can point to the next instruction. It can also be changed by the Jump and Branch instructions or if an exception occurs.

## Instruction Memory:
The Instruction Memory is a ROM memory that stores the instructions of the program. It is a 32 bits memory, where each instruction is stored in a 32 bits address. The instruction memory is read in each cycle, and the instruction that is stored in the address pointed by the PC is sent to the Decoder. Due to **limitations** of the logisim software, the memories that it provides have only 24 bits of address, so to deal with this problem, we used two memories, where the 2nd memory would only be selected at the 25th bit, in this way, we can store the instructions in a number bigger than 24 bits addresses. One thing that is important to mention is that we only are able to walk through the memory in steps of 4, because of a shift of 2 bits. We can't have all the 32 bits of address, unless we use another memory for each remaining bit of address, but this would be very inefficient.

![Instruction Memory](/Images/Instruction%20Memory.png)

## Decoder:
The Decoder is a unit that decodes the instruction that came from the Instruction Memory. It is a combinational circuit that receives the instruction and outputs the control signals that will be used by the Control Unit. With the decoder we can know if an instruction is a R-Type, I-Type or J-Type, and also what operation it is. The decoder also outputs the registers that will be used by the instruction, the immediate and the address of the jump instruction. Basicaly it takes the instruction and outputs the data that will be used by the other units of the architecture, its outputs are:
- **Opcode**: The operation code of the instruction.
- **Rs**: The first register that will be used by the instruction.
- **Rt**: The second register that will be used by the instruction, can be used as a destination register.
- **Rd**: The third register that will be used by the instruction generally used as a destination register.
- **Shamt**: The shift amount of the instruction.
- **Funct**: The function code of the instruction.
- **Imm**: The immediate of the instruction.
- **Addr**: The address of the jump instruction.

![Decoder External](/Images/Decoder%20External.png)
![Decoder Internal](/Images/Decoder.png)


## Registers' Bank:
The Register's Bank contains all the main registers used by the architecture, it has 5 inputs and 2 outputs. The inputs are:
- LR1: The first register that will be read (Rs).
- LR2: The second register that will be read (Rt).
- WR: The register that will be written (Rd). In instructions that aren't R-Type, sometimes Rt is used.
- WD: The data that will be written in the register.
- RegWrite: A control signal that indicates if the register will be written or not.

For the outputs, we have:
- RD1: The data that is stored in the first register.
- RD2: The data that is stored in the second register.

As for the register, we have 32 registers, each one with 32 bits. The registers are numbered from 0 to 31, and the register **\$0** or **$zero** will always be 0.  

| Register     | Number | Register | Number | Register | Number | Register | Number |
| --------     | ------ | -------- | ------ | -------- | ------ | -------- | ------ |
| **$zero**    | 0      | **$t0**  | 8      | **$s0**  | 16     | **$t8**  | 24     |
| **$at**      | 1      | **$t1**  | 9      | **$s1**  | 17     | **$t9**  | 25     |
| **$v0**      | 2      | **$t2**  | 10     | **$s2**  | 18     | **$k0**  | 26     |
| **$v1**      | 3      | **$t3**  | 11     | **$s3**  | 19     | **$k1**  | 27     |
| **$a0**      | 4      | **$t4**  | 12     | **$s4**  | 20     | **$gp**  | 28     |
| **$a1**      | 5      | **$t5**  | 13     | **$s5**  | 21     | **$sp**  | 29     |
| **$a2**      | 6      | **$t6**  | 14     | **$s6**  | 22     | **$fp**  | 30     |
| **$a3**      | 7      | **$t7**  | 15     | **$s7**  | 23     | **$ra**  | 31     |