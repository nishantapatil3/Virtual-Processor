Overview:
    Our program reads in assembly code from a text file, decodes the assembly instruction into
    machine code and stores it into the Instruction Memory portion of Memory. Once we are finished
    reading in the assembly code, our program begins execution. It pulls the first instruction from
    memory location 1024, executes it, and will read each instruction until the “EXIT” opcode is
    reached. If the “EXIT” code is read from Instruction Memory, the program will not fetch the next
    instruction and exit the program.

Reading In Assembly Code:
    We have implemented a functionality which reads instructions from a file named “inputFile.txt”
    and decodes these instructions and stores the opcodes into the instruction memory starting
    from 1024.
    The file “inputFile.txt” contains a series of instructions, each on a new line. The program then
    reads this file and inserts the respective opcodes into the instruction memory.
    When MFA/MTA instructions are read from the file, we perform memory addressing to fetch the
    memory location as below-
    D(Rb,Ri,S) → Mem[Reg[Rb]+S*Reg[Ri]+D]
    D - Constant displacement (1, 2, 4 bytes)
    Rb - Base register (any of 16 integer registers)
    Ri - Index register (any of 16 integer registers, except %rsp)
    S - Scale (1, 2, 4, or 8)
    We define,
    MFA <complete memory address>
    MTA <complete memory address>
    The above 2 instructions considers the memory location as the 1st operand and the
    accumulator as the 2nd operand. Hence, moving the contents from the memory location to the
    Accumulator in case of MTA or moving the contents from the Accumulator to memory location in
    case of MFA.
    Ex: MTA 0xff(R1,R3,2)
    MFA 0xfe(R3,R2,4)
    MTA 0xff(R1,R3,2)
    Consider, R1=0x3, R2=0x5
    0xff + R1 + R3 + 0x2 = 10C
    → Updates memory location: 0x10C by Accumulator value
    MFA 0xfe(R3,R2,4)
    Consider, R2=0xA, R3=0x1
    0xfe + R1 + R3 + 0x2 = 127
    → Updates Accumulator by value at memory location: 0x127
    StringMemoryAddressing() → Converts complete memory addressing string array into individual
    tokens which then converts Immediate addressing into integers and Register string into register
    values.
    StringRegistersToInt() → Converts register string into register value in int and then passed on to
    convert memory addressing into memory locations.
    MemoryAddressing() → Resolves int inputs of D,Rb,Ri,S into int memory location for further
    functional applications.
    Special Cases:
    (Rb,Ri) Mem[Reg[Rb]+Reg[Ri]] (R1,R3)
    D(Rb,Ri) Mem[Reg[Rb]+Reg[Ri]+D] 0xAB(R1,R3)
    (Rb,Ri,S) Mem[Reg[Rb]+S*Reg[Ri]] (R1,R3,4)
    Examples
    0x8(%rdx) 0xf000 + 0x8 0xf008
    (%rdx,%rcx) 0xf000 + 0x100 0xf100
    (%rdx,%rcx,4) 0xf000 + 4*0x100 0xf400
    0x80(,%rdx,2) 2*0xf000 + 0x80 0x1e080
    Reading Instructions From Memory:
    getCurrentInstruction() → Reads in 32-bit Instruction from memory, stores into currentInstruction
    global variable.
    parseInstructionFromMemory() → Separates 32-bit Instruction into 8-bit operation, and three
    8-bit operands.
    decodeInstructionOperationOperand() → Decodes each operation and operand into its
    appropriate operation and register value.
    callAppropriateFunction() → Calls function based on operation value.
    Once the assembly instructions have been read in from the text file, decoded into their opcodes,
    and stored into memory, the program will begin.
    We have a default program start set to memory location 1024. That is, every instruction will start
    from memory location 1024.
    Starting from memory location 1024, we use the getCurrentInstruction function to read in the
    entire instruction to be used. It reads the 32-bit instruction and stores the value into a global
    currentInstruction variable. After we have the entire 32-bit instruction, we need to separate it into
    its 8-bit operation and three 8-bit operand values.
    To do this, we call the parseInstructionFromMemory function. Based on our current Instruction
    Set, every Instruction will include an 8-bit operation, followed by three 8-bit operands labeled
    operand1, operand2, and operand 3 respectively.
    After getting the operation and operand opcodes from the currentInstruction, we use the
    decodeInstructionOperationOperand function, to decode the operation/operand and determine
    which operation and operand they correspond to. We do this by checking, bit by bit, the
    operation/opcode, with their defined value. If all bits match, then we have a match and that is
    the corresponding operation/register.
    Finally, once we have the decoded operation/operand values, we know which function to call.
    We use the callAppropriateFunction function to do this. This function will check the operation
    value and call the function that matches that value.
    After the instruction has been read from memory, parsed into its operation/operand, decoded,
    and executed, the next instruction will be called. Every instruction in our architecture is defined
    to be 32-bits long. With our memory having a width of 8-bits, each instruction will take up four
    rows in memory. This will always be a consistent value. As such, we know that the next
    instruction will be at location (current instruction plus 4).
    In order for our program to know when to stop reading from Instruction Memory, we created an
    “EXIT” instruction. This instruction is added to the end of the assembly program and has an
    opcode of 10101010. Therefore, our program will read every instruction from memory until it has
    reached/matched the 10101010 EXIT opcode. Once this opcode has been fetched and
    decoded, an exit flag will be set and the program will no longer fetch additional instructions.

