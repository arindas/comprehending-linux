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
- Go back to x86_64 ISA, refer to x86_64 specification to know about:
    - Basic execution environment
    - Register
    - Instruction format
    - Available opcodes

- Introduce witing code in machine code first. Use the hex tables in the manual
  to know how to encode registers and opcodes
- This first program simply MOVs a value into a register, show how to link it
  and run it on a bare metal target
- Next do the same in assembly. Explain how the assembler translates the
  assembly into machine code for us
- Bring up the lack of any visible side effects. Suggest introducing basic
  I/O by printing "Gello world"
- Introduce Port I/O and Memory mapped I/O from manual
- Write basic "Hello world!" using COM port on QEMU using Port I/O. Explain priviledge levels.

- Introduce transition to 64bit mode for the "Hello World!" program
- Introduce basic multiprocessor program
    - Perpare a new processor in real mode and places a trampoline to jump to
      64bit mode. Write "Hello" from first processor and "World" from second.
    - Explain how every processor has it's own set of registers
    - Refactor shared code into a function and use the call instruction so
      that it uses RSP. Show how each processor can call the same function
      with their own RSPs
    - Intrdouce synchronization by implementing a spin lock with atomic instructions

- Now make the user realize how impractical it is to write programs like
  this professionally in 2026. How even a simple program like netcat is
  notoriously difficult as we also have to treat the network cards like MMIO
  device and then implement ARP, IP routing and TCP handshakes essentially
  Layer1 to Layer4 of the OSI layer.

- Write the hello world program in assembly using the write syscall. Bring up
  priviledge levels again and how the kernel triggers a transition to ring 0 with
  a mode switch during the context switch. Stay light on the mechanisms of the
  context switch for now, we will revisit during process management.

- Port it to C by exposing the syscalls as C functions from assembly by
  declaring them in C and linking them with assembly definstions.

- Write our multi processor program using the clone and write syscalls.
  Show how synchronization is handled by the kernel itself.

- Write a very simple version of netcat in C by exposing socket, bind,
  listen, accept syscalls from assembly.

- Show how we are using only syscall but no libraries. Not even glibc.

- Explain how the linux kernel presents an abstraction over the hardware
  resources by representing resources handles as file descriptors and
  syscalls to operate on these resources by passing file descriptors to
  these syscalls.

- Explain how syscalls and file descriptors managed by the operating system
  provide a higher level abstraction to program a computer just like the ISA
  provies a higher level abstraction over the underlying digital circuits
  in the CPU and peripheral devices.

- Finally exaplin the role of the operating system as an abstration over the
  underlying hardware for managing access and control to the hardware resources.


-->
