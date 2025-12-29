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
