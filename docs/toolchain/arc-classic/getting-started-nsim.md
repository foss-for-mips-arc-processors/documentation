# Getting Started with nSIM

nSIM supports running and debugging applications built with GNU toolchain for all ARC families.

## Building and Debugging Applications

Suppose that `main.c` contains an application to be debugged on nSIM:

```c
int main()
{
    return 0;
}
```

Then build it (we use `-specs=nosys.specs` if input/output operations are not needed) for ARC HS3x using `-mcpu=hs38`:

```
$ arc-gf-elf-gcc -mcpu=hs38 -specs=nosys.specs -g main.c -o main.elf
```

Start nSIM with a GDB server with 12345 port:

```
$ nsimdrv -tcf $NSIM_HOME/etc/tcf/templates/hs38_full.tcf -gdb -port 12345
```

Run GDB in another terminal and debug the application:

```
$ arc-gf-elf-gdb -quiet main.elf
Reading symbols from main.elf...
(gdb) target remote :12345
Remote debugging using :12345
0x80000000 in ?? ()
(gdb) load
Loading section .init, size 0x22 lma 0x100
Loading section .text, size 0x1548 lma 0x124
Loading section .fini, size 0x16 lma 0x166c
Loading section .ivt, size 0x54 lma 0x1682
Loading section .data, size 0x534 lma 0x36d8
Loading section .ctors, size 0x8 lma 0x3c0c
Loading section .dtors, size 0x8 lma 0x3c14
Loading section .sdata, size 0x10 lma 0x3c1c
Start address 0x00000124, load size 6952
Transfer rate: 188 KB/sec, 869 bytes/write.
(gdb) b main
Breakpoint 1 at 0x276: file main.c, line 3.
(gdb) c
Continuing.

Breakpoint 1, main () at main.c:3
3           return 0;
(gdb)
```

## Building and Running "Hello, World!"

Consider a simple example code (save it as `main.c`):

```c
#include <stdio.h>

int main()
{
    printf("Hello, World!\n");
    return 0;
}
```

You need to use `-specs=nsim.specs` to use input/output features and to pass `-on nsim_emt` option to nSIM
to use ARC GNU input/output protocol:

```
$ arc-gf-elf-gcc -mcpu=archs -specs=nsim.specs main.c -o main.elf
$ nsimdrv -tcf $NSIM_HOME/etc/tcf/templates/hs38_full.tcf -on nsim_emt main.elf
Hello, World!
```

You can use MetaWare's own hostlink protocol for input/output operations by passing `-specs=hl.specs` to GCC.
In this case you don't have to pass any additional options to nSIM:

```
$ arc-gf-elf-gcc -mcpu=archs -specs=hl.specs main.c -o main.elf
$ nsimdrv -tcf $NSIM_HOME/etc/tcf/templates/hs38_full.tcf main.elf
Hello, World!
```

## Choosing nSIM Templates

