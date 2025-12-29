# Kernel interfaces

At some point, we need to ask the question: why do we need an operating system
at all? To understand this, let's try to write the classic "Hello World" example
without using any high-level programming languages.

In this exercise, we will assume x86_64 CPU architecture. Since we are directly
targeting the hardware, it's necessary to specify this up front. But why?

Every CPU architecture (`x86_64`, arm64, RISCV) has a unique **Instruction Set
Architecture (ISA)** which is the set of instructions and the instruction
invocation conventions that the architecture supports. Now why is it necessary
for the ISA to be _unique_?

## A Brief description of CPU architecture

At its heart, every CPU is a digital circuit. It relies on binary inputs and
binary outputs. To move these binary signals, the CPU interfaces with the System
Bus. The Control Unit places the target address on the Address Bus, issues
commands on the Control Bus, and receives the binary code via the Data Bus.

So whenever we need the CPU to:

- Perform some arithmetic operation with its Arithmetic Logical Unit or,
- Perform Memory Read Write or move data between registers,

We need to pass in a specific binary coded instruction that the digital circuit
uses as the input to activate the relevant digital circuit components to perform
the side effects.

A CPU is a synchronous sequential digital circuit with combinatorial components
constructed from these four core pillars:

### The Clock

The Clock is the heartbeat of the CPU. It provides a continuous, oscillating
electrical signal that alternates between low (0) and high (1) voltage states.
It is what makes the CPU a synchronous circuit. The speed of this transition is
the clock frequency (measured in GHz).

It coordinates the timing of all operations. Internal components like registers
only update their data on a specific edge of the clock signal (usually the
rising edge). This ensures that data is stable before it is processed or stored,
preventing race conditions where signals might arrive at different times.

### Registers

Registers are the fastest form of memory in the computer, physically located
within the CPU die. They are constructed from sequential logic circuits
(flip-flops) that hold binary data as long as power is applied.

- General Purpose Registers (GPRs): In x86_64, these are 64-bit wide storage
  locations (like RAX, RBX, RCX) used to hold variables and temporary results
  during calculations.
- Special Purpose Registers: These track the machine's state. Key examples
  include the Instruction Pointer (RIP) which tracks the current code location,
  and the Stack Pointer (RSP) which manages function call memory.

### The Arithmetic Logic Unit

The ALU is a purely combinatorial circuit. It performs the actual data
processing. It has no internal memory of past operations; its output is
determined solely by its current inputs.

- Arithmetic: Performs integer addition, subtraction, multiplication, and
  division.
- Logic: Performs bitwise operations like AND, OR, XOR, and NOT.
- Interaction: When the ALU computes a result (e.g., subtracting two numbers),
  it simultaneously updates the Status Register (RFLAGS) to reflect the outcome
  (e.g., setting the Zero Flag if the result was 0).

### The Control Unit

The control unit is a sequential circuit which relies on the Clock to act as a
Finite State Machine to go through the Fetch → Decode → Execute cycle:

