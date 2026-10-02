# Running on QEMU

## Installing QEMU

Detailed instructions for building on Linux are on [QEMU's Wiki](https://wiki.qemu.org/Hosts/Linux).
First you need to install required additional packages, which depend on the Linux distribution used
on the host.

Install necessary packages for AlmaLinux 8:

```shell
sudo dnf group install "Development Tools"
sudo dnf \
    install git glib2-devel libfdt-devel pixman-devel \
    zlib-devel bzip2 ninja-build python3 python3-tomli \
    python38
```

Install necessary packages for Ubuntu 22.04:

```shell
sudo apt install \
    git cmake ninja-build gperf ccache \
    dfu-util device-tree-compiler wget \
    python3-pip python3-setuptools python3-wheel \
    xz-utils file make gcc gcc-multilib pkg-config \
    libglib2.0-dev libpixman-1-dev zlib1g-dev
```

After installation of prerequisites you can download sources, configure and build in the following way
(use your own `--prefix` value for installation path):

```
# Clone v9.0 from GitHub repository
$ git clone --depth 1 -b stable-9.0 https://github.com/qemu/qemu.git
$ cd qemu

# Configure QEMU for 32-bit and 64-bit RISC-V targets
$ ./configure \
        --target-list=riscv32-softmmu,riscv64-softmmu \
        --prefix=$HOME/qemu

# Build and install QEMU
$ make
$ make install
```

After the build is complete `qemu-system-riscv32` and `qemu-system-riscv64` are placed in
`$HOME/qemu/bin$` folder, which you can add to the `PATH` environment variable for convenience,
and use the short binary name instead of the full path.

## Code Example

Consider this code example which prints all passed command
line arguments:

```c
#include <stdio.h>

int main(int argc, char *argv[]) {
    for (int i = 0; i < argc; i++) {
        printf("%d: %s\n", i, argv[i]);
    }
    return 0;
}
```

## Build and Run for Base RMX-100

Build with Picolibc-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv32imac_zcb_zba_zbb_zbs \
        -mabi=ilp32 \
        -mtune=arc-v-rmx-100-series \
        -Wl,-defsym=__flash=0x80000000 \
        -Wl,-defsym=__flash_size=1M \
        -Wl,-defsym=__ram=0x80200000 \
        -Wl,-defsym=__ram_size=1M \
        -specs=picolibc.specs \
        --oslib=semihost \
        --crt0=semihost \
        args.c -o args.elf
```

Build with Newlib-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv32imac_zcb_zba_zbb_zbs \
        -mabi=ilp32 \
        -mtune=arc-v-rmx-100-series \
        -Wl,-defsym=txtmem_addr=0x80000000 \
        -Wl,-defsym=txtmem_len=1M \
        -Wl,-defsym=datamem_addr=0x80200000 \
        -Wl,-defsym=datamem_len=1M \
        -specs=semihost.specs \
        -specs=arcv.specs \
        --crt0=no-csr \
        -T arcv.ld \
        args.c -o args.elf
```

Run on QEMU:

```
$ qemu-system-riscv32 \
        -semihosting \
        -nographic \
        -machine virt \
        -cpu rv32,f=off,zfa=off,d=off,zce=on,zba=on,zbb=on,zbs=on \
        -bios none \
        -kernel args.elf \
        -append "one two three"
0: args.elf
1: one
2: two
3: three
```

## Build and Run for Base RMX-500

Build with Picolibc-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv32imac_zcb_zba_zbb_zbs \
        -mabi=ilp32 \
        -mtune=arc-v-rmx-500-series \
        -Wl,-defsym=__flash=0x80000000 \
        -Wl,-defsym=__flash_size=1M \
        -Wl,-defsym=__ram=0x80200000 \
        -Wl,-defsym=__ram_size=1M \
        -specs=picolibc.specs \
        --oslib=semihost \
        --crt0=semihost \
        args.c -o args.elf
```

Build with Newlib-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv32imac_zcb_zba_zbb_zbs \
        -mabi=ilp32 \
        -mtune=arc-v-rmx-500-series \
        -Wl,-defsym=txtmem_addr=0x80000000 \
        -Wl,-defsym=txtmem_len=1M \
        -Wl,-defsym=datamem_addr=0x80200000 \
        -Wl,-defsym=datamem_len=1M \
        -specs=semihost.specs \
        -specs=arcv.specs \
        --crt0=no-csr \
        -T arcv.ld \
        args.c -o args.elf
```

Run on QEMU:

```
$ qemu-system-riscv32 \
        -semihosting \
        -nographic \
        -machine virt \
        -cpu rv32,f=off,zfa=off,d=off,zce=on,zba=on,zbb=on,zbs=on \
        -bios none \
        -kernel args.elf \
        -append "one two three"
0: args.elf
1: one
2: two
3: three
```

## Build and Run for Base RMX-700

Build with Picolibc-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv32imac_zcb_zba_zbb_zbs \
        -mabi=ilp32 \
        -mtune=arc-v-rmx-700-series \
        -Wl,-defsym=__flash=0x80000000 \
        -Wl,-defsym=__flash_size=1M \
        -Wl,-defsym=__ram=0x80200000 \
        -Wl,-defsym=__ram_size=1M \
        -specs=picolibc.specs \
        --oslib=semihost \
        --crt0=semihost \
        args.c -o args.elf
```

Build with Newlib-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv32imac_zcb_zba_zbb_zbs \
        -mabi=ilp32 \
        -mtune=arc-v-rmx-700-series \
        -Wl,-defsym=txtmem_addr=0x80000000 \
        -Wl,-defsym=txtmem_len=1M \
        -Wl,-defsym=datamem_addr=0x80200000 \
        -Wl,-defsym=datamem_len=1M \
        -specs=semihost.specs \
        -specs=arcv.specs \
        --crt0=no-csr \
        -T arcv.ld \
        args.c -o args.elf
```

Run on QEMU:

```
$ qemu-system-riscv32 \
        -semihosting \
        -nographic \
        -machine virt \
        -cpu rv32,f=off,zfa=off,d=off,zce=on,zba=on,zbb=on,zbs=on \
        -bios none \
        -kernel args.elf \
        -append "one two three"
0: args.elf
1: one
2: two
3: three
```

## Build and Run for Base RHX-100

Build with Picolibc-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv32imafc_zcb_zba_zbb_zbs \
        -mabi=ilp32f \
        -mtune=arc-v-rhx-100-series \
        -Wl,-defsym=__flash=0x80000000 \
        -Wl,-defsym=__flash_size=1M \
        -Wl,-defsym=__ram=0x80200000 \
        -Wl,-defsym=__ram_size=1M \
        -specs=picolibc.specs \
        --oslib=semihost \
        --crt0=semihost \
        args.c -o args.elf
```

Build with Newlib-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv32imafc_zcb_zba_zbb_zbs \
        -mabi=ilp32f \
        -mtune=arc-v-rhx-100-series \
        -Wl,-defsym=txtmem_addr=0x80000000 \
        -Wl,-defsym=txtmem_len=1M \
        -Wl,-defsym=datamem_addr=0x80200000 \
        -Wl,-defsym=datamem_len=1M \
        -specs=semihost.specs \
        -specs=arcv.specs \
        --crt0=no-csr \
        -T arcv.ld \
        args.c -o args.elf
```

Run on QEMU:

```
$ qemu-system-riscv32 \
        -semihosting \
        -nographic \
        -machine virt \
        -cpu rv32,f=on,zfa=off,d=off,m=on,a=on,zca=on,zcf=on,zcb=on,zcmp=on,zba=on,zbb=on,zbs=on \
        -bios none \
        -kernel args.elf \
        -append "one two three"
0: args.elf
1: one
2: two
3: three
```

## Build and Run for Base RPX-100

Build with Picolibc-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv64imafdc_zcb_zba_zbb_zbs \
        -mabi=lp64d \
        -mtune=arc-v-rpx-100-series \
        -mcmodel=medany \
        -Wl,-defsym=__flash=0x80000000 \
        -Wl,-defsym=__flash_size=1M \
        -Wl,-defsym=__ram=0x80200000 \
        -Wl,-defsym=__ram_size=1M \
        -specs=picolibc.specs \
        --oslib=semihost \
        --crt0=semihost \
        args.c -o args.elf
```

Build with Newlib-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv64imafdc_zcb_zba_zbb_zbs \
        -mabi=lp64d \
        -mtune=arc-v-rpx-100-series \
        -mcmodel=medany \
        -Wl,-defsym=txtmem_addr=0x80000000 \
        -Wl,-defsym=txtmem_len=1M \
        -Wl,-defsym=datamem_addr=0x80200000 \
        -Wl,-defsym=datamem_len=1M \
        -specs=semihost.specs \
        -specs=arcv.specs \
        --crt0=no-csr \
        -T arcv.ld \
        args.c -o args.elf
```

Run on QEMU:

```
$ qemu-system-riscv64 \
        -semihosting \
        -nographic \
        -machine virt \
        -cpu rv64,f=on,zfa=off,d=on,m=on,a=on,zca=on,zcd=on,zcb=on,zba=on,zbb=on,zbs=on \
        -bios none \
        -kernel args.elf \
        -append "one two three"
0: args.elf
1: one
2: two
3: three
```

## Debugging Using GDB

You can debug an application that is being run on QEMU with help of the built-in GDB server of
QEMU. On the QEMU side, you need to enable the GDB sever and optionally instruct the QEMU not 
to start the application execution automatically, but instead wait for user input. For that you
need to pass `-s -S` commands to `qemu-system-riscv32` or `qemu-system-riscv64`.

Build with Picolibc-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv32imac_zcb_zba_zbb_zbs \
        -mabi=ilp32 \
        -mtune=arc-v-rmx-100-series \
        -Wl,-defsym=__flash=0x80000000 \
        -Wl,-defsym=__flash_size=1M \
        -Wl,-defsym=__ram=0x80200000 \
        -Wl,-defsym=__ram_size=1M \
        -specs=picolibc.specs \
        --oslib=semihost \
        --crt0=semihost \
        -g \
        args.c -o args.elf
```

Build with Newlib-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv32imac_zcb_zba_zbb_zbs \
        -mabi=ilp32 \
        -mtune=arc-v-rmx-100-series \
        -Wl,-defsym=txtmem_addr=0x80000000 \
        -Wl,-defsym=txtmem_len=1M \
        -Wl,-defsym=datamem_addr=0x80200000 \
        -Wl,-defsym=datamem_len=1M \
        -specs=semihost.specs \
        -specs=arcv.specs \
        --crt0=no-csr \
        -T arcv.ld \
        -g \
        args.c -o args.elf
```

Start GDB server (we use `-gdb tcp::12345` instead of `-s` to set the custom
GDB port number instead of the default 1234 port number):

```
$ qemu-system-riscv32 \
        -semihosting \
        -nographic \
        -machine virt \
        -cpu rv32,f=off,zfa=off,d=off,zce=on,zba=on,zbb=on,zbs=on \
        -bios none \
        -kernel args.elf \
        -append "one two three" \
        -gdb tcp::12345 -S
```

Start GDB session:

```
$ riscv64-gf-elf-gdb -q args.elf
Reading symbols from args.elf...
(gdb) target remote :12345
Remote debugging using :12345
0x00001000 in ?? ()
(gdb) b main
Breakpoint 1 at 0x800000e0: file args.c, line 4.
(gdb) c
Continuing.

Breakpoint 1, main (argc=4, argv=0x8020007c <argv>) at args.c:4
4	    for (int i = 0; i < argc; i++) {
(gdb) step
5	        printf("%d: %s\n", i, argv[i]);
(gdb) c
Continuing.
[Inferior 1 (process 1) exited normally]
```

Note that the first location where the CPU is waiting for commands is `0x00001000`. It’s a
`virt` board reset vector location. It is not possible to suppress execution of that reset
vector code as it’s an essential part of the QEMU board (think of it as a boot-ROM). You
can only bypass it if you load the target executable from the GDB client with the load command.
Then execution starts from the loaded application's entry point.
