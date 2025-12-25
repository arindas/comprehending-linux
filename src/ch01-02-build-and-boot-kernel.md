# Build and boot kernel

## Build kernel from source

From inside the kernel source directory:

```bash
make menuconfig
```

This should open up a terminal dialog menu:

<p align="center">
<img src="./img/linux-menuconfig.png" alt="linux-source-tree"/>
</p>

Take your time to explore this menu. Once you are done, exit without saving.
We will investigate these options in detail in subsequent sections.

Next, we will use the default `x86_64` config:

```bash
make x86_64_defconfig
```

> For other architectures, look for `${arch}_defconfig`

We will also enable kvm_guest.config since we intend to run it inside QEMU with KVM acceleration.

```bash
make kvm_guest.config
```

Now build the kernel:

```bash
make -j$(nproc)
```

> The `-j` flag specifies the number of parallel jobs to use for running make tasks.
> `nproc` outputs the number of processors on a system. So `-j$(nproc)` means use as
> many parallel jobs as the number of processors.
>
> In practice, if you are on a desktop, you probably don't want to go upto 100% CPU
> <usage on all cores. You can manually specify a lower number. For instance on a
> 16 core system I use -j12 to keep 4 cores free.

This should produce a kernel image:

```
Kernel: arch/x86/boot/bzImage is ready  (#1)
```

## Kernel boot attempt #0

Let's run this kernel image with QEMU:

```bash
popd

qemu-system-x86_64 \
  -kernel linux-${VERSION}/arch/x86/boot/bzImage \
  -append "console=ttyS0" \
  -nographic
```

<details>
<summary>
Kernel Log on boot in QEMU (expand to see full)
</summary>

