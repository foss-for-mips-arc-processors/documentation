# Getting Started with Picolibc

## Overview

Experimental support of [Picolibc](https://github.com/picolibc/picolibc) for
ARC Classic targets is introduced in `arc-2026.03` release of GNU
toolchains for ARC processors.

Below you can find a series of examples of using it for building and
running applications for ARC Classic targets.

Check [Choosing nSIM Templates](./getting-started-nsim.md#choosing-nsim-templates)
for information about choosing correct target options.

## Building and Running for nSIM

Consider this simple example with `hello.c` filename:

```c
#include <stdio.h>

int main()
{
    printf("Hello, World!\n");
    return 0;
}
```

Build the application with GNU nSIM interface for input/output and run it:

```
$ arc-gf-elf-gcc \
    -mcpu=archs \
    -specs=picolibc.specs \
    --crt0=semihost \
    --oslib=semihost \
    hello.c -o hello.elf

$ nsimdrv -tcf $NSIM_HOME/etc/tcf/templates/hs38_full.tcf -on nsim_emt hello.elf
Hello, World!
```

Build the application with Hostlink nSIM interface for input/output and run it:

```
$ arc-gf-elf-gcc \
    -mcpu=archs \
    -specs=picolibc.specs \
    --crt0=hl \
    --oslib=hl \
    hello.c -o hello.elf

$ nsimdrv -tcf $NSIM_HOME/etc/tcf/templates/hs38_full.tcf hello.elf
Hello, World!
```

Build with a size-optimized version of Picolibc:

```
$ arc-gf-elf-gcc \
    -mcpu=archs \
    -specs=picolibc.specs \
    --crt0=semihost \
    --oslib=semihost \
    -specs=nano.specs \
    hello.c -o hello.elf
```

Build with custom memory layout, heap size and stack size:

```
$ arc-gf-elf-gcc \
    -mcpu=archs \
    -specs=picolibc.specs \
    --crt0=semihost \
    --oslib=semihost \
    -Wl,-defsym=__f4lash=0x0 \
    -Wl,-defsym=__flash_size=16M \
    -Wl,-defsym=__ram=0x80000000 \
    -Wl,-defsym=__ram_size=16M \
    -Wl,-defsym=__heap_size_min=32M \
    -Wl,-defsym=__stack_size=32M \
    hello.c -o hello.elf
```

## Compiling C++ Applications

Consider a simple code example with `hello.cpp` filename:

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
$ arc-gf-elf-g++ \
    -mcpu=archs \
    -specs=picolibcpp.specs \
    --crt0=semihost \
    --oslib=semihost \
    -Wl,--defsym=__flash_size=4M \
    -Wl,--defsym=__ram_size=4M \
    hello.cpp -o hello.elf

$ nsimdrv -tcf $NSIM_HOME/etc/tcf/templates/hs38_full.tcf -on nsim_emt hello.elf
Hello, World!
```

## Building and Running for Boards

Build for [ARC HS Development Kit](../../platforms/board-hsdk.md):

```
$ arc-gf-elf-gcc \
    -mcpu=hs38_linux \
    -specs=picolibc.specs \
    -specs=hsdk.specs \
    --crt0=hosted \
    --oslib=hsdk \
    hello.c -o hello.elf
```

Build for [ARC EM Software Development Platform 1.0 and 1.1](../../platforms/board-emsdp.md):

```
$ arc-gf-elf-gcc \
    -mcpu=em4_fpuda \
    -mmpy-option=6 \
    -mfpu=fpuda_all \
    -specs=picolibc.specs \
    -specs=emsdp1.1.specs \
    --crt0=hosted \
    --oslib=emsdp \
    hello.c -o hello.elf
```

Build for [ARC EM Software Development Platform 1.2](../../platforms/board-emsdp.md):

```
$ arc-gf-elf-gcc \
    -mcpu=em4_fpuda \
    -mmpy-option=6 \
    -mfpu=fpuda_all \
    -specs=picolibc.specs \
    -specs=emsdp1.2.specs \
    --crt0=hosted \
    --oslib=emsdp \
    hello.c -o hello.elf
```
