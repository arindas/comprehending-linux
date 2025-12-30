# Kernel interfaces

At some point, we need to ask the question: why do we need an operating system
at all? To understand this, let's try to write programs directly in machine code
and assembly. The idea is to see if it's possible to write programs without using
any of the features provided by the operating system. This way it should become
clear which features the operating system provides for us and whether or not we
need them.

In this exercise, we will assume x86_64 CPU architecture. Since we are directly
targeting the hardware, it's necessary to specify this up front. But why?

Every CPU architecture (`x86_64`, arm64, RISCV) has a unique **Instruction Set
Architecture (ISA)** which is the set of instructions and the instruction
invocation conventions that the architecture supports. Now why is it necessary
for the ISA to be _unique_?

In order to understand this, we need a brief description of CPU architecture.

<!--

# Narrative script

- Pose the question whether it's possible to write programs without using
  OS features directly in assembly or machine code
- Specify that we will we working x86_64 ISA
- Pose the question why it's necessary to specify this?
- Describe CPU architecture, clearly explain how every CPU architecture has it's own ISA
- Go back to x86_64 ISA, refer to IA64 specification to know about:
    - Basic execution environment
    - Register
    - Instruction format
    - Available opcodes

- Introduce witing code in machine code first. Use the hex tables in the manaal to know how to encode registers and opcodes
- This first program simply MOVs a value into a register, show how to link it and run it on a bare metal target
- Next do the same in assembly. Explain how the assembler translates the assembly into machine code for us
- Bring up the lack of any visible side effects. Suggest introducing basic I/O by printing "Gello world"
-




-->
