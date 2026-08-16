# Xbox "hello world"

A freestanding loader diagnostic for the original Microsoft Xbox. It prints
`Hello, Xbox!` over the network via kos-tool's `xbox-load-ip` without linking
against KOS.

## How it works

This deliberately remains a freestanding **guest** so it can test the loader
ABI independently of the KOS Xbox runtime. It uses the same ABI as kos-tool's
own `console-test` / `xbox-video-test` examples:

- `xbox-load-ip` publishes a header at `XBOX_KOSLOAD_BASE` (`0x00011000`):
  the magic `0xdeadbeef` at `+0` and a syscall trampoline pointer at `+4`.
- `hello.c`'s `start()` verifies the magic, then calls
  `syscall(WRITE, 1, "Hello, Xbox!\n", ...)` to print to the host console and
  `syscall(EXIT, 0, ...)` to hand control back.

## Build

Uses the `i686-pc-xbox` cross toolchain directly. It links a symbol-rich PE
image at the guest load address (`0x00400000`) and `objcopy`s a stripped
runtime-only copy to `elf32-i386`, which is what the loader consumes. Keep the
`.exe` for debugging; the uploadable `.elf` deliberately excludes PE
relocations and DWARF sections because xbox-load-ip transfers address-bearing
sections into guest memory:

```sh
make                                                   # -> hello.elf
make XBOX_TARGET=/opt/toolchains/xbox/i686-pc-xbox/bin/i686-pc-xbox
```

## Run

With `xbox-load-ip` running on the Xbox, upload and execute from the host:

```sh
make run          # = kos-tool -x hello.elf
```

`Hello, Xbox!` should appear on the host console.

## Relationship to the KOS port

This example exercises the raw **loader-guest** path. KOS-linked Xbox examples
now use `kernel/arch/xbox`'s `startup.S`, `utils/ldscripts/xbox.ld`, the shared
KOSLoad console driver, libc, and the threading runtime. Keeping this smaller
diagnostic is useful because it can distinguish loader failures from KOS
runtime failures. Neither path currently provides a local video console.
