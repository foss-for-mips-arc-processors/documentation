# Building Linux for a Simulation and Running on nSIM

!!! info "UART Settings for Older Linux Kernels (v5.4 and Prior)"

    nSIM 2019.06 onwards supports DW UART (in addition to the legacy ARC UART). This paved the way to running Linux image
    built for HAPS FPGA on nSIM. So starting with kernel version v5.5-rc1, `nsim_hs_smp_defconfig` and `nsim_hs_defconfig`
    defconfigs were merged with corresponding haps defconfigs and everything was switched to DW UART.

    For kernels v5.4 and prior (with ARC UART based configurations and DT) and/or older nSIM, the legacy ARC UART needs to be instantiated
    instead of `-prop=nsim_mem-dev=<...>` property presented in the guides:

    * For ARC770 use `-prop=nsim_mem-dev=uart0,base=0xc0fc1000,irq=5`
    * For ARC HS38 single-core use `-prop=nsim_mem-dev=uart0,base=0xc0fc1000,irq=24`
    * For ARC HS38 multi-core configuration with IDU, use `-prop=nsim_mem-dev=uart0,base=0xc0fc1000,irq=0,use_connect=1`

## Prerequisites

Clone the Buildroot repository:

```shell
git clone -b arc-2026.09 https://github.com/foss-for-mips-arc-processors/buildroot
cd buildroot
```

## Building Images Without a Preinstalled Toolchain

!!! info

    By default, `gf_archs38_haps_defconfig` uses `haps_hs_smp`
    kernel configuration file. If you are going to run an image on nSIM with
    a single core then change it to `haps_hs` through `make menuconfig`
    (`Kernel` -> `Defconfig name` -> `haps_hs`).

You can configure Buildroot using these commands:

```shell
make gf_arc700_nsim_defconfig  # ARCompact ARC700, nSIM
make gf_archs38_haps_defconfig # ARCv2 HS4x, nSIM and QEMU
make gf_arc32_defconfig # ARCv3 HS5x, nSIM and QEMU
make gf_arc64_defconfig # ARCv3 HS6x, nSIM and QEMU
```

Then run `make` to build images.

## Running Images Using nSIM

### HS4x Targets

Before running the image using nSIM create a corresponding configuration file `hs4x.props`
(use `nsim_fast=1` option if you have nSIM Pro license):

```text
nsim_fast=0
nsim_isa_family=av2hs
nsim_isa_core=3
chipid=0xffff
nsim_isa_atomic_option=1
nsim_isa_ll64_option=1
nsim_mmu=4
mmu_pagesize=8192
mmu_super_pagesize=2097152
mmu_stlb_entries=16
mmu_ntlb_ways=4
mmu_ntlb_sets=128
icache=32768,64,4,0
dcache=16384,64,2,0
nsim_isa_shift_option=2
nsim_isa_swap_option=1
nsim_isa_bitscan_option=1
nsim_isa_sat=1
nsim_isa_div_rem_option=1
nsim_isa_mpy_option=9
nsim_isa_enable_timer_0=1
nsim_isa_enable_timer_1=1
nsim_isa_number_of_interrupts=32
nsim_isa_number_of_external_interrupts=32
isa_counters=1
nsim_isa_pct_counters=8
nsim_isa_pct_size=48
nsim_isa_pct_interrupt=1
nsim_mem-dev=uart0,kind=dwuart,base=0xf0000000,irq=24
nsim_isa_aps_feature=1
nsim_isa_num_actionpoints=4
nsim_isa_rtc_option=1
nsim_isa_fpud_option=1
```

Then run the image using nSIM:

```shell
nsimdrv -propsfile hs4x.props output/images/vmlinux
```

Run the images without using a property file:

```text
nsimdrv -prop nsim_isa_family=av2hs -prop nsim_isa_core=3 -prop chipid=0xffff -prop nsim_isa_atomic_option=1 \
        -prop nsim_isa_ll64_option=1 -prop nsim_mmu=4 -prop mmu_pagesize=8192 -prop mmu_super_pagesize=2097152 \
        -prop mmu_stlb_entries=16 -prop mmu_ntlb_ways=4 -prop mmu_ntlb_sets=128 -prop icache=32768,64,4,0 \
        -prop dcache=16384,64,2,0 -prop nsim_isa_shift_option=2 -prop nsim_isa_swap_option=1 -prop nsim_isa_bitscan_option=1 \
        -prop nsim_isa_sat=1 -prop nsim_isa_div_rem_option=1 -prop nsim_isa_mpy_option=9 -prop nsim_isa_enable_timer_0=1 \
        -prop nsim_isa_enable_timer_1=1 -prop nsim_isa_number_of_interrupts=32 -prop nsim_isa_number_of_external_interrupts=32 \
        -prop isa_counters=1 -prop nsim_isa_pct_counters=8 -prop nsim_isa_pct_size=48 -prop nsim_isa_pct_interrupt=1 \
        -prop nsim_mem-dev=uart0,kind=dwuart,base=0xf0000000,irq=24 -prop nsim_isa_aps_feature=1 \
        -prop nsim_isa_num_actionpoints=4 -prop nsim_isa_rtc_option=1 -prop nsim_isa_fpud_option=1 \
        output/images/vmlinux
```

Run the image using a predefined TCF file:

```text
nsimdrv -tcf $NSIM_HOME/etc/tcf/templates/hs38_full.tcf \
        -prop nsim_mem-dev=uart0,kind=dwuart,base=0xf0000000,irq=24 \
        output/images/vmlinux
```

You will see an kernel's initialization output:

```text
Console now belongs to UART, hit CRTL-] to return to simulator.
Linux version 5.16.0 ...
Memory @ 80000000 [1024M]
Memory @ 100000000 [1024M] Not used
OF: fdt: Machine model: snps,zebu_hs
earlycon: uart8250 at MMIO32 0xf0000000 (options '115200n8')
printk: bootconsole [uart8250] enabled

IDENTITY        : ARCVER [0x53] ARCNUM [0x0] CHIPID [0xffff]
processor [0]   : HS38 R3.0 (ARCv2 ISA)
ISA Extn        : atomic ll64 unalign mpy[opt 9] div_rem   FPU: sp dp
BPU             : partial match, cache:2048, Predict Table:16384 Return stk: 8
MMU [v4]        : 8k/2M, swalk 2 lvl, JTLB 128x4, uDTLB 8, uITLB 4
I-Cache         : 32K, 4way/set, 64B Line, VIPT
D-Cache         : 16K, 2way/set, 64B Line, PIPT
Peripherals     : 0xc0000000
Timers          : Timer0 Timer1 RTC [UP 64-bit]
Vector Table    : 0x80000000 [64-bit]
DEBUG           : ActionPoint 4/full

...

Welcome to the HAPS Development Platform
zebu_hs login:
```

Username is `root` without a password. To halt target system issue `halt` command.

### HS5x Targets

Before running the image using nSIM create a corresponding configuration file `hs5x.props`
(use `nsim_fast=1` option if you have nSIM Pro license):

```text
nsim_fast=0
nsim_isa_family=av3hs
nsim_isa_dc_hw_prefetch=1
nsim_isa_dual_issue_option=1
nsim_isa_atomic_option=2
nsim_isa_m128_option=0
nsim_isa_ll64_option=1
nsim_isa_mpy_option=9
nsim_isa_div_rem_option=2
nsim_isa_enable_timer_0=1
nsim_isa_enable_timer_1=1
nsim_isa_rtc_option=1
icache=16384,64,4
dcache=16384,64,2
mmu_version=16
mmu_pagesize=4096
mmu_address_space=32
nsim_mem-dev=uart0,kind=dwuart,base=0xf0000000,irq=24
nsim_isa_number_of_interrupts=32
nsim_isa_number_of_external_interrupts=32
nsim_isa_has_fp=1
nsim_isa_fp_dds_option=1
nsim_isa_fp_div_option=1
nsim_isa_fp_dp_option=1
nsim_isa_fp_hp_option=1
nsim_isa_fp_vec_option=1
nsim_isa_fp_wide_option=1
```

Then run the image using nSIM:

```shell
nsimdrv -propsfile hs5x.props output/images/loader
```

Run the image without a property file:

```text
nsimdrv -prop nsim_isa_family=av3hs -prop nsim_isa_dc_hw_prefetch=1 -prop nsim_isa_dual_issue_option=1 \
        -prop nsim_isa_atomic_option=2 -prop nsim_isa_m128_option=0 -prop nsim_isa_ll64_option=1 -prop nsim_isa_mpy_option=9 \
        -prop nsim_isa_div_rem_option=2 -prop nsim_isa_enable_timer_0=1 -prop nsim_isa_enable_timer_1=1 \
        -prop nsim_isa_rtc_option=1 -prop icache=16384,64,4 -prop dcache=16384,64,2 -prop mmu_version=16 -prop mmu_pagesize=4096 \
        -prop mmu_address_space=32 -prop nsim_mem-dev=uart0,kind=dwuart,base=0xf0000000,irq=24 -prop nsim_isa_number_of_interrupts=32 \
        -prop nsim_isa_number_of_external_interrupts=32 -prop nsim_isa_has_fp=1 -prop nsim_isa_fp_dds_option=1 \
        -prop nsim_isa_fp_div_option=1 -prop nsim_isa_fp_dp_option=1 -prop nsim_isa_fp_hp_option=1 -prop nsim_isa_fp_vec_option=1 \
        -prop nsim_isa_fp_wide_option=1 output/images/loader
```

### HS6x Targets

Before running the image using nSIM create a corresponding configuration file `hs6x.props`
(use `nsim_fast=1` option if you have nSIM Pro license):

```text
nsim_fast=0
nsim_isa_dual_issue_option=1
nsim_isa_has_hw_pf=1
nsim_isa_m128_option=1
nsim_isa_has_hw_pf=1
nsim_isa_vec64=1
nsim_isa_family=arc64
nsim_isa_enable_timer_0=1
nsim_isa_enable_timer_1=1
nsim_isa_rtc_option=1
nsim_isa_addr_size=64
nsim_isa_pc_size=64
icache=16384,64,4,o
dcache=16384,64,4,o
mmu_version=16
mmu_pagesize=4096
mmu_address_space=48
nsim_mem-dev=uart0,kind=dwuart,base=0xf0000000,irq=24
nsim_isa_mpy_option=9
nsim_isa_mpy64=1
nsim_isa_div64_option=1
nsim_isa_div_rem_option=2
nsim_isa_atomic_option=2
nsim_isa_has_fp=1
nsim_isa_fp_dds_option=1
nsim_isa_fp_div_option=1
nsim_isa_fp_dp_option=1
nsim_isa_fp_hp_option=1
nsim_isa_fp_vec_option=1
nsim_isa_fp_wide_option=1
```

Then run the image using nSIM:

```shell
nsimdrv -propsfile hs6x.props output/images/loader
```

Also, you can run the images without a property file:

```
nsimdrv -prop nsim_isa_dual_issue_option=1 -prop nsim_isa_has_hw_pf=1 -prop nsim_isa_m128_option=1 \
        -prop nsim_isa_vec64=1 -prop nsim_isa_family=arc64 -prop nsim_isa_enable_timer_0=1 -prop nsim_isa_enable_timer_1=1 \
        -prop nsim_isa_rtc_option=1 -prop nsim_isa_addr_size=64 -prop nsim_isa_pc_size=64 -prop icache=16384,64,4,o \
        -prop dcache=16384,64,4,o -prop mmu_version=16 -prop mmu_pagesize=4096 -prop mmu_address_space=48 \
        -prop nsim_mem-dev=uart0,kind=dwuart,base=0xf0000000,irq=24 -prop nsim_isa_mpy_option=9 -prop nsim_isa_mpy64=1 \
        -prop nsim_isa_div64_option=1 -prop nsim_isa_div_rem_option=2 -prop nsim_isa_atomic_option=2 -prop nsim_isa_has_fp=1 \
        -prop nsim_isa_fp_dds_option=1 -prop nsim_isa_fp_div_option=1 -prop nsim_isa_fp_dp_option=1 -prop nsim_isa_fp_hp_option=1 \
        -prop nsim_isa_fp_vec_option=1 -prop nsim_isa_fp_wide_option=1 \
        output/images/loader
```

### ARC700 Targets