```
SeaBIOS (version 1.15.0-1)


iPXE (https://ipxe.org) 00:03.0 CA00 PCI2.10 PnP PMM+07F8B340+07ECB340 CA00



Booting from ROM..
[    0.000000] Linux version 6.18.2 (arindas@arubox) (gcc (Ubuntu 11.4.0-1ubuntu1~22.04.2) 11.4.0, GNU ld (GNU Binutils for Ubuntu) 2.38) #1 SMP PREEMPT_DYNAMIC Thu Dec 25 20:41:215
[    0.000000] Command line: console=ttyS0
[    0.000000] BIOS-provided physical RAM map:
[    0.000000] BIOS-e820: [mem 0x0000000000000000-0x000000000009fbff] usable
[    0.000000] BIOS-e820: [mem 0x000000000009fc00-0x000000000009ffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000000f0000-0x00000000000fffff] reserved
[    0.000000] BIOS-e820: [mem 0x0000000000100000-0x0000000007fdffff] usable
[    0.000000] BIOS-e820: [mem 0x0000000007fe0000-0x0000000007ffffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fffc0000-0x00000000ffffffff] reserved
[    0.000000] NX (Execute Disable) protection: active
[    0.000000] APIC: Static calls initialized
[    0.000000] SMBIOS 2.8 present.
[    0.000000] DMI: QEMU Standard PC (i440FX + PIIX, 1996), BIOS 1.15.0-1 04/01/2014
[    0.000000] DMI: Memory slots populated: 1/1
[    0.000000] tsc: Fast TSC calibration using PIT
[    0.000000] tsc: Detected 2950.459 MHz processor
[    0.007744] last_pfn = 0x7fe0 max_arch_pfn = 0x400000000
[    0.008237] MTRR map: 4 entries (3 fixed + 1 variable; max 19), built from 8 variable MTRRs
[    0.008390] x86/PAT: Configuration [0-7]: WB  WC  UC- UC  WB  WP  UC- WT
[    0.018021] found SMP MP-table at [mem 0x000f5ba0-0x000f5baf]
[    0.022031] ACPI: Early table checksum verification disabled
[    0.022330] ACPI: RSDP 0x00000000000F59E0 000014 (v00 BOCHS )
[    0.023796] ACPI: RSDT 0x0000000007FE1905 000034 (v01 BOCHS  BXPC     00000001 BXPC 00000001)
[    0.024510] ACPI: FACP 0x0000000007FE17B9 000074 (v01 BOCHS  BXPC     00000001 BXPC 00000001)
[    0.025076] ACPI: DSDT 0x0000000007FE0040 001779 (v01 BOCHS  BXPC     00000001 BXPC 00000001)
[    0.025141] ACPI: FACS 0x0000000007FE0000 000040
[    0.025176] ACPI: APIC 0x0000000007FE182D 000078 (v01 BOCHS  BXPC     00000001 BXPC 00000001)
[    0.025190] ACPI: HPET 0x0000000007FE18A5 000038 (v01 BOCHS  BXPC     00000001 BXPC 00000001)
[    0.025204] ACPI: WAET 0x0000000007FE18DD 000028 (v01 BOCHS  BXPC     00000001 BXPC 00000001)
[    0.025276] ACPI: Reserving FACP table memory at [mem 0x7fe17b9-0x7fe182c]
[    0.025300] ACPI: Reserving DSDT table memory at [mem 0x7fe0040-0x7fe17b8]
[    0.025304] ACPI: Reserving FACS table memory at [mem 0x7fe0000-0x7fe003f]
[    0.025308] ACPI: Reserving APIC table memory at [mem 0x7fe182d-0x7fe18a4]
[    0.025312] ACPI: Reserving HPET table memory at [mem 0x7fe18a5-0x7fe18dc]
[    0.025316] ACPI: Reserving WAET table memory at [mem 0x7fe18dd-0x7fe1904]
[    0.026982] No NUMA configuration found
[    0.026999] Faking a node at [mem 0x0000000000000000-0x0000000007fdffff]
[    0.027489] NODE_DATA(0) allocated [mem 0x07fdc900-0x07fdffff]
[    0.029049] Zone ranges:
[    0.029066]   DMA      [mem 0x0000000000001000-0x0000000000ffffff]
[    0.029118]   DMA32    [mem 0x0000000001000000-0x0000000007fdffff]
[    0.029125]   Normal   empty
[    0.029141] Movable zone start for each node
[    0.029161] Early memory node ranges
[    0.029183]   node   0: [mem 0x0000000000001000-0x000000000009efff]
[    0.029309]   node   0: [mem 0x0000000000100000-0x0000000007fdffff]
[    0.029397] Initmem setup node 0 [mem 0x0000000000001000-0x0000000007fdffff]
[    0.031368] On node 0, zone DMA: 1 pages in unavailable ranges
[    0.031578] On node 0, zone DMA: 97 pages in unavailable ranges
[    0.032101] On node 0, zone DMA32: 32 pages in unavailable ranges
[    0.032501] ACPI: PM-Timer IO Port: 0x608
[    0.032849] ACPI: LAPIC_NMI (acpi_id[0xff] dfl dfl lint[0x1])
[    0.033154] IOAPIC[0]: apic_id 0, version 32, address 0xfec00000, GSI 0-23
[    0.033268] ACPI: INT_SRC_OVR (bus 0 bus_irq 0 global_irq 2 dfl dfl)
[    0.033465] ACPI: INT_SRC_OVR (bus 0 bus_irq 5 global_irq 5 high level)
[    0.033495] ACPI: INT_SRC_OVR (bus 0 bus_irq 9 global_irq 9 high level)
[    0.033556] ACPI: INT_SRC_OVR (bus 0 bus_irq 10 global_irq 10 high level)
[    0.033562] ACPI: INT_SRC_OVR (bus 0 bus_irq 11 global_irq 11 high level)
[    0.033718] ACPI: Using ACPI (MADT) for SMP configuration information
[    0.033756] ACPI: HPET id: 0x8086a201 base: 0xfed00000
[    0.034060] CPU topo: Max. logical packages:   1
[    0.034070] CPU topo: Max. logical dies:       1
[    0.034077] CPU topo: Max. dies per package:   1
[    0.034124] CPU topo: Max. threads per core:   1
[    0.034247] CPU topo: Num. cores per package:     1
[    0.034259] CPU topo: Num. threads per package:   1
[    0.034266] CPU topo: Allowing 1 present CPUs plus 0 hotplug CPUs
[    0.034932] PM: hibernation: Registered nosave memory: [mem 0x00000000-0x00000fff]
[    0.034959] PM: hibernation: Registered nosave memory: [mem 0x0009f000-0x000fffff]
[    0.035047] [mem 0x08000000-0xfffbffff] available for PCI devices
[    0.035079] Booting paravirtualized kernel on bare hardware
[    0.035303] clocksource: refined-jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 1910969940391419 ns
[    0.045972] setup_percpu: NR_CPUS:64 nr_cpumask_bits:1 nr_cpu_ids:1 nr_node_ids:1
[    0.047080] percpu: Embedded 52 pages/cpu s172440 r8192 d32360 u2097152
[    0.048691] Kernel command line: console=ttyS0
[    0.049422] printk: log buffer data + meta data: 262144 + 917504 = 1179648 bytes
[    0.052625] Dentry cache hash table entries: 16384 (order: 5, 131072 bytes, linear)
[    0.055426] Inode-cache hash table entries: 8192 (order: 4, 65536 bytes, linear)
[    0.057936] Fallback order for Node 0: 0
[    0.058187] Built 1 zonelists, mobility grouping on.  Total pages: 32638
[    0.058203] Policy zone: DMA32
[    0.058435] mem auto-init: stack:off, heap alloc:off, heap free:off
[    0.064904] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=1, Nodes=1
[    0.077203] Dynamic Preempt: voluntary
[    0.081392] rcu: Preemptible hierarchical RCU implementation.
[    0.081415] rcu:     RCU event tracing is enabled.
[    0.081437] rcu:     RCU restricting CPUs from NR_CPUS=64 to nr_cpu_ids=1.
[    0.081556]  Trampoline variant of Tasks RCU enabled.
[    0.081563]  Tracing variant of Tasks RCU enabled.
[    0.081624] rcu: RCU calculated value of scheduler-enlistment delay is 100 jiffies.
[    0.081643] rcu: Adjusting geometry for rcu_fanout_leaf=16, nr_cpu_ids=1
[    0.082594] RCU Tasks: Setting shift to 0 and lim to 1 rcu_task_cb_adjust=1 rcu_task_cpu_ids=1.
[    0.082611] RCU Tasks Trace: Setting shift to 0 and lim to 1 rcu_task_cb_adjust=1 rcu_task_cpu_ids=1.
[    0.103380] NR_IRQS: 4352, nr_irqs: 256, preallocated irqs: 16
[    0.111101] rcu: srcu_init: Setting srcu_struct sizes based on contention.
[    0.115558] Console: colour VGA+ 80x25
[    0.117105] printk: legacy console [ttyS0] enabled
[    0.148534] ACPI: Core revision 20250807
[    0.155394] clocksource: hpet: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 19112604467 ns
[    0.160386] APIC: Switch to symmetric I/O mode setup
[    0.164110] ..TIMER: vector=0x30 apic1=0 pin1=2 apic2=-1 pin2=-1
[    0.170486] clocksource: tsc-early: mask: 0xffffffffffffffff max_cycles: 0x2a87760835b, max_idle_ns: 440795322967 ns
[    0.171624] Calibrating delay loop (skipped), value calculated using timer frequency.. 5900.91 BogoMIPS (lpj=2950459)
[    0.175001] Last level iTLB entries: 4KB 512, 2MB 255, 4MB 127
[    0.175347] Last level dTLB entries: 4KB 512, 2MB 255, 4MB 127, 1GB 0
[    0.175668] mitigations: Enabled attack vectors: user_kernel, user_user, SMT mitigations: auto
[    0.176695] Spectre V2 : Mitigation: Retpolines
[    0.177024] Spectre V1 : Mitigation: usercopy/swapgs barriers and __user pointer sanitization
[    0.177596] Spectre V2 : Spectre v2 / SpectreRSB: Filling RSB on context switch and VMEXIT
[    0.181551] x86/fpu: x87 FPU will use FXSAVE
[    0.580055] Freeing SMP alternatives memory: 52K
[    0.581228] pid_max: default: 32768 minimum: 301
[    0.587607] LSM: initializing lsm=capability,selinux
[    0.589432] SELinux:  Initializing.
[    0.593685] Mount-cache hash table entries: 512 (order: 0, 4096 bytes, linear)
[    0.594231] Mountpoint-cache hash table entries: 512 (order: 0, 4096 bytes, linear)
[    0.724315] smpboot: CPU0: AMD QEMU Virtual CPU version 2.5+ (family: 0xf, model: 0x6b, stepping: 0x1)
[    0.733303] Performance Events: PMU not available due to virtualization, using software events only.
[    0.734887] signal: max sigframe size: 1440
[    0.736510] rcu: Hierarchical SRCU implementation.
[    0.736828] rcu:     Max phase no-delay instances is 400.
[    0.741441] smp: Bringing up secondary CPUs ...
[    0.743260] smp: Brought up 1 node, 1 CPU
[    0.743852] smpboot: Total of 1 processors activated (5900.91 BogoMIPS)
[    0.751233] Memory: 84040K/130552K available (18446K kernel code, 2899K rwdata, 7348K rodata, 2884K init, 628K bss, 44120K reserved, 0K cma-reserved)
[    0.759230] devtmpfs: initialized
[    0.770769] clocksource: jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 1911260446275000 ns
[    0.772525] posixtimers hash table entries: 512 (order: 1, 8192 bytes, linear)
[    0.773100] futex hash table entries: 256 (16384 bytes on 1 NUMA nodes, total 16 KiB, linear).
[    0.777156] PM: RTC time: 15:12:05, date: 2025-12-25
[    0.782426] NET: Registered PF_NETLINK/PF_ROUTE protocol family
[    0.786113] audit: initializing netlink subsys (disabled)
[    0.789929] audit: type=2000 audit(1766675524.629:1): state=initialized audit_enabled=0 res=1
[    0.796302] thermal_sys: Registered thermal governor 'step_wise'
[    0.796961] cpuidle: using governor menu
[    0.801231] PCI: Using configuration type 1 for base access
[    0.804999] kprobes: kprobe jump-optimization is enabled. All kprobes are optimized if possible.
[    0.810498] HugeTLB: registered 2.00 MiB page size, pre-allocated 0 pages
[    0.810934] HugeTLB: 28 KiB vmemmap can be freed for a 2.00 MiB page
[    0.821272] ACPI: Added _OSI(Module Device)
[    0.822444] ACPI: Added _OSI(Processor Device)
[    0.822685] ACPI: Added _OSI(Processor Aggregator Device)
[    0.859474] ACPI: 1 ACPI AML tables successfully acquired and loaded
[    0.895055] ACPI: Interpreter enabled
[    0.898835] ACPI: PM: (supports S0 S3 S4 S5)
[    0.899516] ACPI: Using IOAPIC for interrupt routing
[    0.902008] PCI: Using host bridge windows from ACPI; if necessary, use "pci=nocrs" and report a bug
[    0.902468] PCI: Using E820 reservations for host bridge windows
[    0.906974] ACPI: Enabled 2 GPEs in block 00 to 0F
[    1.014845] ACPI: PCI Root Bridge [PCI0] (domain 0000 [bus 00-ff])
[    1.016518] acpi PNP0A03:00: _OSC: OS supports [ASPM ClockPM Segments MSI HPX-Type3]
[    1.017547] acpi PNP0A03:00: _OSC: not requesting OS control; OS requires [ExtendedConfig ASPM ClockPM MSI]
[    1.025319] acpi PNP0A03:00: fail to add MMCONFIG information, can't access extended configuration space under this bridge
[    1.035173] PCI host bridge to bus 0000:00
[    1.035777] pci_bus 0000:00: root bus resource [io  0x0000-0x0cf7 window]
[    1.036568] pci_bus 0000:00: root bus resource [io  0x0d00-0xffff window]
[    1.036896] pci_bus 0000:00: root bus resource [mem 0x000a0000-0x000bffff window]
[    1.037485] pci_bus 0000:00: root bus resource [mem 0x08000000-0xfebfffff window]
[    1.037729] pci_bus 0000:00: root bus resource [mem 0x100000000-0x17fffffff window]
[    1.045668] pci_bus 0000:00: root bus resource [bus 00-ff]
[    1.047682] pci 0000:00:00.0: [8086:1237] type 00 class 0x060000 conventional PCI endpoint
[    1.068884] pci 0000:00:01.0: [8086:7000] type 00 class 0x060100 conventional PCI endpoint
[    1.075886] pci 0000:00:01.1: [8086:7010] type 00 class 0x010180 conventional PCI endpoint
[    1.077552] pci 0000:00:01.1: BAR 4 [io  0xc040-0xc04f]
[    1.078457] pci 0000:00:01.1: BAR 0 [io  0x01f0-0x01f7]: legacy IDE quirk
[    1.078927] pci 0000:00:01.1: BAR 1 [io  0x03f6]: legacy IDE quirk
[    1.079480] pci 0000:00:01.1: BAR 2 [io  0x0170-0x0177]: legacy IDE quirk
[    1.079769] pci 0000:00:01.1: BAR 3 [io  0x0376]: legacy IDE quirk
[    1.084947] pci 0000:00:01.3: [8086:7113] type 00 class 0x068000 conventional PCI endpoint
[    1.085781] pci 0000:00:01.3: quirk: [io  0x0600-0x063f] claimed by PIIX4 ACPI
[    1.086234] pci 0000:00:01.3: quirk: [io  0x0700-0x070f] claimed by PIIX4 SMB
[    1.087853] pci 0000:00:02.0: [1234:1111] type 00 class 0x030000 conventional PCI endpoint
[    1.089505] pci 0000:00:02.0: BAR 0 [mem 0xfd000000-0xfdffffff pref]
[    1.089972] pci 0000:00:02.0: BAR 2 [mem 0xfebb0000-0xfebb0fff]
[    1.090708] pci 0000:00:02.0: ROM [mem 0xfeba0000-0xfebaffff pref]
[    1.095606] pci 0000:00:02.0: Video device with shadowed ROM at [mem 0x000c0000-0x000dffff]
[    1.109584] pci 0000:00:03.0: [8086:100e] type 00 class 0x020000 conventional PCI endpoint
[    1.111947] pci 0000:00:03.0: BAR 0 [mem 0xfeb80000-0xfeb9ffff]
[    1.112480] pci 0000:00:03.0: BAR 1 [io  0xc000-0xc03f]
[    1.112833] pci 0000:00:03.0: ROM [mem 0xfeb00000-0xfeb7ffff pref]
[    1.141989] ACPI: PCI: Interrupt link LNKA configured for IRQ 10
[    1.143706] ACPI: PCI: Interrupt link LNKB configured for IRQ 10
[    1.145046] ACPI: PCI: Interrupt link LNKC configured for IRQ 11
[    1.146109] ACPI: PCI: Interrupt link LNKD configured for IRQ 11
[    1.151950] ACPI: PCI: Interrupt link LNKS configured for IRQ 9
[    1.154697] iommu: Default domain type: Translated
[    1.154989] iommu: DMA domain TLB invalidation policy: lazy mode
[    1.156884] SCSI subsystem initialized
[    1.162827] ACPI: bus type USB registered
[    1.163706] usbcore: registered new interface driver usbfs
[    1.164226] usbcore: registered new interface driver hub
[    1.168710] usbcore: registered new device driver usb
[    1.169404] pps_core: LinuxPPS API ver. 1 registered
[    1.169606] pps_core: Software ver. 5.3.6 - Copyright 2005-2007 Rodolfo Giometti <giometti@linux.it>
[    1.170234] PTP clock support registered
[    1.173155] Advanced Linux Sound Architecture Driver Initialized.
[    1.188200] NetLabel: Initializing
[    1.188483] NetLabel:  domain hash size = 128
[    1.188756] NetLabel:  protocols = UNLABELED CIPSOv4 CALIPSO
[    1.196766] NetLabel:  unlabeled traffic allowed by default
[    1.204933] PCI: Using ACPI for IRQ routing
[    1.207909] pci 0000:00:02.0: vgaarb: setting as boot VGA device
[    1.208370] pci 0000:00:02.0: vgaarb: bridge control possible
[    1.208414] pci 0000:00:02.0: vgaarb: VGA device added: decodes=io+mem,owns=io+mem,locks=none
[    1.208477] vgaarb: loaded
[    1.215440] hpet: 3 channels of 0 reserved for per-cpu timers
[    1.216056] hpet0: at MMIO 0xfed00000, IRQs 2, 8, 0
[    1.216414] hpet0: 3 comparators, 64-bit 100.000000 MHz counter
[    1.224136] clocksource: Switched to clocksource tsc-early
[    1.232640] VFS: Disk quotas dquot_6.6.0
[    1.232934] VFS: Dquot-cache hash table entries: 512 (order 0, 4096 bytes)
[    1.247160] pnp: PnP ACPI init
[    1.257014] pnp: PnP ACPI: found 6 devices
[    1.310279] clocksource: acpi_pm: mask: 0xffffff max_cycles: 0xffffff, max_idle_ns: 2085701024 ns
[    1.314837] NET: Registered PF_INET protocol family
[    1.320218] IP idents hash table entries: 2048 (order: 2, 16384 bytes, linear)
[    1.339108] tcp_listen_portaddr_hash hash table entries: 256 (order: 0, 4096 bytes, linear)
[    1.339725] Table-perturb hash table entries: 65536 (order: 6, 262144 bytes, linear)
[    1.340261] TCP established hash table entries: 1024 (order: 1, 8192 bytes, linear)
[    1.340870] TCP bind hash table entries: 1024 (order: 3, 32768 bytes, linear)
[    1.341726] TCP: Hash tables configured (established 1024 bind 1024)
[    1.348819] UDP hash table entries: 256 (order: 2, 16384 bytes, linear)
[    1.349933] UDP-Lite hash table entries: 256 (order: 2, 16384 bytes, linear)
[    1.352117] NET: Registered PF_UNIX/PF_LOCAL protocol family
[    1.360421] RPC: Registered named UNIX socket transport module.
[    1.360754] RPC: Registered udp transport module.
[    1.361064] RPC: Registered tcp transport module.
[    1.361384] RPC: Registered tcp-with-tls transport module.
[    1.361816] RPC: Registered tcp NFSv4.1 backchannel transport module.
[    1.367323] pci_bus 0000:00: resource 4 [io  0x0000-0x0cf7 window]
[    1.367604] pci_bus 0000:00: resource 5 [io  0x0d00-0xffff window]
[    1.367879] pci_bus 0000:00: resource 6 [mem 0x000a0000-0x000bffff window]
[    1.368856] pci_bus 0000:00: resource 7 [mem 0x08000000-0xfebfffff window]
[    1.370305] pci_bus 0000:00: resource 8 [mem 0x100000000-0x17fffffff window]
[    1.376441] pci 0000:00:01.0: PIIX3: Enabling Passive Release
[    1.376827] pci 0000:00:00.0: Limiting direct PCI/PCI transfers
[    1.377480] PCI: CLS 0 bytes, default 64
[    1.540770] Initialise system trusted keyrings
[    1.544029] workingset: timestamp_bits=56 max_order=15 bucket_order=0
[    1.550187] NFS: Registering the id_resolver key type
[    1.550803] Key type id_resolver registered
[    1.551431] Key type id_legacy registered
[    1.552664] 9p: Installing v9fs 9p2000 file system support
[    1.632199] Key type asymmetric registered
[    1.632488] Asymmetric key parser 'x509' registered
[    1.633048] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 251)
[    1.634291] io scheduler mq-deadline registered
[    1.634587] io scheduler kyber registered
[    1.638152] input: Power Button as /devices/LNXSYSTM:00/LNXPWRBN:00/input/input0
[    1.642509] ACPI: button: Power Button [PWRF]
[    1.645584] Serial: 8250/16550 driver, 4 ports, IRQ sharing enabled
[    1.650892] 00:04: ttyS0 at I/O 0x3f8 (irq = 4, base_baud = 115200) is a 16550A
[    1.662403] Non-volatile memory driver v1.3
[    1.662686] Linux agpgart interface v0.103
[    1.664752] ACPI: bus type drm_connector registered
[    1.684407] loop: module loaded
[    1.693176] scsi host0: ata_piix
[    1.697623] scsi host1: ata_piix
[    1.698638] ata1: PATA max MWDMA2 cmd 0x1f0 ctl 0x3f6 bmdma 0xc040 irq 14 lpm-pol 0
[    1.699345] ata2: PATA max MWDMA2 cmd 0x170 ctl 0x376 bmdma 0xc048 irq 15 lpm-pol 0
[    1.705422] e100: Intel(R) PRO/100 Network Driver
[    1.705695] e100: Copyright(c) 1999-2006 Intel Corporation
[    1.706162] e1000: Intel(R) PRO/1000 Network Driver
[    1.706509] e1000: Copyright (c) 1999-2006 Intel Corporation.
[    1.856523] ata2: found unknown device (class 0)
[    1.864897] ata2.00: ATAPI: QEMU DVD-ROM, 2.5+, max UDMA/100
[    1.889058] scsi 1:0:0:0: CD-ROM            QEMU     QEMU DVD-ROM     2.5+ PQ: 0 ANSI: 5
[    1.912245] sr 1:0:0:0: [sr0] scsi3-mmc drive: 4x/4x cd/rw xa/form2 tray
[    1.912938] cdrom: Uniform CD-ROM driver Revision: 3.20
[    1.928788] sr 1:0:0:0: Attached scsi generic sg0 type 5
[    1.949270] ACPI: \_SB_.LNKC: Enabled at IRQ 11
[    2.248777] e1000 0000:00:03.0 eth0: (PCI:33MHz:32-bit) 52:54:00:12:34:56
[    2.249594] e1000 0000:00:03.0 eth0: Intel(R) PRO/1000 Network Connection
[    2.250694] e1000e: Intel(R) PRO/1000 Network Driver
[    2.251362] e1000e: Copyright(c) 1999 - 2015 Intel Corporation.
[    2.252167] sky2: driver version 1.30
[    2.255070] usbcore: registered new interface driver usblp
[    2.255549] usbcore: registered new interface driver usb-storage
[    2.256726] i8042: PNP: PS/2 Controller [PNP0303:KBD,PNP0f13:MOU] at 0x60,0x64 irq 1,12
[    2.261558] serio: i8042 KBD port at 0x60,0x64 irq 1
[    2.262170] serio: i8042 AUX port at 0x60,0x64 irq 12
[    2.264648] rtc_cmos 00:05: RTC can wake from S4
[    2.271833] rtc_cmos 00:05: registered as rtc0
[    2.273265] rtc_cmos 00:05: alarms up to one day, y3k, 242 bytes nvram, hpet irqs
[    2.275397] device-mapper: ioctl: 4.50.0-ioctl (2025-04-28) initialised: dm-devel@lists.linux.dev
[    2.276606] amd_pstate: the _CPC object is not present in SBIOS or ACPI disabled
[    2.277502] hid: raw HID events driver (C) Jiri Kosina
[    2.279568] usbcore: registered new interface driver usbhid
[    2.279894] usbhid: USB HID core driver
[    2.287128] Initializing XFRM netlink socket
[    2.287825] NET: Registered PF_INET6 protocol family
[    2.295164] Segment Routing with IPv6
[    2.295546] In-situ OAM (IOAM) with IPv6
[    2.296583] sit: IPv6, IPv4 and MPLS over IPv4 tunneling driver
[    2.299633] NET: Registered PF_PACKET protocol family
[    2.303058] 9pnet: Installing 9P2000 support
[    2.303555] Key type dns_resolver registered
[    2.306262] IPI shorthand broadcast: enabled
[    2.335112] sched_clock: Marking stable (2287107565, 47872115)->(2345777921, -10798241)
[    2.338529] registered taskstats version 1
[    2.338938] Loading compiled-in X.509 certificates
[    2.351361] Demotion targets for Node 0: null
[    2.356182] PM:   Magic number: 5:675:235
[    2.356687] netconsole: network logging started
[    2.358238] cfg80211: Loading compiled-in X.509 certificates for regulatory database
[    2.365790] kworker/u4:0 (51) used greatest stack depth: 14384 bytes left
[    2.375786] Loaded X.509 cert 'sforshee: 00b28ddf47aef9cea7'
[    2.377489] Loaded X.509 cert 'wens: 61c038651aabdcf94bd0ac7ff06c7248db18c600'
[    2.379589] faux_driver regulatory: Direct firmware load for regulatory.db failed with error -2
[    2.380896] ALSA device list:
[    2.381635] cfg80211: failed to load regulatory.db
[    2.382325]   No soundcards found.
[    2.388675] check access for rdinit=/init failed: -2, ignoring
[    2.432571] tsc: Refined TSC clocksource calibration: 2950.418 MHz
[    2.433260] clocksource: tsc: mask: 0xffffffffffffffff max_cycles: 0x2a874f2c57f, max_idle_ns: 440795274316 ns
[    2.433939] clocksource: Switched to clocksource tsc
[    2.682360] input: ImExPS/2 Generic Explorer Mouse as /devices/platform/i8042/serio1/input/input2
[    2.685366] input: AT Translated Set 2 keyboard as /devices/platform/i8042/serio0/input/input3
[    2.690187] md: Waiting for all devices to be available before autodetect
[    2.690578] md: If you don't use raid, use raid=noautodetect
[    2.690950] md: Autodetecting RAID arrays.
[    2.691329] md: autorun ...
[    2.691519] md: ... autorun DONE.
[    2.694548] /dev/root: Can't open blockdev
[    2.695414] VFS: Cannot open root device "" or unknown-block(0,0): error -6
[    2.695765] Please append a correct "root=" boot option; here are the available partitions:
[    2.696585] 0b00         1048575 sr0
[    2.696670]  driver: sr
[    2.697118] List of all bdev filesystems:
[    2.697297]  ext3
[    2.697344]  ext2
[    2.697417]  ext4
[    2.697490]  vfat
[    2.697537]  msdos
[    2.697625]  iso9660
[    2.697751]
[    2.698058] Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0)
[    2.698797] CPU: 0 UID: 0 PID: 1 Comm: swapper/0 Not tainted 6.18.2 #1 PREEMPT(voluntary)
[    2.699143] Hardware name: QEMU Standard PC (i440FX + PIIX, 1996), BIOS 1.15.0-1 04/01/2014
[    2.699670] Call Trace:
[    2.700352]  <TASK>
[    2.700788]  vpanic+0x34f/0x360
[    2.701257]  panic+0x56/0x60
[    2.701500]  mount_root_generic+0x1ff/0x320
[    2.701761]  prepare_namespace+0x63/0x270
[    2.701989]  kernel_init_freeable+0x29a/0x2e0
[    2.702219]  ? __pfx_kernel_init+0x10/0x10
[    2.702482]  kernel_init+0x15/0x1c0
[    2.702630]  ret_from_fork+0xc1/0x100
[    2.702777]  ? __pfx_kernel_init+0x10/0x10
[    2.702981]  ret_from_fork_asm+0x1a/0x30
[    2.703225]  </TASK>
[    2.703736] Kernel Offset: 0x35600000 from 0xffffffff81000000 (relocation range: 0xffffffff80000000-0xffffffffbfffffff)
[    2.704462] ---[ end Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0) ]---
```

