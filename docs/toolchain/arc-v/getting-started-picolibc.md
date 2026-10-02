# Getting Started with Picolibc

## Getting Started

Consider a simple code example:

```c
#include <stdio.h>

int main()
{
        printf("Hello, World!\n");
        return 0;
}
```

To compile an application you need to set target options:

1. `-march` - stands for RISC-V ISA.
2. `-mabi` - stands for ABI.
3. `-mtune` - stands for a particular GCC instruction scheduling optimization,
   refer [Tuning Instruction Scheduling](#tuning-instruction-scheduling)
   for ARC-V specific values .
4. `-mcmodel` - `medlow` for `rv32` targets and `medany` for `rv64` targets.

All together they correspond to a particular prebuilt standard library. Refer
[Understanding ARC-V configurations](./multilib.md) for details.

If I want to build it for the base RMX-100 target and link with 
a semihosting library, I would use this set of options:

```
$ riscv64-gf-elf-gcc \
        -march=rv32ic_zcb_zba_zbb_zbs \
        -mabi=ilp32 \
        -mtune=arc-v-rmx-100-series \
        -specs=picolibc.specs \
        --crt0=semihost \
        --oslib=semihost \
        example.c -o example.elf
```

Passing `-specs=picolibc.specs` for linking with Picolibc is mandatory:

1. It allows choosing a proper variant of `crt0` startup file through `--crt0=` option ([see all variants below](#choosing-a-crt0-variant)).
2. It allows choosing a proper variant of a system library through `--oslib=` option ([see all variants below](#choosing-a-system-library)).
3. It chooses a custom linker script crafted for Picolibc (`picolibc.ld`).

In turn, when `picolibc.ld` linker script is used, it's possible to configure
placement and size of code and data sections:

1. Set offset of code sections through `-Wl,--defsym=__flash=` option.
2. Set size of code sections through `-Wl,--defsym=__flash_size=` option.
3. Set offset of data sections through `-Wl,--defsym=__ram=` option.
4. Set size of data sections through `-Wl,--defsym=__ram_size=` option.

Here is a full example with custom code and data sections:

```
$ riscv64-gf-elf-gcc \
        -march=rv32ic_zcb_zba_zbb_zbs \
        -mabi=ilp32 \
        -mtune=arc-v-rmx-100-series \
        -specs=picolibc.specs \
        --crt0=semihost \
        --oslib=semihost \
        -Wl,--defsym=__flash=0x80000000 \
        -Wl,--defsym=__flash_size=128K \
        -Wl,--defsym=__ram=0x80020000 \
        -Wl,--defsym=__ram_size=128K \
        example.c -o example.elf
```

Run the application using nSIM:

```
$ nsimdrv -p nsim_isa_family=rv32 -p nsim_isa_ext=-all.i.c.zcb.zba.zbb.zbs.zicsr -p nsim_semihosting=1 example.elf
Hello, World!
```

Refer to [Running on nSIM](./running-on-nsim.md) and [Running on QEMU](./running-on-qemu.md) to learn how
to run ARC-V examples on nSIM or QEMU simulator.

## Compiling C++ Applications

Consider a simple code example with `example.cpp` filename:

```c
#include <iostream>

int main()
{
        std::cout << "Hello, World!" << std::endl;
        return 0;
}
```

For compiling C++ applications use `-specs=picolibcpp.specs`:

```
$ riscv64-gf-elf-g++ \
        -march=rv32ic_zcb_zba_zbb_zbs \
        -mabi=ilp32 \
        -mtune=arc-v-rmx-100-series \
        -specs=picolibcpp.specs \
        --crt0=semihost \
        --oslib=semihost \
        -Wl,--defsym=__flash_size=4M \
        -Wl,--defsym=__ram_size=4M \
        example.cpp -o example.elf

$ nsimdrv -p nsim_isa_family=rv32 -p nsim_isa_ext=-all.i.c.zcb.zba.zbb.zbs.zicsr -p nsim_semihosting=1 example.elf
Hello, World!
```

## Using Size Optimized Picolibc Variant

Pass `-specs=nano.specs` option to link an application with a
size optimized Picolibc variant:

```
$ riscv64-gf-elf-gcc \
        -march=rv32ic_zcb_zba_zbb_zbs \
        -mabi=ilp32 \
        -mtune=arc-v-rmx-100-series \
        -specs=picolibc.specs \
        -specs=nano.specs \
        --crt0=semihost \
        --oslib=semihost \
        example.c -o example.elf
```

The table below summarizes differences between the regular Picolibc
and size optimized one.

| Feature                                          | Picolibc | Picolibc Nano |
|--------------------------------------------------|----------|---------------|
| Optimization level                               | `-O3`    | `-Os`         |
| Support of `long long` format in i/o functions   | Yes      | No            |
| Support of `long double` format in i/o functions | Yes      | No            |
| Support of `%b` format in i/o functions          | Yes      | No            |
| Support of `%n` format in i/o functions          | Yes      | No            |
| Faster buffered i/o operations                   | Yes      | No            |
| Multibyte support for UTF-8 charset              | Yes      | No            |
| Wide character support in `printf`/`scanf`       | Yes      | No            |

## Choosing a crt0 Variant

A variant of a startup file (`crt0.o`) is chosen through `--crt0=` option. See a list of all supported values for this option below.

| Option                                | Uses CSR instructions | Returns exit code | Calls constructors | Reads command line arguments | Sets traps | Initializes ARC-V features |
|---------------------------------------|-----------------------|-------------------|--------------------|------------------------------|------------|----------------------------|
| Not passing `--crt0=` value (default) | No                    | No                | __Yes__            | No                           | No         | No                         |
| `--crt0=hosted`                       | No                    | __Yes__           | __Yes__            | No                           | No         | No                         |
| `--crt0=minimal`                      | No                    | No                | No                 | No                           | No         | No                         |
| `--crt0=semihost`                     | No                    | __Yes__           | __Yes__            | __Yes__                      | No         | No                         |
| `--crt0=semihost-trap`                | __Yes__               | __Yes__           | __Yes__            | __Yes__                      | __Yes__    | No                         |
| `--crt0=arcv-hosted`                  | __Yes__               | __Yes__           | __Yes__            | No                           | No         | __Yes__                    |
| `--crt0=arcv-minimal`                 | __Yes__               | No                | No                 | No                           | No         | __Yes__                    |
| `--crt0=arcv-semihost`                | __Yes__               | __Yes__           | __Yes__            | __Yes__                      | No         | __Yes__                    |
| `--crt0=arcv-semihost-trap`           | __Yes__               | __Yes__           | __Yes__            | __Yes__                      | __Yes__    | __Yes__                    |

Empty `crt0` variant corresponds to `--crt0=none`. All `arcv-*` variants enable all available ARC-V caches
and support of RVV if it's present.

## Choosing a system library

So far, Picolibc supports 2 system libraries, and it's chosen through `--oslib=` option:

| Option              | Description                                               |
|---------------------|-----------------------------------------------------------|
| `--oslib=nosys`     | A simple environment without input/output and exit codes. |
| `--oslib=semihost`  | Support of Semihosting environment.                       |

## Tuning Instruction Scheduling

GCC instruction scheduling may be tuned for different ARC-V
targets using `-mtune=` option:

| Feature                      | Option                        |
|------------------------------|-------------------------------|
| Tune for RMX-100 targets     | `-mtune=arc-v-rmx-100-series` |
| Tune for RMX-500 targets     | `-mtune=arc-v-rmx-500-series` |
| Tune for RMX-700 targets     | `-mtune=arc-v-rmx-700-series` |
| Tune for RHX-100 targets     | `-mtune=arc-v-rhx-100-series` |
| Tune for RPX-100 targets     | `-mtune=arc-v-rpx-100-series` |
| Tune for Atlas M8500 targets | `-mtune=mips-m8500`           |

For RMX-100 targets it's also possible to choose a version of MPY unit using `-param=arcv-mpy-option=` option:

* `-param=arcv-mpy-option=1c`
* `-param=arcv-mpy-option=2c` (default)
* `-param=arcv-mpy-option=10c`

You can choose a number of cycles that a word-size integer load operation takes
(from 1 to 3, 3 is default) using `--param=arcv-ld-cycles=<1,3>` option.

Note that all `-mtune` values for ARC-V assume that fast unaligned access is supported
(`__riscv_misaligned_fast == 1`) by targets. This assumption may be overwritten by
`-mstrict-align` when building an application or toolchain libraries itself.

## Using the TCF Wrapper

[The TCF Wrapper](https://github.com/foss-for-mips-arc-processors/arcv-tcf-wrapper?tab=readme-ov-file#the-tcf-wrapper) allows
using TCF configuration files for building binaries using the GNU toolchain.

Suppose that the environment is configured for nSIM and `NSIM_HOME` variable is set.
Here is an example of using the TCF wrapper with `rmx100_dmips.tcf` configuration file:

```
$ riscv64-gf-elf-tcf-gcc \
        -tcf=$NSIM_HOME/etc/tcf/templates/rmx100_dmips.tcf \
        -tcf-with-memory-defines \
        -specs=picolibc.specs \
        --oslib=semihost \
        --crt0=semihost \
        example.c -o example.elf

$ nsimdrv -tcf=$NSIM_HOME/etc/tcf/templates/rmx100_dmips.tcf -p nsim_semihosting=1 example.elf
Hello, World!
```

## Migrating from Newlib

### Build with Semihosting and ARC-V Features

Using Newlib:

```
...
-specs=semihost.specs \
-specs=arcv.specs \
-T arcv.ld \
...
```

Using Picolibc:

```
...
-specs=picolibc.specs \
--oslib=semihost \
--crt0=arcv-semihost \
...
```

### Build with Semihosting and Without ARC-V Features

Using Newlib:

```
...
-specs=semihost.specs \
-specs=arcv.specs \
-T arcv.ld \
--crt0=no-csr \
...
```

Using Picolibc:

```
...
-specs=picolibc.specs \
--oslib=semihost \
--crt0=semihost \
...
```

### Set Offset and Size of Text and Data Sections

Using Newlib:

```
...
-specs=semihost.specs \
-specs=arcv.specs \
-T arcv.ld \
-Wl,-defsym=txtmem_addr=0x80000000 \
-Wl,-defsym=txtmem_len=128K \
-Wl,-defsym=datamem_addr=0x80020000 \
-Wl,-defsym=datamem_len=128K \
...
```

Using Picolibc:

```
...
-specs=picolibc.specs \
--oslib=semihost \
--crt0=arcv-semihost \
-Wl,--defsym=__flash=0x80000000 \
-Wl,--defsym=__flash_size=128K \
-Wl,--defsym=__ram=0x80020000 \
-Wl,--defsym=__ram_size=128K \
...
```
