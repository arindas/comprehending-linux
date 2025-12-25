# Introduction

<!-- TODO: rewrite with better prose -->

Goals:

- Form a mental model of the different kernel subsystems and their API boundaries
- Present optimal topologically sorted sequence of source files to read
- Deeply understand the internals of the Linux kernel
- Empower the reader to explore, debug, trace and extend the Linux kernel

### What's in the book

Here are the topics we are going to cover:

- Booting
  - bios
  - bootloader
  - protected mode, real mode
- Process management
  - process repr
  - init process
  - syscalls
  - kernel mode, user mode, context-switch
  - traps, signals, interrupts
  - scheduler
    - eevdf
    - cfs
    - preempt_rt
- Memory management
- Debugging
  - perf
  - ptrace
  - ftrace
  - kprobe
- File systems
  - vfs
  - ext4
- I/O
  - open, read, select, poll, epoll
  - io_uring
- networking
  - UDP + TCP / IP stack
- eBPF
  - xdp, tc, kprobe, uprobe, tracepoint
  - AF_XDP
  - sched_ext
- Linux kernel module development
- device drivers
  - character block devices
  - graphics drivers
  - custom driver from custom hardware
