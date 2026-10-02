# Release notes

This is a release notes page of 2026.09 version of the GNU Toolchain for
MIPS ARC 600, ARC 700, EM, HS3x/4x, HS5x, HS6x and ARC-V processors.

More information about ARC-V processors can be found
on the [MIPS ARC-V webpage](https://mips.com/processor-solutions/arc-v-processors/).

Binary distributions may be found on the [GitHub Release Page](https://github.com/foss-for-mips-arc-processors/toolchain/releases/tag/arc-2026.09-release).

## Toolchain and IDE Components Versions

* GCC 16.2 with ARC patches
* Binutils 2.45.1 with ARC patches
* GDB 17.1 with ARC patches
* Newlib 4.6.0 with ARC patches
* Picolibc 1.8.11 with ARC patches
* uClibc-ng v1.0.57 with ARC patches
* glibc 2.42 with ARC patches

This release of GNU toolchain is supported by CGEN IPlib (TCF generator) version 1.0.53 and later.

## New Features and Enhancements

## Binary distribution

* Supported host operating systems: Windows 11 64-bit, Ubuntu 22.04, RHEL/AlmaLinux 8.x
* Prebuilt bare-metal toolchains for 64-bit Windows & Linux hosts, see table with references and list of artifacts below.

## Toolchain Components

For this release binary distributions of ARC GNU tools for all supported processor families (both ARC Classic and ARC-V) are produced from the same sources of corresponding tools.

Here is a list of GitHub issues addressed in this release: [GitHub issues for 2026.09](https://github.com/foss-for-mips-arc-processors/toolchain/issues?q=is%3Aissue%20milestone%3A2026.09%20is%3Aclosed). Note, though, this list only contains issues filed against ARC GNU toolchain. Bugs and enhancements made in upstream open-source projects of each toolchain component could be found in the corresponding bug-tracking system.

* GCC 16.2 with ARC patches
    * Sources used for the release: <https://github.com/foss-for-mips-arc-processors/gcc/releases/tag/arc-2026.09-release>
    * Uses upstream 16.2 release, see release announcement ([16.1](https://lists.gnu.org/archive/html/info-gnu/2026-04/msg00013.html) and [16.2](https://lists.gnu.org/archive/html/info-gnu/2026-08/msg00000.html)) and [complete list of changes](https://gcc.gnu.org/gcc-16/changes.html).
    * Performance tuning for ARC-V RPX-100 and ARC-V RMX-500
    * Improved floating-point performance for ARC-V targets
    * Added initial ARC-V RMX-700 support
    * Added experimental Zicfilp & Zicfiss (CFI) support
    * Added preliminary MIPS M8500 support
* Binutils 2.45.1 with ARC patches
    * Sources used for the release: <https://github.com/foss-for-mips-arc-processors/binutils-gdb/releases/tag/arc-2026.09-release>
    * Uses upstream 2.45.1 release, see [release notes](https://sourceware.org/pipermail/binutils/2025-July/142967.html).
* GDB 17.1 with ARC patches
    * Sources used for the release: <https://github.com/foss-for-mips-arc-processors/binutils-gdb/releases/tag/arc-2026.09-release-gdb>
    * Uses upstream 17.1 release, see [release announcement](https://sourceware.org/pipermail/gdb-announce/2024/000141.html) and [complete list of changes](https://lists.gnu.org/archive/html/info-gnu/2025-12/msg00007.html) for major changes.
* Newlib 4.6.0 with ARC patches
    * Sources used for the release: <https://github.com/foss-for-mips-arc-processors/newlib/releases/tag/arc-2026.09-release>
    * Uses upstream 4.6.0 release.
* Picolibc 1.8.11 with ARC patches
    * Sources used for the release: <https://github.com/foss-for-mips-arc-processors/picolibc/releases/tag/arc-2026.09-release>
    * Uses upstream 1.8.11 release, see [release announcement](https://github.com/picolibc/picolibc/releases/tag/1.8.11).
* uClibc-ng 1.0.57 with ARC patches
    * Sources used for the release: <https://github.com/foss-for-mips-arc-processors/uClibc/releases/tag/arc-2026.09-release>
    * Uses upstream 1.0.57 release, see [release announcement](https://mailman.openadk.org/mailman3/hyperkitty/list/devel@uclibc-ng.org/thread/JMEZYWV7VVHKB67QNLOVLPGKAFMDYS7B/).
* glibc 2.42 with ARC patches
    * Sources used for the release: <https://github.com/foss-for-mips-arc-processors/glibc/releases/tag/arc-2026.09-release>
    * Uses upstream 2.42 release, see [release announcement](https://lists.gnu.org/archive/html/info-gnu/2025-07/msg00011.html) and [complete list of changes](https://sourceware.org/glibc/wiki/Release/2.42).

## Known issues

### ARC Classic

1. Newlib's `libgloss` doesn't support RF16 configuration of ARC cores when building for nSIM with `-specs=nsim.specs`, see #231. Use `-specs=hl.specs` instead for RF 16 configurations.

2. Non-multilib toolchain doesn't contain `libgloss` libraries for nSIM and development boards, see #262.

3. `libcrypt.so.1` is not included in the toolchain (starting from glibc 2.38 `libcrypt.so.1` is not built by default and will be removed from glibc in the future), please use `libcrypt` implemented by external libraries such as [libxcrypt](https://github.com/besser82/libxcrypt) instead of relying on Glibc internal implementation.

4. Eclipse IDE for ARCompact does not support selecting `-specs=` options in project's configuration menu. Consider passing this options (e.g., `-specs=nsim.specs` for nSIM) in "ARC GNU Linker" field of projects configuration dialog (C/C++ Build -> Settings -> Top Settings -> ARC GNU Linker).

### ARC-V

1. Some complex combinations of `-march=` and `-mabi=` options may lead to unpredictable errors during compilation. Such issues are related to general support of RISC-V extensions in GCC. Refer [#610](https://github.com/foss-for-mips-arc-processors/toolchain/issues/610) for details.

2. The size-optimized Newlib Nano configuration (used when `-specs=nano.specs` is passed to GCC) does not support `printf()` for `float` and `double` by default. Nano `printf()` is size-optimized and does not include support of `float` and `double`. If you need that feature, pass `-u _printf_float` to GCC when you compile your applications. This option picks up support of `float` and `double` for size optimized `printf()` on demand.

3. Using selective scheduler with a specific set of options may lead to incorrect code generation. Refer [Bug 118153](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=118153) for details.
