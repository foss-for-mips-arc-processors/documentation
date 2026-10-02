# Toolchains for ARC Processors

## System Requirements

GNU toolchain for ARC has the same standard prerequisites as an upstream GNU toolchain
as documented in the GNU toolchain user guide or on [the GCC website](http://gcc.gnu.org/install/prerequisites.html).

Prebuilt toolchain on [the release page](https://github.com/foss-for-mips-arc-processors/toolchain/releases)
are supported on these systems for x86-64 targets:

1. Ubuntu 22.04
2. RedHat Enterprise Linux (RHEL) 8.x and compatible alternatives
3. Windows 11

Note that [the TCF wrapper](./arc-v/getting-started-picolibc.md#using-the-tcf-wrapper) and
[the Buildlib tool](./arc-v/multilib.md#using-buildlib-for-building-libraries) are not supported for
Windows hosts.

[The Buildlib tool](./arc-v/multilib.md#using-buildlib-for-building-libraries) tool has extra requirements:

1. Python 3.8 or higher is required. The default RedHat 8.x setup does not
   meet this requirement. You can install it using `dnf` and then use `python3.8`
   for running the Buildlib script:

    ```
    $ sudo dnf install python3.8
    $ python3.8 riscv64-gf-elf-buildlib ...
    ```

2. Meson 0.61 is or higher required. The default RedHat 8.x setup does not
   meet this requirement. You can install it in a user environment using `pip3`:

    ```
    $ pip3 install --user --upgrade meson
    ```

    Then reboot the system or logout and login again.

## Toolchains for Baremetal Targets

GNU toolchains for baremetal ARC targets consist of GCC, Binutils, GDB and
a standard library. There are two versions of baremetal toolchain with different
standard libraries: Picolibc and Newlib. There is
[a separate page for ARC Classic toolchain with Picolibc](./arc-classic/getting-started-picolibc.md)
since it's still under development.

ARCompact, ARCv2 and ARCv3 families are supported for ARC Classic targets.
GNU toolchain for ARC-V targets supports all ARC-V families:

* Tuning GCC instruction scheduling for RMX-100, RMX-500, RHX-100 and RPX-100.
* Includes optimized sets of libraries for basic profiles for RMX-100, RMX-500, RHX-100 and RPX-100.
* Enabling ARC-V caches on startup.

This table depicts which GCC driver should be used depending on ISA:

| ISA       | Driver/Triplet    | Driver/Triplet (alias) | Families           | Endianness |
|-----------|-------------------|------------------------|--------------------|------------|
| ARC-V     | `riscv64-gf-elf`  | -                      | RMX, RHX, RPX      | Little     |
| Atlas     | `riscv64-gf-elf`  | -                      | M8500              | Little     |
| ARCv3     | `arc64-elf-gcc`   | `arc64-gf-elf-gcc`     | ARC HS6x, ARC HS5x | Little[^1] |
| ARCv2     | `arc-elf32-gcc`   | `arc-gf-elf-gcc`       | ARC HS, ARC EM     | Little     |
| ARCv2     | `arceb-elf32-gcc` | `arceb-gf-elf-gcc`     | ARC HS, ARC EM     | Big        |
| ARCompact | `arc-elf32-gcc`   | `arc-gf-elf-gcc`       | ARC 700, ARC 600   | Little     |
| ARCompact | `arceb-elf32-gcc` | `arceb-gf-elf-gcc`     | ARC 700, ARC 600   | Big        |

[^1]: Big endian targets are not supported by GNU toolchain for ARCv3.

Note that binaries for both ARCv1 and ARCv2 may be built using a single `arc-elf32-gcc` driver. It means
that there is a single toolchain for two ISAs.

Prebuilt toolchains for ARCv3 families on
[the release page](https://github.com/foss-for-mips-arc-processors/toolchain/releases)
are configured with `arc64-elf-` prefix only and work both with HS5x and HS6x
families. However, you can configure the toolchain for HS5x with `arc32-elf-` prefix using
[Crosstool-NG](https://github.com/foss-for-mips-arc-processors/toolchain#crosstool-ng-configuration-manual-tuning)
configuration menu.

## Toolchains for Linux Targets for ARC Classic

There is a set of GNU toolchains for ARC Classic processors which allow to build and debug
applications for Linux. The Linux kernel itself for ARC Classic processors may be built using
both baremetal and Linux toolchains.

Linux toolchains are presented for all ARC processor families except ARC EM and ARC 600.
Two Linux standard libraries are currently supported: glibc and uClibc-ng.
This table depicts which GCC driver should be used depending on CPU family:

| Family      | Standard library | Driver/Triplet           |
|-------------|------------------|--------------------------|
| ARC 700     | glibc            | `arc-linux-gnu-gcc`      |
| ARC 700     | uClibc-ng        | `arc-linux-uclibc-gcc`   |
| ARC HS3x/4x | glibc            | `arc-linux-gnu-gcc`      |
| ARC HS3x/4x | uClibc-ng        | `arc-linux-uclibc-gcc`   |
| ARC HS5x    | uClibc-ng        | `arc32-linux-uclibc-gcc` |
| ARC HS6x    | glibc            | `arc64-linux-gnu-gcc`    |

Note, that in case of Linux toolchains, `arc-linux-*` drivers for ARC 700 and ARC HS are
different toolchains! For example, if you want to build applications for ARC 700 Linux targets,
then you need to use a particular toolchain for ARC 700 and the same is applicable for ARC HS,
though they have the same names.

## Native Toolchains for Linux Targets for ARC Classic

Native toolchains are toolchains that can be used on the targets Linux system
natively. Here is a list of native toolchains which are available on
[a release page](https://github.com/foss-for-mips-arc-processors/toolchain/releases):

| Family      | Standard library | Driver/Triplet           |
|-------------|------------------|--------------------------|
| ARC HS3x/4x | glibc            | `arc-linux-gnu-gcc`      |
| ARC HS5x    | uClibc-ng        | `arc32-linux-uclibc-gcc` |
| ARC HS6x    | glibc            | `arc64-linux-gnu-gcc`    |

## Eclipse IDE Package

Eclipse IDE bundle is also available on [a release page](https://github.com/foss-for-mips-arc-processors/toolchain/releases).
The bundle may contains different components depending on the host platform:

| Component                                           | Linux | Windows |
|-----------------------------------------------------|-------|---------|
| Installer                                           | ❌     | ✅       |
| ARCompact/ARCv2 baremetal toolchain (little endian) | ✅     | ✅       |
| ARCompact/ARCv2 baremetal toolchain (big endian)    | ✅     | ✅       |
| ARCv2 Linux uClibc toolchain (little endian)        | ✅     | ❌       |
| ARCv2 Linux uClibc toolchain (big endian)           | ✅     | ❌       |
| OpenOCD                                             | ✅     | ✅       |
| Eclipse IDE                                         | ✅     | ✅       |

Linux toolchains are not included in the bundle for Windows because they are not
supported by case-insensitive file systems.

Also, there is no an installer for Linux hosts - the bundle may be extracted and
used anywhere in a file system. Add `<ide>/bin` directory to `PATH` variable
to make all toolchain binaries available. Eclipse IDE may be launched by
`<ide>/eclipse/eclipse` binary.

## How to Get The Toolchain

There are several ways of getting the toolchain:

1. Download the latest release of the prebuilt toolchain on [the release page](https://github.com/foss-for-mips-arc-processors/toolchain/releases)
   of main [toolchain's repository](https://github.com/foss-for-mips-arc-processors/toolchain). Also, Eclipse IDE
   is available for downloading which is shipped with some of toolchains and OpenOCD.
2. You can build toolchains using Crosstool-NG build system. Follow instructions presented in
   `README.md` of main [toolchain's repository](https://github.com/foss-for-mips-arc-processors/toolchain)
   on GitHub.

## Resources

Because the ARC toolchains are built on top of standard components such as GCC, Binutils, and GDB,
all functionality of these tools stays in place and can be studied in detail in the corresponding manuals:

* [GCC documentation](https://gcc.gnu.org/onlinedocs/15.2.0/)
* [Binutils documentation](https://sourceware.org/binutils/docs-2.45/)
* [GDB documentation](https://www.sourceware.org/gdb/documentation/)