</details>

For the time being, let's focus on the tail section of the emitted kernel log:

```
[    2.690187] md: Waiting for all devices to be available before autodetect
[    2.690578] md: If you don't use raid, use raid=noautodetect
[    2.690950] md: Autodetecting RAID arrays.
[    2.691329] md: autorun ...
[    2.691519] md: ... autorun DONE.
[    2.694548] /dev/root: Can't open blockdev
[    2.695414] VFS: Cannot open root device "" or unknown-block(0,0): error -6
[    2.695765] Please append a correct "root=" boot option; here are the available partitions:
[    2.696585] 0b00         1048575 sr0
[    2.696670]  driver: sr
[    2.697118] List of all bdev filesystems:
[    2.697297]  ext3
[    2.697344]  ext2
[    2.697417]  ext4
[    2.697490]  vfat
[    2.697537]  msdos
[    2.697625]  iso9660
[    2.697751]
[    2.698058] Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0)
[    2.698797] CPU: 0 UID: 0 PID: 1 Comm: swapper/0 Not tainted 6.18.2 #1 PREEMPT(voluntary)
[    2.699143] Hardware name: QEMU Standard PC (i440FX + PIIX, 1996), BIOS 1.15.0-1 04/01/2014
[    2.699670] Call Trace:
[    2.700352]  <TASK>
[    2.700788]  vpanic+0x34f/0x360
[    2.701257]  panic+0x56/0x60
[    2.701500]  mount_root_generic+0x1ff/0x320
[    2.701761]  prepare_namespace+0x63/0x270
[    2.701989]  kernel_init_freeable+0x29a/0x2e0
[    2.702219]  ? __pfx_kernel_init+0x10/0x10
[    2.702482]  kernel_init+0x15/0x1c0
[    2.702630]  ret_from_fork+0xc1/0x100
[    2.702777]  ? __pfx_kernel_init+0x10/0x10
[    2.702981]  ret_from_fork_asm+0x1a/0x30
[    2.703225]  </TASK>
[    2.703736] Kernel Offset: 0x35600000 from 0xffffffff81000000 (relocation range: 0xffffffff80000000-0xffffffffbfffffff)
[    2.704462] ---[ end Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0) ]---
```

