# Getting Started with QEMU

## Installing QEMU

!!! info "Additional packages and plugins"

    Refer to [the official documentation](https://wiki.qemu.org/Hosts/Linux#Required_additional_packages) for details about
    installing additional packages and enabling support of QEMU plugins.

Install necessary packages for AlmaLinux 8:

```shell
sudo dnf group install "Development Tools"
sudo dnf \
    install git glib2-devel libfdt-devel pixman-devel \
    zlib-devel bzip2 ninja-build python3 python3-tomli
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

Then prepare sources and a build directory:

```bash
git clone -b arc-2026.09 https://github.com/foss-for-mips-arc-processors/qemu
mkdir -p qemu/build
cd qemu/build
```

Configure QEMU inside of the build directory (use your own `--prefix` value for installation path):

```
../configure \
    --target-list=arc-softmmu,arc64-softmmu,arc-linux-user,arc64-linux-user \
    --prefix=/tools/qemu \
    --enable-debug \
    --enable-debug-tcg \
    --enable-trace-backends=simple \
    --disable-plugins \
    --skip-meson \
    --disable-pie
```

If you face an error then consider using `--disable-werror` configure option to
try to eliminate it. Also, consider reporting a bug on
[toolchain's GitHub page](https://github.com/foss-for-mips-arc-processors/toolchain/issues/new).

What options are responsible for what:

* `--target-list=arc-softmmu,arc64-softmmu,arc-linux-user,arc64-linux-user` — build QEMU both for these targets:
  * `qemu-system-arc` — system emulation for ARC HS3x/4x/5x processors family;
  * `qemu-system-arc64` — system emulation for ARC HS6x processors family;
  * `qemu-arc` — user space Linux emulation for ARC HS3x/4x/5x processors family;
  * `qemu-arc64` — user space Linux emulation for ARC HS6x processors family.
* `--prefix=/tools/qemu` — an installation path.
* `--enable-debug --enable-debug-tcg --enable-trace-backends=simple --disable-plugins` — options for development needs.
* `--enable-trace-backends=simple` — for tracing (described in [[Profiling with QEMU]]).
* `--skip-meson` — do not run Meson on every build.
* `--disable-werror` — in case QEMU emits unexpected warnings.
* `--disable-pie` — needed for older GCC (like in CentOS 7).

Build and install:

```shell
make
make install
```

Configure your environment:

```bash
export QEMU_HOME="/tools/qemu"
export PATH="${QEMU_HOME}/bin:$PATH"
```

## Building and Debugging Applications

QEMU supports running and debugging applications for ARC HS3x/HS4x, HS5x and HS6x families. Here is a
table of tools and options for a particular family:

| CPU family    | Toolchain       | `-mcpu=`      | QEMU binary         | ARC-specific QEMU options |
|---------------|-----------------|---------------|---------------------|---------------------------|
| ARC HS3x/HS4x | `arc-elf32-gcc` | `-mcpu=archs` | `qemu-system-arc`   | `-M arc-sim -cpu archs`   |
| ARC HS5x      | `arc64-elf-gcc` | `-mcpu=hs5x`  | `qemu-system-arc`   | `-M arc-sim -cpu hs5x`    |
| ARC HS6x      | `arc64-elf-gcc` | `-mcpu=hs6x`  | `qemu-system-arc64` | `-M arc-sim -cpu hs6x`    |

Suppose that `main.c` contains an application to be debugged on QEMU for ARC HS3x/4x:

```c
int main()
{
    return 0;
}
```

Then build it (we use `-specs=nosys.specs` if input/output operations are not needed):

```shell
arc-elf32-gcc -mcpu=archs -specs=nosys.specs -g main.c -o main.elf
```

Start a GDB server in port 1234 (this is the default port, so we could use the alias `-s` instead of `-gdb tcp::1234`):

```shell
qemu-system-arc -M arc-sim \
                -cpu archs \
                -monitor none \
                -display none \
                -nographic \
                -no-reboot \
                -gdb tcp::1234 -S \
                -kernel main.elf
```

Debug the application:

```text
$ arc-elf32-gdb -quiet main.elf
Reading symbols from main.elf...
(gdb) target remote :1234
Remote debugging using :1234
0x00000124 in __start ()
(gdb) load
Loading section .init, size 0x22 lma 0x100
Loading section .text, size 0x1554 lma 0x124
Loading section .fini, size 0x16 lma 0x1678
Loading section .ivt, size 0x54 lma 0x168e
Loading section .data, size 0x534 lma 0x36e8
Loading section .ctors, size 0x8 lma 0x3c1c
Loading section .dtors, size 0x8 lma 0x3c24
Loading section .sdata, size 0x10 lma 0x3c2c
Start address 0x00000124, load size 6964
Transfer rate: 1700 KB/sec, 696 bytes/write.
(gdb) b main
Breakpoint 1 at 0x276: file main.c, line 3.
(gdb) c
Continuing.

Breakpoint 1, main () at main.c:3
3               return 0;
(gdb)
```

## Using a Socket Instead of Port

If known ports are busy then you can connect to the GDB server using a socket.
Expose GDB server through socket instead of port

```shell
qemu-system-arc -M arc-sim \
                -cpu archs \
                -monitor none \
                -display none \
                -nographic \
                -no-reboot \
                -chardev socket,path=/tmp/gdb-socket,server=on,wait=off,id=gdb0 \
                -gdb chardev:gdb0 -S \
                -kernel main.elf
```

Connect to the GDB server
```text
$ arc-elf32-gdb -quiet main.elf
Reading symbols from main.elf...
(gdb) target remote /tmp/gdb-socket
Remote debugging using /tmp/gdb-socket
0x00000124 in __start ()
...
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

You need to use `-specs=nsim.specs` to use input/output features and
pass `-semihosting` option to QEMU:

```text
$ arc-elf32-gcc -mcpu=archs -specs=nsim.specs main.c -o main.elf
$ qemu-system-arc -M arc-sim \
                  -cpu archs \
                  -monitor none \
                  -display none \
                  -nographic \
                  -no-reboot \
                  -semihosting \
                  -kernel main.elf
Hello, World!
```

## Building "Hello, World!" Using MetaWare Development Toolkit and Running on QEMU

Without `-semihosting` QEMU creates a character device for `arc-sim` board on
hard coded `0x90000000` address. MetaWare’s standard runtime library does not
support input/output interfaces of QEMU for ARC. But it is possible to implement
your own basic `hostlink` library for MetaWare to meet QEMU's requirements.
You have to implement at least one function to add support of simple output
using the character device at `0x90000000`:

```c
int _write (int handle, const char *buf, unsigned int count)
{
    unsigned int i = 0;
    while (i < count)
    {
        *(char *) 0x90000000 = buf[i++];
    }
    return count;
}
```

Save it as `write.c` file and compile it along with `main.c`:

```shell
# For ARC HS3x/HS4x
ccac -av2hs -Hhostlib= main.c write.c -o main.elf

# For ARC HS5x
ccac -av3hs -Hhostlib= main.c write.c -o main.elf

# For ARC HS6x
ccac -arc64 -Hhostlib= main.c write.c -o main.elf
```

Run it using QEMU with `-serial stdio` option:

```shell
# For ARC HS3x/HS4x
qemu-system-arc -M arc-sim \
                -cpu archs \
                -monitor none \
                -display none \
                -nographic \
                -no-reboot \
                -serial stdio \
                -kernel main.elf

# For ARC HS5x
qemu-system-arc -M arc-sim \
                -cpu hs5x \
                -monitor none \
                -display none \
                -nographic \
                -no-reboot \
                -serial stdio \
                -kernel main.elf

# For ARC HS6x
qemu-system-arc64 -M arc-sim \
                  -cpu hs6x \
                  -monitor none \
                  -display none \
                  -nographic \
                  -no-reboot \
                  -serial stdio \
                  -kernel main.elf
```

## Enhanced Logging

To enable logging, it is necessary to provide the enabled log levels with the `-d` flag. Some of the more relevant ones are:

```text
in_asm          show target assembly code for each compiled TB
nochain         do not chain compiled TBs so that "exec" and "cpu" show complete traces
exec            show trace before each executed TB (lots of logs)
cpu             show CPU registers before entering a TB (lots of logs)
fpu             include FPU registers in the 'cpu' logging
int             show interrupts/exceptions in short format
mmu             log MMU-related activities
unimp           log unimplemented functionality
```

To get a complete listing, run `qemu-system-arc -d help`. Use `-D <logfile>` to dump the logs into a file instead of
standard output.

## Tracing Internals

QEMU provides a tracing infrastructure which may help in debugging or analyzing what happens within a simulation cycle.
At this moment, there are two tracers added into ARC backend, one for MMU operations, and another for exceptions:

```c
# mmu.c
mmu_command(uint32_t address, const char *command, uint32_t pd0, uint32_t pd1) "[MMU] at 0x%08x, CMD=%s, PD0=0x%08x, PD1=0x%08x"

# helper.c
excp_info(uint32_t address, const char *name) "[IRQ] at 0x08, Exception=%s"
```

Firstly, build QEMU with the `--enable-trace-backends=simple` configure parameter. Then
Create a file with the events you want to trace. For example, here is such file with name `events.trc`:

```text
mmu_command
excp_info
```

Run the virtual machine to produce a trace file:

```shell
qemu-system-arc --trace events=events.trc ...
```

Pretty-print the binary trace file (override `<pid>` with QEMU process id for you session):

```text
<QEMU-source-tree-path>/scripts/simpletrace.py <QEMU-source-tree-path>/target/arc/trace-events trace-<pid>
```

## Running Tests

TCG is the internal language that powers QEMU. There are some assembly tests
that validate the basic function of several instructions in QEMU.

Firstly, make sure that QEMU if configured with `--cross-cc-arc=arc-elf32-gcc`
and `--cross-cc-arc64=arc64-elf-gcc` options. Then after building QEMU use these
commands to run TCG tests:

```shell
make clean-tcg
make build-tcg
make check-tcg
```