There is no a Buildroot configuration file for building ARC700 images for
simulators. Read a [following section](#arc-770-with-uclibc-ng) to find out how
to build an image.

Before running the image using nSIM create a corresponding configuration file `arc700.props`
(use `nsim_fast=1` option if you have nSIM Pro license):

```text
nsim_fast=0
nsim_isa_family=a700
nsim_isa_atomic_option=1
nsim_mmu=3
icache=32768,64,2,0
dcache=32768,64,4,0
nsim_isa_dpfp=none
nsim_isa_shift_option=2
nsim_isa_swap_option=1
nsim_isa_bitscan_option=1
nsim_isa_sat=1
nsim_isa_mpy32=1
nsim_isa_enable_timer_0=1
nsim_isa_enable_timer_1=1
nsim_mem-dev=uart0,kind=dwuart,base=0xf0000000,irq=24
isa_counters=1
nsim_isa_pct_counters=8
nsim_isa_pct_size=48
```

Then run the image using nSIM:

```shell
nsimdrv -propsfile arc700.props output/images/vmlinux
```

Run the image without a property file:

```text
nsimdrv -prop nsim_isa_family=a700 -prop nsim_isa_atomic_option=1 -prop nsim_mmu=3 -prop icache=32768,64,2,0 \
        -prop dcache=32768,64,4,0 -prop nsim_isa_dpfp=none -prop nsim_isa_shift_option=2 -prop nsim_isa_swap_option=1 \
        -prop nsim_isa_bitscan_option=1 -prop nsim_isa_sat=1 -prop nsim_isa_mpy32=1 -prop nsim_isa_enable_timer_0=1 \
        -prop nsim_isa_enable_timer_1=1 -prop nsim_mem-dev=uart0,kind=dwuart,base=0xf0000000,irq=24 -prop isa_counters=1 \
        -prop nsim_isa_pct_counters=8 -prop nsim_isa_pct_size=48 output/images/vmlinux
```

Run the image using a predefined TCF file:

```text
nsimdrv -tcf $NSIM_HOME/etc/tcf/templates/arc770d.tcf -prop nsim_isa_number_of_interrupts=32 \
        -prop nsim_mem-dev=uart0,kind=dwuart,base=0xf0000000,irq=24 -prop nsim_isa_dpfp=none \
        -prop icache=32768,64,2,0 -prop dcache=32768,64,4,0 -prop nsim_isa_dpfp=none \
        -prop nsim_isa_swap_option=1 -prop nsim_isa_bitscan_option=1 -prop nsim_isa_sat=1 \
        -prop isa_counters=1 -prop nsim_isa_pct_counters=8 -prop nsim_isa_pct_size=48 \
        output/images/vmlinux
```

## Building Images With a Preinstalled Toolchain

We assume that a toolchain is preinstalled in `/tools/toolchains`. Also,
only a limited set of all available toolchains is considered. All releases may be downloaded
from [the official releases page](https://github.com/foss-for-mips-arc-processors/toolchain/releases).
Here is a table of toolchains, which are used in this guide. Note that
toolchain's installation path in this guide is a path that contains `bin` directory.

| ARC processors family | Standard library | Toolchain's installation path           | Version                            |
|-----------------------|------------------|-----------------------------------------|------------------------------------|
| ARC HS 6x             | glibc            | `/tools/toolchains/arc64-linux-gnu`     | [2026.09][arc64_glibc_toolchain]   |
| ARC HS 5x             | glibc            | `/tools/toolchains/arc32-linux-gnu`     | [2026.09][arc32_glibc_toolchain]   |
| ARC HS 5x             | uClibc-ng        | `/tools/toolchains/arc32-linux-uclibc`  | [2026.09][arc32_uclibc_toolchain]  |
| ARC HS 3x/4x          | glibc            | `/tools/toolchains/arc-linux-gnu`       | [2026.09][archs_glibc_toolchain]   |
| ARC HS 3x/4x          | uClibc-ng        | `/tools/toolchains/arc-linux-uclibc`    | [2026.09][archs_uclibc_toolchain]  |
| ARC 700               | uClibc-ng        | `/tools/toolchains/arc700-linux-uclibc` | [2026.09][arc700_uclibc_toolchain] |

[arc64_glibc_toolchain]: https://github.com/foss-for-mips-arc-processors/toolchain/releases/download/arc-2026.09-release/arc_gnu_2026.09_prebuilt_arc64_glibc_linux_install.tar.xz
[arc32_glibc_toolchain]: https://github.com/foss-for-mips-arc-processors/toolchain/releases/download/arc-2026.09-release/arc_gnu_2026.09_prebuilt_arc32_glibc_linux_install.tar.xz
[arc32_uclibc_toolchain]: https://github.com/foss-for-mips-arc-processors/toolchain/releases/download/arc-2026.09-release/arc_gnu_2026.09_prebuilt_arc32_uclibc_linux_install.tar.xz
[archs_glibc_toolchain]: https://github.com/foss-for-mips-arc-processors/toolchain/releases/download/arc-2026.09-release/arc_gnu_2026.09_prebuilt_glibc_le_archs_linux_install.tar.xz
[archs_uclibc_toolchain]: https://github.com/foss-for-mips-arc-processors/toolchain/releases/download/arc-2026.09-release/arc_gnu_2026.09_prebuilt_uclibc_le_archs_linux_install.tar.xz
[arc700_uclibc_toolchain]: https://github.com/foss-for-mips-arc-processors/toolchain/releases/download/arc-2026.09-release/arc_gnu_2026.09_prebuilt_uclibc_le_arc700_linux_install.tar.xz

### ARC HS 3x/4x with glibc

We a going to use the latest development branch of the Linux kernel.
Also, we use `hasp_hs` configuration file for the Linux kernel - it corresponds to
a single core (UP) configuration. Save a custom configuration file in `defconfig`:

```shell
BR2_arcle=y
BR2_archs38=y
BR2_TOOLCHAIN_EXTERNAL=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM=y
BR2_TOOLCHAIN_EXTERNAL_PATH="/tools/toolchains/arc-linux-gnu"
BR2_TOOLCHAIN_EXTERNAL_GCC_16=y
BR2_TOOLCHAIN_EXTERNAL_HEADERS_5_16=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM_GLIBC=y
BR2_TOOLCHAIN_EXTERNAL_INET_RPC=n
BR2_TOOLCHAIN_EXTERNAL_CXX=y
BR2_TOOLCHAIN_EXTERNAL_FORTRAN=y
BR2_TOOLCHAIN_EXTERNAL_HAS_SSP=y
BR2_LINUX_KERNEL=y
BR2_LINUX_KERNEL_CUSTOM_GIT=y
BR2_LINUX_KERNEL_CUSTOM_REPO_URL="https://github.com/foss-for-mips-arc-processors/linux"
BR2_LINUX_KERNEL_CUSTOM_REPO_VERSION="arc-2026.09"
BR2_LINUX_KERNEL_DEFCONFIG="haps_hs"
BR2_LINUX_KERNEL_VMLINUX=y
BR2_TARGET_ROOTFS_INITRAMFS=y
```

Build a Linux image:

```shell
make defconfig DEFCONFIG=defconfig
make
```

### ARC HS 3x/4x with uClibc-ng

We use `haps_hs` configuration file for the Linux kernel - it corresponds to
a single core (UP) configuration. Here is a corresponding configuration file for Buildroot:

```shell
BR2_arcle=y
BR2_archs38=y
BR2_TOOLCHAIN_EXTERNAL=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM=y
BR2_TOOLCHAIN_EXTERNAL_PATH="/tools/toolchains/arc-linux-uclibc"
BR2_TOOLCHAIN_EXTERNAL_GCC_16=y
BR2_TOOLCHAIN_EXTERNAL_WCHAR=y
BR2_TOOLCHAIN_EXTERNAL_HEADERS_5_16=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM_UCLIBC=y
BR2_TOOLCHAIN_EXTERNAL_INET_RPC=n
BR2_TOOLCHAIN_EXTERNAL_CXX=y
BR2_TOOLCHAIN_EXTERNAL_FORTRAN=y
BR2_TOOLCHAIN_EXTERNAL_HAS_SSP=y
BR2_LINUX_KERNEL=y
BR2_LINUX_KERNEL_CUSTOM_GIT=y
BR2_LINUX_KERNEL_CUSTOM_REPO_URL="https://github.com/foss-for-mips-arc-processors/linux"
BR2_LINUX_KERNEL_CUSTOM_REPO_VERSION="arc-2026.09"
BR2_LINUX_KERNEL_DEFCONFIG="haps_hs"
BR2_LINUX_KERNEL_VMLINUX=y
BR2_TARGET_ROOTFS_INITRAMFS=y
```

A process of building and running of the Linux kernel for ARC HS38 based on uClibc-ng
is the same as for [glibc version](#arc-hs-3x4x-with-glibc).

### ARC 770 with uClibc-ng

We use `nsim_700` configuration file for the Linux kernel - it corresponds to
a single core (UP) configuration. Here is a corresponding configuration file for Buildroot:

```shell
BR2_arcle=y
BR2_arc770d=y
BR2_TOOLCHAIN_EXTERNAL=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM=y
BR2_TOOLCHAIN_EXTERNAL_PATH="/tools/toolchains/arc700-linux-uclibc"
BR2_TOOLCHAIN_EXTERNAL_CUSTOM_UCLIBC=y
BR2_TOOLCHAIN_EXTERNAL_GCC_16=y
BR2_TOOLCHAIN_EXTERNAL_WCHAR=y
BR2_TOOLCHAIN_EXTERNAL_HAS_SSP=y
BR2_TOOLCHAIN_EXTERNAL_CXX=y
BR2_TOOLCHAIN_EXTERNAL_HEADERS_5_16=y
BR2_LINUX_KERNEL=y
BR2_LINUX_KERNEL_CUSTOM_GIT=y
BR2_LINUX_KERNEL_CUSTOM_REPO_URL="https://github.com/foss-for-mips-arc-processors/linux"
BR2_LINUX_KERNEL_CUSTOM_REPO_VERSION="arc-2026.09"
BR2_LINUX_KERNEL_DEFCONFIG="nsim_700"
BR2_LINUX_KERNEL_VMLINUX=y
BR2_TARGET_ROOTFS_INITRAMFS=y
```

Build a Linux image:

```shell
make -C .. O=$(pwd) defconfig DEFCONFIG=build/defconfig
make
```

### ARC HS5x with uClibc-ng

!!! info

    Linux kernels for ARCv3 are loaded differently in comparison to kernels for ARCv1 and ARCv2.
    Kernels for ARCv3 are always loaded from a physical address `0x0` instead of `0x80000000`.
    A custom `loader` image is generated to distinguish it from a default `vmlinux`
    (`vmlinux` uses `0x80000000` as a starting address in physical and virtual spaces).

We use `haps_hs5x` configuration file for the Linux kernel - it corresponds to
a single core (UP) configuration. Here is a corresponding configuration file for Buildroot:

```shell
BR2_arcle=y
BR2_arc32=y
BR2_TOOLCHAIN_EXTERNAL=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM_UCLIBC=y
BR2_TOOLCHAIN_EXTERNAL_PATH="/tools/toolchains/arc32-linux-uclibc"
BR2_TOOLCHAIN_EXTERNAL_GCC_16=y
BR2_TOOLCHAIN_EXTERNAL_INET_RPC=n
BR2_TOOLCHAIN_EXTERNAL_HEADERS_5_16=y
BR2_TOOLCHAIN_EXTERNAL_WCHAR=y
BR2_TOOLCHAIN_EXTERNAL_HAS_SSP=y
BR2_TOOLCHAIN_EXTERNAL_CXX=y
# BR2_STRIP_strip is not set
BR2_ROOTFS_POST_IMAGE_SCRIPT="board/gf/arc64/post-image.sh"
BR2_ROOTFS_POST_SCRIPT_ARGS="$(LINUX_DIR)"
BR2_LINUX_KERNEL=y
BR2_LINUX_KERNEL_CUSTOM_GIT=y
BR2_LINUX_KERNEL_CUSTOM_REPO_URL="https://github.com/foss-for-mips-arc-processors/linux.git"
BR2_LINUX_KERNEL_CUSTOM_REPO_VERSION="arc-2026.09"
BR2_LINUX_KERNEL_DEFCONFIG="haps_hs5x"
BR2_LINUX_KERNEL_IMAGE_TARGET_CUSTOM=y
BR2_LINUX_KERNEL_IMAGE_TARGET_NAME="loader"
BR2_TARGET_ROOTFS_INITRAMFS=y
```

Build a Linux image:

```shell
make defconfig DEFCONFIG=defconfig
make
```

### ARC HS5x with glibc

We use `haps_hs5x` configuration file for the Linux kernel - it corresponds to
a single core (UP) configuration. Here is a corresponding configuration file for Buildroot:

```shell
BR2_arcle=y
BR2_arc32=y
BR2_TOOLCHAIN_EXTERNAL=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM_GLIBC=y
BR2_TOOLCHAIN_EXTERNAL_PATH="/tools/toolchains/arc32-linux-gnu"
BR2_TOOLCHAIN_EXTERNAL_GCC_16=y
BR2_TOOLCHAIN_EXTERNAL_INET_RPC=n
BR2_TOOLCHAIN_EXTERNAL_HEADERS_5_16=y
BR2_TOOLCHAIN_EXTERNAL_WCHAR=y
BR2_TOOLCHAIN_EXTERNAL_HAS_SSP=y
BR2_TOOLCHAIN_EXTERNAL_CXX=y
# BR2_STRIP_strip is not set
BR2_ROOTFS_POST_IMAGE_SCRIPT="board/gf/arc64/post-image.sh"
BR2_ROOTFS_POST_SCRIPT_ARGS="$(LINUX_DIR)"
BR2_LINUX_KERNEL=y
BR2_LINUX_KERNEL_CUSTOM_GIT=y
BR2_LINUX_KERNEL_CUSTOM_REPO_URL="https://github.com/foss-for-mips-arc-processors/linux.git"
BR2_LINUX_KERNEL_CUSTOM_REPO_VERSION="arc-2026.09"
BR2_LINUX_KERNEL_DEFCONFIG="haps_hs5x"
BR2_LINUX_KERNEL_IMAGE_TARGET_CUSTOM=y
BR2_LINUX_KERNEL_IMAGE_TARGET_NAME="loader"
BR2_TARGET_ROOTFS_INITRAMFS=y
```

Build a Linux image:

```shell
make defconfig DEFCONFIG=defconfig
make
```

### ARC HS6x with glibc

We use `haps_arc64` configuration file for the Linux kernel - it corresponds to
a single core (UP) configuration. Here is a corresponding configuration file for Buildroot:

```shell
BR2_arcle=y
BR2_arc64=y
BR2_TOOLCHAIN_EXTERNAL=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM_GLIBC=y
BR2_TOOLCHAIN_EXTERNAL_PATH="/tools/toolchains/arc64-linux-gnu"
BR2_TOOLCHAIN_EXTERNAL_GCC_16=y
BR2_TOOLCHAIN_EXTERNAL_INET_RPC=n
BR2_TOOLCHAIN_EXTERNAL_HEADERS_5_16=y
BR2_TOOLCHAIN_EXTERNAL_WCHAR=y
BR2_TOOLCHAIN_EXTERNAL_HAS_SSP=y
BR2_TOOLCHAIN_EXTERNAL_CXX=y
BR2_TOOLCHAIN_EXTERNAL_FORTRAN=y
# BR2_STRIP_strip is not set
BR2_ROOTFS_POST_IMAGE_SCRIPT="board/gf/arc64/post-image.sh"
BR2_ROOTFS_POST_SCRIPT_ARGS="$(LINUX_DIR)"
BR2_LINUX_KERNEL=y
BR2_LINUX_KERNEL_CUSTOM_GIT=y
BR2_LINUX_KERNEL_CUSTOM_REPO_URL="https://github.com/foss-for-mips-arc-processors/linux.git"
BR2_LINUX_KERNEL_CUSTOM_REPO_VERSION="arc-2026.09"
BR2_LINUX_KERNEL_DEFCONFIG="haps_arc64"
BR2_LINUX_KERNEL_IMAGE_TARGET_CUSTOM=y
BR2_LINUX_KERNEL_IMAGE_TARGET_NAME="loader"
BR2_TARGET_ROOTFS_INITRAMFS=y
```

Build a Linux image:

```shell
make defconfig DEFCONFIG=defconfig
make
```