Different GCC options for ARC targets require different nSIM options for successful
execution of binaries. You can find them on corresponding pages for [ARCompact](./arcompact.md),
[ARCv2 HS](./arcv2.md#compatibility-for-predefined-targets-for-arc-hs),
[ARCv2 EM](./arcv2.md#compatibility-for-predefined-targets-for-arc-em) and
[ARCv3](./arcv3.md#compatibility-for-predefined-targets).

You can match `-mcpu` values for GCC with corresponding `-tcf` templates for nSIM using a table
below.

| Compiler             | `-mcpu`      | TCF             | Additional nSIM options                                                                                        |
|----------------------|--------------|-----------------|----------------------------------------------------------------------------------------------------------------|
| `arc-gf-elf-gcc`   | `hs`         | `hs36_base.tcf` |                                                                                                                |
| `arc-gf-elf-gcc`   | `hs34`       | `hs36.tcf`      |                                                                                                                |
| `arc-gf-elf-gcc`   | `hs38`       | `hs38_full.tcf` |                                                                                                                |
| `arc-gf-elf-gcc`   | `hs38_linux` | `hs38_full.tcf` | `-on nsim_isa_fpud_option -on nsim_isa_fpud_div_option -on nsim_isa_fpu_mac_option -on nsim_isa_fpu_hp_option` |
| `arc-gf-elf-gcc`   | `archs`      | `hs38_full.tcf` | `-p nsim_isa_mpy_option=2`                                                                                     |
| `arc-gf-elf-gcc`   | `em`         | `em6_mini.tcf`  | `-p nsim_isa_shift_option=3 -p nsim_isa_rgf_num_regs=32`                                                       |
| `arc-gf-elf-gcc`   | `em4`        | `em6_mini.tcf`  | `-p nsim_isa_shift_option=3 -p nsim_isa_rgf_num_regs=32`                                                       |
| `arc-gf-elf-gcc`   | `em4_dmips`  | `em6_dmips.tcf` |                                                                                                                |
| `arc-gf-elf-gcc`   | `em4_fpus`   | `em6_dmips.tcf` | `-on nsim_isa_fpus_option`                                                                                     |
| `arc-gf-elf-gcc`   | `em4_fpuda`  | `em6_dmips.tcf` | `-on nsim_isa_fpus_option -on nsim_isa_fpuda_option`                                                           |
| `arc-gf-elf-gcc`   | `arcem`      | `em6_dmips.tcf` | `-off nsim_isa_bitscan_option -off nsim_isa_div_rem_option`                                                    |
| `arc-gf-elf-gcc`   | `arc700`     | `arc770d.tcf`   |                                                                                                                |
| `arc-gf-elf-gcc`   | `arc600`     | `arc625d.tcf`   |                                                                                                                |
| `arc64-gf-elf-gcc` | `hs5x`       | `hs58_full.tcf` | `-on nsim_isa_ll64_option`                                                                                     |
| `arc64-gf-elf-gcc` | `hs58`       | `hs58_full.tcf` | `-on nsim_isa_ll64_option`                                                                                     |
| `arc64-gf-elf-gcc` | `hs6x`       | `hs68_full.tcf` |                                                                                                                |
| `arc64-gf-elf-gcc` | `hs68`       | `hs68_full.tcf` |                                                                                                                |

## Linking with Size-Optimized Libraries

You can link your application with size-optimized versions of libraries
using `-specs=nano.specs`:

```
$ arc-gf-elf-gcc -mcpu=archs -specs=hl.specs -specs=nano.specs main.c -o main.elf
$ size main.elf
   text    data     bss     dec     hex filename
   5336    1564     472    7372    1ccc main.elf
```

Compare code size:

```
$ arc-gf-elf-gcc -mcpu=archs -specs=hl.specs main.c -o main.elf
$ size main.elf
   text    data     bss     dec     hex filename
  12396    2848     788   16032    3ea0 main.elf
```

## Building for Big Endian Targets

To produce binaries for big-endian targets it's necessary to use `arceb-*`
toolchains (except ARCv3 toolchain which does not support big endian).
Also, you need to pass `-on nsim_isa_big_endian` to nSIM for big endian targets:

```
$ arceb-gf-elf-gcc -mcpu=archs -specs=nsim.specs main.c -o main.elf
$ nsimdrv -tcf $NSIM_HOME/etc/tcf/templates/hs38_full.tcf -on nsim_emt -on nsim_isa_big_endian main.elf
Hello, World!
```

## Specs Files

There is a set of files called _specs files_ with `.specs` extension. They are
shipped with Newlib library in the baremetal toolchain. A specs file for a
particular platform (for example, a simulator or a development board) is
responsible for linking the application with specific startup code and
additional libraries.

For example, a specs file for HS Development Kit is called `hsdk.specs` and
it does this when it's used:

1. Link an application with a custom linker script that represents HSDK's
   [memory map](./memory.md).
2. Link an application with a custom startup code.
3. Link an application with UART library for input/output operations.

You can use specs file using `-specs=` option:

```
$ arc-elf32-gcc -mcpu=archs -specs=hsdk.specs main.c -o main.elf
```

There is a list of general specs files:

* `nosys.specs` - link stubs for system calls.
* `nsim.specs` - link with GNU hostlink library for nSIM simulator (with `-on nsim_emt`) or QEMU (`-semihosting`).
* `hl.specs` - link with MetaWare hostlink library for nSIM simulator (default hostlink mode).
* `nano.specs` - use Newlib nano instead of default Newlib implementation. May be used
  in conjunction with other specs file. For example: `-specs=nano.specs -specs=nsim.specs`. 

Also, there are specs files for various development platforms:

* [HS Development Kit](../../platforms/board-hsdk.md)
* [EM Software Development Platform](../../platforms/board-emsdp.md)

## Heap and Stack Size

To change size of heap in baremetal applications the following option should be specified to the linker:
`--defsym=__DEFAULT_HEAP_SIZE=${SIZE}`, where `${SIZE}` is desired heap size, in bytes. It also possible to use
size suffixes, like `k` and `m` to specify size in kilobytes and megabytes respectively. For stack size respective
option is `--defsym=__DEFAULT_STACK_SIZE=${STACK_SIZE}`. Note that those are linker commands - they are valid only
when passed to `ld` application, if `gcc` driver is used for linking, then those options should be prefixed with
`-Wl`. For example:

```
$ arc-elf32-gcc \
    -Wl,--defsym=__DEFAULT_HEAP_SIZE=256m \
    -Wl,--defsym=__DEFAULT_STACK_SIZE=1024m
    --specs=nosys.specs \
    hello.o -o hello.bin
```

Those options are valid only when default linker script is used. If custom linker script is used, then effective
way to change stack/heap size depends on properties of that linker script - it might be the same, or it might be
different.