We observe the following:

- The kernel tries to detect block devices
- `VFS` (virtual file system) cannot find a block storage device to mount the root filesystem to
- `VFS` is unable to mount `rootfs`
- This leads to a kernel panic

> Use Ctrl+A + X to exit QEMU.

## Build rootfs

So we need something called a root filesystem and some block storage device. But what does this mean?

> From <b>The Linux BootDisk HOWTO</b> - <i>["Building a root filesystem"](https://tldp.org/HOWTO/Bootdisk-HOWTO/buildroot.html)</i>
>
> A root filesystem must contain everything needed to support a full Linux system. To be able to do this, the disk must include the minimum requirements for a Linux system:
>
> - The basic file system structure,
> - Minimum set of directories: /dev, /proc, /bin, /etc, /lib, /usr, /tmp,
> - Basic set of utilities: sh, ls, cp, mv, etc.,
> - Minimum set of config files: rc, inittab, fstab, etc.,
> - Devices: /dev/hd*, /dev/tty*, /dev/fd0, etc.,
> - Runtime library to provide basic functions used by utilities.

<p align="center">
<img src="./img/standard-unix-filesystem-hierarchy-1.png" alt="linux-source-tree"/>
Fig: The Linux Filesystem Hierarchy (https://www.linuxfoundation.org)
</p>

> For more information, see [`file-hierarchy(7)`](https://man7.org/linux/man-pages/man7/file-hierarchy.7.html) on the Linux man pages.

We will create our `rootfs` with [`busybox`](https://busybox.net).

> From **`busybox`** - <i>["About Busybox"](https://busybox.net/about.html)</i>
>
> ### BusyBox: The Swiss Army Knife of Embedded Linux
>
> BusyBox combines tiny versions of many common UNIX utilities into a single small executable. It provides replacements for most of
> the utilities you usually find in GNU fileutils, shellutils, etc. The utilities in BusyBox generally have fewer options than their
> full-featured GNU cousins; however, the options that are included provide the expected functionality and behave very much like their
> GNU counterparts. BusyBox provides a fairly complete environment for any small or embedded system.
>
> BusyBox has been written with size-optimization and limited resources in mind. It is also extremely modular so you can easily
> include or exclude commands (or features) at compile time. This makes it easy to customize your embedded systems. To create a
> working system, just add some device nodes in /dev, a few configuration files in /etc, and a Linux kernel.
>
> BusyBox is maintained by Denys Vlasenko, and licensed under the GNU GENERAL PUBLIC LICENSE version 2.

`busybox` provides a single static binary with the same name, which acts as something called a "multicall" binary. Essentialy,
it uses `argv[0]` i.e the first command line parameter (which is the invoking binaries file name) to decide which functionality
to provide. This way you can create multiple symlinks named `mv`, `cp` etc. to the same `busybox` binary path; yet when you
invoke these symlinks, they will provide their intended unique functionality.

### Build BusyBox from source

Similar workflow to our kernel: fetch sources, extract, configure, build.

```bash
popd
wget https://busybox.net/downloads/busybox-1.36.1.tar.bz2
tar xf busybox-1.36.1.tar.bz2
pushd busybox-1.36.1
make menuconfig
```

Now select:

1. Settings
2. Enable "Build static binary (no shapred libs)" (press space)
3. Select and specify "Destination path for 'make install'" as "./../rootfs"

<p align="center">
<img src="./img/busybox-config.png" alt="busybox-config" width="800"/>
</p>

Exit and save configuration.

Next, build busybox from sources:

```bash
# customize number of make jobs as necessary
make -j12
sudo make install
popd
```

> Why `sudo`? The `rootfs` directory and it's contents must belong to `root`.

This should result in a `rootfs` directory in our `kernel-workspace` directory.

Next, let's add the missing directories and configuration for our rootfs:

```bash
pushd rootfs
sudo mkdir -p proc sys dev tmp etc
```

Next, inside the `rootfs` directory, create a new file `./etc/inittab` with the following contents:

```
::sysinit:/bin/mount -t proc proc /proc
::sysinit:/bin/mount -t sysfs sysfs /sys
::sysinit:/bin/mount -t devtmpfs dev /dev

::sysinit:/sbin/ip link set eth0 up
::sysinit:/sbin/udhcpc -i eth0 -s /usr/share/udhcpc/default.script

ttyS0::respawn:/sbin/getty -n -l /bin/sh -L ttyS0 115200 vt100
```

> `/etc/inittab` is the configuration file to the `/sbin/init` init process for our `busybox` system.
>
> The init process is the first process to run on a Linux system. It has `PID = 0` (PID: process id).
>
> It is responsible for mounting file systems, setting up devices and starting system services.
>
> Let's analyse our `/etc/inittab` configuration:
>
> ```
> ::sysinit:/bin/mount -t proc proc /proc
> ::sysinit:/bin/mount -t sysfs sysfs /sys
> ::sysinit:/bin/mount -t devtmpfs dev /dev
> ```
>
> Mount `procfs`, `sysfs`, `devtmpfs`. These are psuedo filesystems that allow interacting with
> processes, system configuration and devices, respectively, via file-like interfaces.
>
> ```
> ::sysinit:/sbin/ip link set eth0 up
> ::sysinit:/sbin/udhcpc -i eth0 -s /usr/share/udhcpc/default.script
> ```
>
> Setup `eth0` network interface and DHCP with `udhcpc`.
>
> ```
> ttyS0::respawn:/sbin/getty -n -l /bin/sh -L ttyS0 115200 vt100
> ```
>
> Spawn a shell terminal.

Also copy the udhcpc script:

```bash
sudo mkdir -p usr/share/udhcpc
sudo cp ../busybox-1.36.1/examples/udhcp/simple.script usr/share/udhcpc/default.script
sudo chmod +x usr/share/udhcpc/default.script
```

With this, our `rootfs` is ready.

Now how do we mount this rootfs? We have the following options:

- Use a virtual hard disk

  - Create a virtual hard disk file.
  - Create partitions on this virtual hardisk
  - Mount it to the host machine
  - Install the bootloader (GRUB) into it
  - Copy the kernel image and rootfs into it
  - Pass it to QEMU as the only block storage device

- Use QEMU SeaBIOS bootloader + `initramfs`
  - Create an `initramfs` `CPIO` archive from out `rootfs`
  - Pass the kernel and the initramfs to QEMU to boot with SeaBIOS

Using an `initramfs` is significantly simpler, that is the approach we will be taking for now.

> From <b>The Linux Kernel Documentation</b> - <i>["Ramfs, rootfs and initramfs"](https://docs.kernel.org/filesystems/ramfs-rootfs-initramfs.html)</i>
>
> ### What is ramfs?
>
> Ramfs is a very simple filesystem that exports Linux’s disk caching mechanisms (the page
> cache and dentry cache) as a dynamically resizable RAM-based filesystem.
>
> Normally all files are cached in memory by Linux. Pages of data read from backing store
> (usually the block device the filesystem is mounted on) are kept around in case it’s
> needed again, but marked as clean (freeable) in case the Virtual Memory system needs the
> memory for something else. Similarly, data written to files is marked clean as soon as it
> has been written to backing store, but kept around for caching purposes until the VM
> reallocates the memory. A similar mechanism (the dentry cache) greatly speeds up access
> to directories.
>
> With ramfs, there is no backing store. Files written into ramfs allocate dentries and page
> cache as usual, but there’s nowhere to write them to. This means the pages are never marked
> clean, so they can’t be freed by the VM when it’s looking to recycle memory.
>
> The amount of code required to implement ramfs is tiny, because all the work is done by
> the existing Linux caching infrastructure. Basically, you’re mounting the disk cache as a
> filesystem. Because of this, ramfs is not an optional component removable via menuconfig,
> since there would be negligible space savings.
>
> _[ ... ]_
>
> ### What is initramfs?
>
> Linux kernels contain a gzipped “cpio” format archive, which is extracted into rootfs
> when the kernel boots up. After extracting, the kernel checks to see if rootfs contains
> a file “init”, and if so it executes it as PID 1. If found, this init process is responsible
> for bringing the system the rest of the way up, including locating and mounting the real
> root device (if any). If rootfs does not contain an init program after the embedded cpio
> archive is extracted into it, the kernel will fall through to the older code to locate
> and mount a root partition, then exec some variant of /sbin/init out of that.

### Build initramfs

In the `rootfs` directory:

```bash
find . -print0 | cpio --null -ov --format=newc | gzip -9 > ../initramfs.cpio.gz
popd
```

You should now be in the parent `kernel-workspace` directory.

## Kernel boot attempt #1

Now with our initramfs and kernel image ready, let's boot our kernel:

```bash
sudo qemu-system-x86_64 \
    -kernel linux-${VERSION}/arch/x86/boot/bzImage \
    -initrd initramfs.cpio.gz \
    -append "console=ttyS0 rdinit=/sbin/init" \
    -device virtio-net,netdev=n0 \
    -netdev user,id=n0 \
    -nographic \
    -enable-kvm \
    -cpu host
```

`rdinit` specifies the `init` binary path.

> Why `sudo`? In order to use `KVM` acceleration QEMU needs to access `/dev/kvm`. You either need to be
> part of the `kvm` user group or run this as `root`.
>
> ```bash
> $ ls -l /dev/kvm
> crw-rw---- 1 root kvm 10, 232 Nov 30 10:00 /dev/kvm
> ```

This launches us into a `/bin/sh` shell:

```
[    0.802828] cfg80211: failed to load regulatory.db
[    0.856293] ata2: found unknown device (class 0)
[    0.858599] ata2.00: ATAPI: QEMU DVD-ROM, 2.5+, max UDMA/100
[    0.862059] scsi 1:0:0:0: CD-ROM            QEMU     QEMU DVD-ROM     2.5+ PQ: 0 ANSI: 5
[    0.874383] sr 1:0:0:0: [sr0] scsi3-mmc drive: 4x/4x cd/rw xa/form2 tray
[    0.876480] cdrom: Uniform CD-ROM driver Revision: 3.20
[    0.882498] sr 1:0:0:0: Attached scsi generic sg0 type 5
[    0.884890] Freeing unused kernel image (initmem) memory: 2868K
[    0.886516] Write protecting the kernel read-only data: 26624k
[    0.889554] Freeing unused kernel image (text/rodata gap) memory: 72K
[    0.892213] Freeing unused kernel image (rodata/data gap) memory: 872K
[    0.924143] x86/mm: Checked W+X mappings: passed, no W+X pages found.
[    0.925750] Run /sbin/init as init process
[    0.930418] ip (58) used greatest stack depth: 13536 bytes left
udhcpc: started, v1.36.1
Clearing IP addresses on eth0, upping it
[    0.935532] ip (61) used greatest stack depth: 13520 bytes left
udhcpc: broadcasting discover
udhcpc: broadcasting select for 10.0.2.15, server 10.0.2.2
udhcpc: lease of 10.0.2.15 obtained from 10.0.2.2, lease time 86400
Setting IP address 10.0.2.15 on eth0
Deleting routers
route: SIOCDELRT: No such process
Adding router 10.0.2.2
Recreating /etc/resolv.conf
 Adding DNS server 10.0.2.3

~ # [    1.156665] input: ImExPS/2 Generic Explorer Mouse as /devices/platform/i8042/serio1/input/input3

~ # ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8): 56 data bytes
64 bytes from 8.8.8.8: seq=0 ttl=255 time=33.543 ms
64 bytes from 8.8.8.8: seq=1 ttl=255 time=35.879 ms
64 bytes from 8.8.8.8: seq=2 ttl=255 time=32.863 ms
64 bytes from 8.8.8.8: seq=3 ttl=255 time=33.360 ms
64 bytes from 8.8.8.8: seq=4 ttl=255 time=32.872 ms
64 bytes from 8.8.8.8: seq=5 ttl=255 time=92.780 ms
^C
--- 8.8.8.8 ping statistics ---
6 packets transmitted, 6 packets received, 0% packet loss
round-trip min/avg/max = 32.863/43.549/92.780 ms
~ # wget -qO- http://ifconfig.me/all
ip_addr: xxx.xxx.xxx.xxx
remote_host: unavailable
user_agent: Wget
port: 50858
language:
referer:
connection:
keep_alive:
method: GET
encoding:
mime:
charset:
via: 1.1 google
forwarded: xxx.xxx.xxx.xxx,xxx.xxx.xxx.xxx
~ #
```

We have internet access in our VM!

We now have a workflow to run and test custom kernels, kernel modules, drivers and different userspace utilities.
