# Using OpenOCD

## Preface

OpenOCD is used for connecting to a board and running a GDB server for
debugging.

Connection host is a host that is connected to the debug target via USB cable
and runs OpenOCD. Debug host is a host that runs GDB, which connects to the
OpenOCD with TCP connection. Typically it is the same host.

Note, that all MIPS ARC boards a have built-in debug cable. It means that
a separate Digilent HS cable is not required for connecting to the board,
but only a simple USB cable.

## Running on Linux

Run `lsusb` to ensure that FTDI device is connected to the host. Here is an
example for EM Starter Kit 2.2:

```text
$ lsusb
Bus 001 Device 002: ID 0403:6010 Future Technology Devices International, Ltd FT2232C/D/H Dual UART/FIFO IC
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub
```

If you built and installed OpenOCD manually, then you can run it this way:

```text
$ openocd -f board/arc_em_sk_v2.2.cfg
Open On-Chip Debugger 0.9.0-dev (2023-08-08-15:19)
Licensed under GNU GPL v2
For bug reports, read
    http://openocd.sourceforge.net/doc/doxygen/bugs.html
adapter speed: 5000 kHz
Info : clock speed 5000 kHz
Info : JTAG tap: arc-em.cpu tap/device found: 0x200044b1 (mfg: 0x258, part: 0x0004, ver: 0x2)
Info : JTAG tap: arc-em.cpu tap/device found: 0x200044b1 (mfg: 0x258, part: 0x0004, ver: 0x2)
target state: halted
```

If you downloaded IDE bundle with OpenOCD for Linux, then you have to set
a path to OpenOCD scripts. You can do it this way (replace `<ide>` by a path to
the directory of IDE bundle):

```shell
$ <ide>/bin/openocd -s <ide>/share/openocd/scripts -f board/arc_em_sk_v2.2.cfg
```

You can find all available configuration files in `<openocd>/scripts/board`
directory (where `<openocd>` stands for OpenOCD installation directory):

```text
$ ls /tools/openocd/share/openocd/scripts/board/arc_* -1
/tools/openocd/share/openocd/scripts/board/arc_axs101.cfg
/tools/openocd/share/openocd/scripts/board/arc_axs102.cfg
/tools/openocd/share/openocd/scripts/board/arc_axs103_hs36.cfg
/tools/openocd/share/openocd/scripts/board/arc_axs103_hs38.cfg
/tools/openocd/share/openocd/scripts/board/arc_axs103_hs47D.cfg
/tools/openocd/share/openocd/scripts/board/arc_axs103_hs48.cfg
/tools/openocd/share/openocd/scripts/board/arc_em_sk.cfg
/tools/openocd/share/openocd/scripts/board/arc_em_sk_v1.cfg
/tools/openocd/share/openocd/scripts/board/arc_em_sk_v2.1.cfg
/tools/openocd/share/openocd/scripts/board/arc_em_sk_v2.2.cfg
/tools/openocd/share/openocd/scripts/board/arc_em_sk_v2.2_cjtag.cfg
/tools/openocd/share/openocd/scripts/board/arc_em_sk_v2.3.cfg
/tools/openocd/share/openocd/scripts/board/arc_em_sk_v2.3_cjtag.cfg
/tools/openocd/share/openocd/scripts/board/arc_hsdk.cfg
/tools/openocd/share/openocd/scripts/board/arc_iotdk.cfg
```

