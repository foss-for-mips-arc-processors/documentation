# Configuring Buildroot and Linux Kernel

## Preface

This guide contains a bunch of recipes which may be useful while building Linux kernel
for MIPS ARC processors. Consider reading [the Buildroot user manual](https://buildroot.org/downloads/manual/manual.html) for details.

## Configuration Files

Configuration menu allows to manually tune options for a particular build.
Initialize Buildroot with a predefined configuration file and enter the menu:

```text
make gf_archs38_haps_defconfig
make menuconfig
```

After you finishes your configuration session a configuration file is saved
as `.config`.

You can use `make savedefconfig` to save a current configuration to a separate
configuration file. It reduces all configuration options to a minimum set of options.

```shell
make savedefconfig DEFCONFIG=my_defconfig
```

Configuration file `my_defconfig` is saved in Buildroot's source tree. Here is an example of
a configuration file which selects a preinstalled toolchain:

```shell
BR2_arcle=y
BR2_archs38=y
BR2_TOOLCHAIN_EXTERNAL=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM=y
BR2_TOOLCHAIN_EXTERNAL_PATH="/tools/toolchains/arc-linux-gnu"
BR2_TOOLCHAIN_EXTERNAL_HEADERS_5_16=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM_GLIBC=y
# BR2_TOOLCHAIN_EXTERNAL_INET_RPC is not set
BR2_TOOLCHAIN_EXTERNAL_CXX=y
BR2_TOOLCHAIN_EXTERNAL_FORTRAN=y
BR2_TARGET_GENERIC_HOSTNAME="zebu_hs"
BR2_TARGET_GENERIC_ISSUE="Welcome to the HAPS Development Platform"
BR2_LINUX_KERNEL=y
BR2_LINUX_KERNEL_CUSTOM_VERSION=y
BR2_LINUX_KERNEL_CUSTOM_VERSION_VALUE="5.16"
BR2_LINUX_KERNEL_DEFCONFIG="haps_hs_smp"
BR2_LINUX_KERNEL_VMLINUX=y
BR2_TARGET_ROOTFS_INITRAMFS=y
```

You can place a custom configuration file in `config` directory in Buildroot's source tree.
It allows you to use custom configuration files as usual:

```shell
make my_defconfig
```

You can use `defconfig` command and `DEFCONFIG` environment variable to pass a
path to the configuration file:

```shell
# Using an absolute path
$ make defconfig DEFCONFIG=/home/user/buildroot/my_defconfig

# Using a relative path (it's always relative to Buildroot's source tree)
$ make defconfig DEFCONFIG=my_defconfig
```

## Building in a Separate Directory

It's a good practice to build Linux images out of the main source tree:

```shell
git clone -b arc-2026.09 https://github.com/foss-for-mips-arc-processors/buildroot
mkdir buildroot/build
cd buildroot/build
make -C .. O=$(pwd) gf_archs38_haps_defconfig
```

Option `-C ..` points to Buildroot source tree and `O=$(pwd)` passes an output directory's path to `make`.
Note that if you use relative paths in Buildroot configuration options, then they are relative to Buildroot's
source tree, but not to build directory.

## Selection a Big Endian Configuration

Select this option in a configuration menu:

```text
Target options -> Target Architecture -> (X) ARC (big endian)
```

This option corresponds to `BR2_arceb=y` configuration line.

## Selecting a Linux Kernel Version

By default, Buildroot selects the latest available version of the upstream Linux kernel. However, if you
want to use a development branch with the latest patches and support of ARCv3 processor families, then
consider using `arc-2026.09` branch of the development repository:

```text
Kernel -> Kernel version -> Custom Git repository
       -> URL of custom repository -> https://github.com/foss-for-mips-arc-processors/linux
       -> Custom repository version -> arc-2026.09
```

This set of options corresponds to these configuration lines:

```text
BR2_LINUX_KERNEL_CUSTOM_GIT=y
BR2_LINUX_KERNEL_CUSTOM_REPO_URL="https://github.com/foss-for-mips-arc-processors/linux"
BR2_LINUX_KERNEL_CUSTOM_REPO_VERSION="arc-2026.09"
```

## Selecting a Linux Kernel Configuration File

You can select a Linux kernel configuration file in Buildroot configuration menu. For example:

```text
Kernel -> Defconfig name -> haps_hs
```

This option corresponds to `BR2_LINUX_KERNEL_DEFCONFIG="haps_hs"` configuration line.

You can find a complete list of all Linux kernel configuration files in `arch/arc/configs` directory
of Linux kernel source tree:

```text
$ ls -1 <path-to-kernel-source-tree>/arch/arc/configs
axs101_defconfig
axs103_defconfig
axs103_smp_defconfig
haps_hs_defconfig
haps_hs_smp_defconfig
hsdk_defconfig
nsim_700_defconfig
nsimosci_defconfig
nsimosci_hs_defconfig
nsimosci_hs_smp_defconfig
tb10x_defconfig
vdk_hs38_defconfig
vdk_hs38_smp_defconfig
```

## Using Overlays

If you want to place addition files and directories in `rootfs`, then you can
use `BR2_ROOTFS_OVERLAY=<path>` parameter: it contains a path to the a directory
with files and directories which will be placed in the final filesystem image.

You can select it in a configuration menu:

```text
System configuration -> Root filesystem overlay directories -> overlay
```

Also you can achieve the same result by placing all necessary files and directories in `output/target`
(or just `target` if you build in a separate directory) directory and rebuilding images using `make`.

## Using Custom Sources Paths

You can specify custom sources paths for various components for debugging
purposes. The easiest way is to create `local.mk` file in a build directory.
Here is an example:

```bash
UCLIBC_OVERRIDE_SRCDIR = /sources/uClibc
GCC_OVERRIDE_SRCDIR = /sources/gcc
```

Refer to [Advanced usage](https://buildroot.org/downloads/manual/manual.html#_advanced_usage)
section of Buildroot documentation for details.