- Fetch: Fetch the next instruction binary code. The CPU's Bus Interface Unit
  (BIU) orchestrates this process. It first checks the high-speed L1 Instruction
  Cache. If the data is not found, it cascades the check to the L2 and L3
  Caches. If a Cache Miss occurs at all levels, the CPU must retrieve the data
  from Main Memory (RAM). This triggers a physical hardware transaction across
  the System Bus, which is actually composed of three distinct sets of wires
  working in unison:

  - Addressing: The BIU reads the 64-bit address currently held in the
    Instruction Pointer (RIP). It places this specific binary address onto the
    Address Bus. This is a unidirectional channel that electrically signals the
    Memory Controller exactly where to look in the physical RAM grids.
  - Signaling: Simultaneously, the Control Unit asserts the Memory Read (MEMR)
    signal on the Control Bus. This line goes "active" (high or low voltage
    depending on the circuit logic) to tell the system memory that this is a
    retrieval operation, not a write operation. If the RAM is slower than the
    CPU, the memory controller may use the Control Bus to send a WAIT signal,
    forcing the CPU to pause (insert wait states) until the data is ready.
  - Transfer: Once the RAM locates the requested data, it places the binary
    values onto the Data Bus. Unlike the Address Bus, this is bidirectional. The
    memory usually sends a full Cache Line (often 64 bytes) rather than just the
    single instruction, filling the caches for future efficiency. The CPU senses
    the voltage changes on these wires, latches the data, and moves it into the
    Instruction Register (IR) or the Instruction Queue to await decoding.

  > The size of the memory address is what
  > determines the 64-bit in 64-bit architecture. So a 64-bit architecture uses
  > 64-bit wide memory addresses. A 32-bit architecture used 32-bit wide memory
  > addresses.
  >
  > Every memory address points to a byte of memory.
  >
  > A highest possible value of an unsigned 32-bit value is:
  >
  > ```
  > pow(2, 32)
  > = pow(2, 2) * pow(2, 10) * pow(2, 10) * pow(2, 10)
  > = pow(2, 2) * pow(2, 10) * pow(2, 10) * 1K
  > = pow(2, 2) * pow(2, 10) * 1M
  > = pow(2, 2) * 1G = 4 * 1G = 4G
  >
  > Every memory address points to 1 byte i.e 1B.
  >
  > So max memory addressable by 32-bit architecture = 4G * 1B = 4GB
  >
  > ```
  >
  > This is why 32-bit systems could not traditonally use more than 4GB or memory
  > since this is a mathematical limit. That said, specific hardware features
  > like [Physical Address
  > Extension](https://en.wikipedia.org/wiki/Physical_Address_Extension)
  > alleviated this to a certain extent.

- Decode: Decode the instruction and examine the Status Register. This register,
  known as RFLAGS on x86_64, holds the current state of the processor. Key flags
  include:

  - Zero Flag for equality
  - Sign Flag for negative results
  - Carry Flag for unsigned arithmetic errors,
  - Overflow Flag for signed arithmetic errors
  - Parity Flag for error checking
  - Direction Flag for string processing order
  - Interrupt Flag which controls response to external hardware signals.

- Execute: Activate the specific circuits to perform the operation.
  - If the instruction is a conditional branch,
    - the control unit uses these flag values to determine if the condition is
      met.
    - If true, it updates the Instruction Pointer to the new target address.
    - If false, it ignores the jump and allows the Instruction Pointer to
      increment to the next sequential instruction

### Modern x86_64 Optimizations

Modern processors expand on this foundation to maximize efficiency. They do not
wait for one instruction to finish completely before starting the next. Instead,
they use [Instruction
Pipelining](https://en.wikipedia.org/wiki/Instruction_pipelining) to treat the
Fetch, Decode, and Execute cycle like a factory assembly line. While one
instruction is being executed, the next is already being decoded, and a third is
being fetched.

#### The Standard 5-Stage Pipeline Model

To visualize how pipelining improves throughput, computer scientists often use a
generic 5-stage model. In a non-pipelined CPU, Instruction 2 cannot begin until
Instruction 1 completes all five stages (taking 5 clock cycles). In a pipelined
CPU, a new instruction enters the "assembly line" every clock cycle. The
important thing to note is, every pipeline stage can function independently
as long it has the necessary data to operate on.

**Pipeline Execution Table**

In the table below, notice how at **Clock Cycle 5**, the hardware is fully
saturated—five different instructions are being processed simultaneously in
different stages.

| Instruction | Cycle 1 | Cycle 2 | Cycle 3 | Cycle 4 | Cycle 5 | Cycle 6 | Cycle 7 | Cycle 8 | Cycle 9 |
| :---------- | :-----: | :-----: | :-----: | :-----: | :-----: | :-----: | :-----: | :-----: | :-----: |
| **Instr 1** | **IF**  |   ID    |   EX    |   MEM   |   WB    |         |         |         |         |
| **Instr 2** |         | **IF**  |   ID    |   EX    |   MEM   |   WB    |         |         |         |
| **Instr 3** |         |         | **IF**  |   ID    |   EX    |   MEM   |   WB    |         |         |
| **Instr 4** |         |         |         | **IF**  |   ID    |   EX    |   MEM   |   WB    |         |
| **Instr 5** |         |         |         |         | **IF**  |   ID    |   EX    |   MEM   |   WB    |

**The 5 Pipeline Stages**

1. **Instruction Fetch (IF):**
   The CPU reads the instruction from the L1 Instruction Cache using the address
   in the Instruction Pointer (RIP). The IP is incremented to point to the next
   instruction.

2. **Instruction Decode (ID):**
   The Control Unit interprets the opcode bits to determine which circuits are
   needed. Simultaneously, the input operands are read from the Register File
   (e.g., reading values from `RAX` or `RBX`).

3. **Execute (EX):**
   The ALU performs the actual operation. This could be an arithmetic
   calculation (add, subtract) or calculating the effective address for a memory
   access (e.g., calculating `base + offset`).

4. **Memory Access (MEM):**
   If the instruction involves system memory (like a `MOV` instruction that
   reads from RAM), the data is read from or written to the data cache/memory
   during this phase. Arithmetic-only instructions effectively skip this step
   (idle).

5. **Write Back (WB):**
   The final result of the operation (from the ALU) or the data retrieved from
   memory is written back into the destination register in the Register File,
   making it available for future instructions.

These processors are also Superscalar:

- The CPU contains multiple execution units to retire multiple instructions per
  clock cycle.
- To support this, the hardware uses [Register
  Renaming](https://en.wikipedia.org/wiki/Register_renaming) to map
  architectural registers like RAX to a larger pool of physical registers,
  removing false dependencies.
- Furthermore, [Out of Order
  Execution](https://en.wikipedia.org/wiki/Out-of-order_execution#Basic_concept)
  allows the CPU to process instructions as soon as their inputs are ready
  rather than waiting for previous unrelated tasks to finish.
- A [Reorder
  Buffer](https://cseweb.ucsd.edu/classes/fa14/cse240A-a/pdf/07/CSE240A-MBT-L13-ReorderBuffer.ppt.pdf)
  then ensures results are committed in the original program order.
- Finally, [Branch Prediction](https://en.wikipedia.org/wiki/Branch_predictor)
  logic guesses which path the code will take and speculatively executes
  instructions before the flags are even calculated to keep the pipeline full.

> I recommend the following books for a deeper understanding (bottom-up sequence):
>
> - Digital Design and Computer Architecture (David Money Harris and Sarah L. Harris)
>   - Goes into detail about the CPU is implemented at the digital circuit
>     level, down to the logic gates and transistors.
> - Computer Architecture: A Quantitative Approach (Hennessy, Patterson)
>   - Goes into detail about pipelining, data dependencies and hazards,
>     dynamic scheduling, branch prediction, Tomasulo's algorithm
> - Computer Organization and Design: The Hardware Software Interface (Hennessy, Patterson)
>   - Provides an overview of the Pipelining process
> - Computer Systems a Programmer's Perspective (Randal E. Bryant, David R. O’Hallaron)
>   - Compares high level
>     C and assembly side by side and describes the execution process in detail

From this we can conclude that the following is unique for every architecture:

- The specific operations supported and their specific binary encoding i.e the
  _opcodes_
- The specific registers used in the architecture
- Architecture specific digital circuit implementation
- Architecture specific optimizations

Due to the above reasons, every architecture has it's own set of instructions,
instruction invocation conventions and thereby it's own ISA.

That said there are modern open standardized ISAs like RISCV which can then have
different implementations at the digital circuit level.

We can therefore, think of ISA as the **API interface** that an architecture
exposes for being programmed with.