ALU Functions:
    Flag Bits: We have included the following flags in our CPU architecture - Sign Flag, Overflow
    Flag, Zero Flag, Carry Flag.
    SUB Function: Subtract function - Takes three register numbers as input (z,x,y) and subtract 'x'
    register and 'y' register and stores the result in 'z' register. It just takes 2’s complement of the the
    content of ‘y’ register and adds it to the content of ‘x’ register
    ADD Function: Addition function - Takes three register numbers as input (z,x,y) and addition 'x'
    register and 'y' register and stores the result in 'z' register. It does the normal addition of two
    binary bit stream bit by bit using bitwise operations
    MUL Function: Multiply function - Takes three register numbers as input (z,x,y) and multiply 'x'
    register and 'y' register and stores the result in 'z' register. To multiply ‘x’ and ‘y’, It adds ‘x’, ‘y’
    times and stores it to ‘z’ register.
    DIV Function: Divition function - Takes three register numbers as input (z,x,y) and divides 'x'
    register and 'y' register and stores the result in 'z' register. To divide ‘x’ by ‘y’, It counts how
    many times we can subtract ‘x’ from ‘y’ and stores it to ‘z’ register.
    MOD Function: Modular function - Takes three register numbers as input (z,x,y) and take
    Modulos between 'x' register and 'y' register and stores the result in 'z' register. To divide ‘x’ by
    ‘y’, It counts how many times we can subtract ‘x’ from ‘y’ and at the end whatever is the content
    of ‘x’ register is stores it to ‘z’ register.

CPU Overview:
    1) Number of 32 bit registers – 16 (R0 – R15) General Purpose
    16 32-bit general purpose registers.
    2) Accumulator 32 bit – 1
    Accumulator is a 32-bit register. It will store the output of the ALU.
    3) Memory 4096 locations (rows) each with 8 bit
      1. 0-2047 (reserved for Instruction set, Stack, Heap)
        a. 0 - 1023 (Bootstrap)
        b. 1024 - 2047 (Instruction Memory)
      2. 2048-4096 (Data Memory)
      3. Memory total 4KB
    4) Flag – 4 bits
      Carry, Overflow, Zero, and Sign.
    5) Program Counter
      Register that contains the address of the instruction to be executed next.
    6) Memory Address Register
      This register will contain the address of an instruction or it will store the address of data.
    7) Memory Data Register
      Register containing data.

LDA and STA: 
    The two opcodes that we implemented are LDA and STA. LDA will accept a 32-bit hexadecimal
    value (memory address) and copy the values in that memory location to the accumulator
    register. STA (store) will accept a 32-bit address and copy the values in the accumulator into the
    memory location.

Various other functions were created to print out the register and memory contents, converting
binary to decimal, and hex to decimal.
First, we use the store command STA 00000800 to store the value within accumulator,
FF01FF01, to address location 800. Next, we send the STA 07FA to store FF01FF01 to address
07FA. Last, we send the LDA 07FE to store the values in memory location 07FE, 07FF, 800,
and 801 and store them into accumulator. Final value in accumulator is 0000FF01. There is also
complex error checking within our code to verify valid commands/addresses.

Sample Code in the assembly level language:
  1.
    MFA 0x429(R14,R15,2)
    
    MTA 0x429(R14,R15,2)
    
    ADD R5,R1,R2
    
    SUB R6,R4,R3
    
    MUL R7,R3,R2
    
    DIV R8,R4,R2
    
    MOD R4,R5,R6
    
    MOV R10,R7,R2
    
    EXIT
 