Refer to [the corresponding section](#configurations-files) for details.

Start a debugger on debug host:

```shell
$ arc-elf32-gdb application.elf
```

Connect to OpenOCD server:

```text
(gdb) target remote <connection host ip address>:3333
```

If OpenOCD and GDB are running on the same host:

```text
(gdb) target remote :3333
```

Load image to be debugged (`application.elf`) into the target memory:

```text
(gdb) load
```

Set breakpoints at functions main and exit:

```text
(gdb) break main
(gdb) break exit
```

Start the execution on target of the image to debug, to reach function main:

```text
(gdb) continue
```

Resume execution to reach function exit:

```text
(gdb) continue
```

## Running on Windows

!!! warning

    If you are going to use OpenOCD on Windows, firstly follow
    [Installing WinUSB on Windows](./winusb.md) guide to install WinUSB driver.

OpenOCD may be used on Windows the same way it's used on Linux.
If you downloaded and installed IDE bundle for Windows, then you can run
OpenOCD this way:

```text
$ C:\arc_gnu\bin\openocd -s C:\arc_gnu\share\openocd\scripts -f board\arc_em_sk_v2.2.cfg
```

## Running on macOS

Run `ioreg` application to ensure that FTDI device is there:

```shell
$ ioreg -p IOUSB -l -w 0
```

There should be output like this:

```text
Digilent USB Device@14100000  <class AppleUSBDevice, id 0x1000015f7, registered, matched, active, busy 0 (5 ms), retain 21>
{
    ...
}
```

OpenOCD may be used on macOS the same way it's used on Linux. However,
consider using `sudo` to get access to USB devices:

```shell
$ sudo openocd -f board/arc_em_sk_v2.2.cfg
```

Note that exact output could differ from host to host.

If you're using a USB adapter and have a driver kext matched to it,
you will need to unload it prior to running OpenOCD. E.g. with Apple
driver (OS X 10.9 or later) for FTDI run:

```shell
$ sudo kextunload -b com.apple.driver.AppleUSBFTDI
```

For FTDI vendor driver use:

```shell
sudo kextunload FTDIUSBSerialDriver.kext
```

## Debugging Multi-Core Targets

OpenOCD starts a distinct GDB server for each core of multi-core target. Here is
an example for HS Development Kit 4xD:

```text
$ openocd -f board/arc_hsdk_4xd.cfg
Open On-Chip Debugger 0.12.0+dev-gffa52f0e0 (2023-08-02-10:41)
Licensed under GNU GPL v2
For bug reports, read
    http://openocd.org/doc/doxygen/bugs.html
Info : target has l2 cache enabled is enabled
Info : target has l2 cache enabled is enabled
Info : target has l2 cache enabled is enabled
Info : target has l2 cache enabled is enabled
32768
Info : Listening on port 6666 for tcl connections
Info : Listening on port 4444 for telnet connections
Info : ftdi: if you experience problems at higher adapter clocks, try the command "ftdi tdo_sample_edge falling"
Info : clock speed 10000 kHz
Info : JTAG tap: arc-em.cpu4 tap/device found: 0x100c54b1 (mfg: 0x258 (ARC International), part: 0x00c5, ver: 0x1)
Info : JTAG tap: arc-em.cpu3 tap/device found: 0x100854b1 (mfg: 0x258 (ARC International), part: 0x0085, ver: 0x1)
Info : JTAG tap: arc-em.cpu2 tap/device found: 0x100454b1 (mfg: 0x258 (ARC International), part: 0x0045, ver: 0x1)
Info : JTAG tap: arc-em.cpu1 tap/device found: 0x100054b1 (mfg: 0x258 (ARC International), part: 0x0005, ver: 0x1)
Info : starting gdb server for arc-em.cpu4 on 3333
Info : Listening on port 3333 for gdb connections
Info : starting gdb server for arc-em.cpu3 on 3334
Info : Listening on port 3334 for gdb connections
Info : starting gdb server for arc-em.cpu2 on 3335
Info : Listening on port 3335 for gdb connections
Info : starting gdb server for arc-em.cpu1 on 3336
Info : Listening on port 3336 for gdb connections
```

All cores are numbered in reverse order:

* Port 3333 corresponds to Core 4.
* Port 3334 corresponds to Core 3.
* Port 3335 corresponds to Core 2.
* Port 3336 corresponds to Core 1.

If you want to debug the Linux kernel or other application with support of SMP
then consider running separated GDB servers starting from the last core:

```shell
# Load and run core #1
arc-elf32-gdb -ex "target remote :3335" -ex "load" -ex "c" vmlinux

# Load and run core #2
arc-elf32-gdb -ex "target remote :3334" -ex "load" -ex "c" vmlinux

# Load and run core #3
arc-elf32-gdb -ex "target remote :3333" -ex "load" -ex "c" vmlinux

# Load and run core #0 (main one)
arc-elf32-gdb -ex "target remote :3336" -ex "load" -ex "c" vmlinux
```

## Advanced Debug Commands

With the GDB `monitor` command, you have an access to the core without
any interference from GDB. With other words, GDB has no notion of
changes in core state when using the so called `monitor` commands (but it is
very powerful). In GDB, connect to the OpenOCD target and type following
command to get a list of available monitor commands:

```text
(gdb) monitor help
```

To get a list of some ARC-specific commands, run:

```text
(gdb) monitor help arc
```

Those are actually internal OpenOCD commands which are also available in
configuration scripts and can be passed in OpeOCD command line with `-c ...`
options. Those command allow to enabled some extra features of OpenOCD
(disabled by default for one or another reason) or perform some low-level
actions bypassing GDB or even OpenOCD. For example it is possible to write/read
core and aux registers. However some command for register access will be
removed in future, when ARC OpenOCD will fully support flexible register
configurations.

## Using Another GDB port

You can use an OpenOCD command to change the GDB port this way:

```text
openocd -c "gdb_port 12345" ...
```

## Connecting to a Board with a Particular Serial Number

If several boards are connected to the host, then OpenOCD will choose
the first detected one. To choose a board with a particular serial number
it's necessary to create a custom board configuration file and specify
a serial number explicitly.

Here is an example for HSDK (a serial number is set through `ftdi_serial` variable):

```
$ cat arc_hsdk.cfg
source [find interface/ftdi/arc_sdp.cfg]
ftdi_serial "251642000213"
adapter_khz 10000
transport select jtag
source [find target/arc_hsdk.cfg]
init
reset halt

$ openocd -f ./arc_hsdk.cfg
...
```

Here is an example for EM SDP:

```
$ cat arc_em_sk_v2.3.cfg
source [find interface/ftdi/digilent-hs1.cfg]
ftdi_serial "210203826102"
adapter_khz 5000
transport select jtag
source [find target/arc_em_sk_fpga.cfg]
init
reset halt

$ openocd -f ./arc_em_sk_v2.3.cfg
...
```

## Configurations Files

Here is a table of configuration files for OpenOCD 0.9:

| Board                                            | OpenOCD configuration   |
|--------------------------------------------------|-------------------------|
| HS Development Kit 4x/4xD                        | `arc_hsdk_4xd.cfg`      |
| HS Development Kit                               | `arc_hsdk.cfg`          |
| IoT Development Kit                              | `arc_iotdk.cfg`         |
| EM Software Development Platform                 | `arc_em_sk_v2.3.cfg`    |
| EM Starter Kit 2.3                               | `arc_em_sk_v2.3.cfg`    |
| EM Starter Kit 2.2                               | `arc_em_sk_v2.2.cfg`    |
| EM Starter Kit 2.1                               | `arc_em_sk_v2.1.cfg`    |
| EM Starter Kit 2.0                               | `arc_em_sk_v2.1.cfg`    |
| EM Starter Kit 1                                 | `arc_em_sk_v1.cfg`      |
| AXS Software Development Platform 101            | `arc_axs101.cfg`        |
| AXS Software Development Platform 102            | `arc_axs102.cfg`        |
| AXS Software Development Platform 103 with HS36  | `arc_axs103_hs36.cfg`   |
| AXS Software Development Platform 103 with HS38  | `arc_axs103_hs38.cfg`   |
| AXS Software Development Platform 103 with HS47D | `arc_axs103_hs47D.cfg`  |
| AXS Software Development Platform 103 with HS48  | `arc_axs103_hs48.cfg`   |
