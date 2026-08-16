# Original Xbox port status

The original Xbox port is experimental and is not ready for general use. This
document describes the code tracked on the `xbox/native-bringup` branch; it
does not describe probes or uncommitted experiments kept in external
worktrees.

## Implemented

- `i686-pc-xbox` Binutils/GCC/Newlib toolchain profiles and PE/COFF linker
  support
- KOS startup and shutdown through a compatible loader
- x86 GDT, IDT/PIC interrupt handling, PIT scheduling timer, stack handling,
  static TLS, and integer/FPU/SSE thread context switching
- KOS, C11, and POSIX threading primitives, including compiler TLS
- OHCI USB host, hub enumeration, Xbox Input Device discovery, hotplug, and
  controller state polling
- MCPX real-time clock reads and writes
- SMBus transport and safety-bounded SMC status queries
- Shared `kosload` console, host filesystem, and loader-exit support

The examples under `examples/xbox/` cover loader startup, threading, RTC, and
SMC status. USB controller validation is currently performed by the external
interactive hardware test harness.

## Known limitations

- The port relies on the Xbox kernel's existing paging and physical-memory
  setup; it does not own or discover the complete memory map.
- Hardware support is limited to the devices listed above. There is no GPU,
  audio, storage, or network driver in this branch.
- The build produces loader-hosted ELF images. A complete, supported
  standalone XBE packaging workflow is not integrated into KOS.
- Emulator coverage cannot replace retail testing. The latest upstream merge
  has compile-time coverage only and needs a fresh xemu and retail hardware
  pass before review sign-off.
- The Dreamcast-to-shared `kosload` refactor predates upstream's August 2026
  `dcload` backend changes. The merge retains the shared implementation; its
  KOS-socket transport should receive a focused compatibility review.

## Build

Install the Xbox toolchain, set `KOS_CC_BASE` to its parent directory, then:

```sh
source environ.sh
make
```

The toolchain configured by this branch targets `i686-pc-xbox`; the default
Xbox subarchitecture is `retail`.

## Current verification

At upstream commit `c22f26c99805430e1b29ea9081929240f20cf540`:

- clean full Xbox KOS build: pass
- 16 public Xbox headers compiled independently as C and C++: pass
- 11 threading examples built and checked for loader-compatible ELF layout:
  pass
- hello, RTC, and SMC examples: pass

Runtime behavior must be revalidated after every upstream merge. Do not infer
runtime support from a successful cross-build alone.
