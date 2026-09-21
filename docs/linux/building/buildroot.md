# Building Linux kernel with Filesystem Using Buildroot

## Getting Buildroot

Clone the Buildroot repository:

```shell
git clone -b arc-2026.09 https://github.com/foss-for-mips-arc-processors/buildroot
cd buildroot
```

## Using Predefined Configurations

!!! info

    By default, `gf_archs38_haps_defconfig` uses `haps_hs_smp`
    kernel configuration file. If you are going to run an image on nSIM with
    a single core then change it to `haps_hs` through `make menuconfig`
    (`Kernel` -> `Defconfig name` -> `haps_hs`).

Buildroot uses configuration files (`defconfig`s) for configuring a root filesystem,
a toolchain, a Linux kernel, etc. Buildroot comes with a number of existing predefined
configurations which may be found in `config` directory. Use `make list-defconfigs` command
to list all available configurations:

```text
$ make list-defconfigs | grep arc
  gf_arc32_defconfig
  gf_arc64_defconfig
  gf_arc700_axs101_defconfig
  gf_archs38_axs103_defconfig
  gf_archs38_haps_defconfig
  gf_archs38_hsdk_defconfig
```

Here is a short description of each configuration:

* `gf_arc32_defconfig` - A configuration for ARC HS5x on HAPS, nSIM or QEMU.
* `gf_arc64_defconfig` - A configuration for ARC HS6x on HAPS, nSIM or QEMU.
* `gf_arc700_axs101_defconfig` - A configuration for ARC 700 on AXS101 development board.
* `gf_archs38_axs103_defconfig` - A configuration for ARC HS38 on AXS103 development board.
* `gf_archs38_haps_defconfig` - A configuration for ARC HS3x/4x in ZeBU, HAPS, nSIM or QEMU.
* `gf_archs38_hsdk_defconfig` - A configuration for ARC HS3x/4x on HSDK and HSDK 4xD development boards.
This configuration builds images for running Linux using U-Boot.

## Configuring Root Filesystem

You can select a predefined configuration this way:

```shell
make gf_archs38_haps_defconfig
```

You can manually tune filesystem's configuration this way:

```shell
make menuconfig
```

The final configuration file is saved in `.config` file.

## Configuring Linux Kernel

Also, you can configure kernel's options this way:

```shell
make linux-menuconfig
```

## Building Linux Image

Buildroot with default configurations, which are mentioned above, builds Linux images
in this sequence:

1. Download sources for a toolchain and build it.
2. Download sources for packages, build them using the toolchain and put them
in a filesystem image (`rootfs`).
3. Download sources for a Linux kernel and build it using the toolchain.
4. Link the Linux kernel against `rootfs` (`vmlinux`) or save it separately from `rootfs` (`bzImage` or `uImage`).
Whether `rootfs` is linked against  Linux kernel or not depends on a particular configuration. For example,
`gf_archs38_hsdk_defconfig` configuration doesn't link `rootfs` against Linux kernel.

You can build images using `make`:

```shell
make
```

By default, Buildroot builds everything in `output` directory. Images are placed in
`output/images` directory. For `gf_archs38_haps_defconfig` configuration you will find
a couple of filesystem images (`rootfs.cpio` and `rootfs.tar`) and `vmlinux` - a kernel
with the filesystem included:

```text
$ ls output/images
rootfs.cpio  rootfs.tar  vmlinux
```

For example, for `gf_archs38_hsdk_defconfig` configuration you will find much more
images (consider reading a guide about building Linux for HSDK for more details):

```text
$ ls -1 output/images
boot.vfat
rootfs.ext2
rootfs.ext4
sdcard.img
u-boot
u-boot.bin
uImage
uboot-env.bin
```

## Building Linux Kernel and Filesystem Separately

Sometimes it's more convenient to build root file system and a Linux kernel separately.
This guide covers an example when it's necessary to build those images separately
for ARC HS38 target.

Suppose, that a glibc-based toolchain for ARC HS38 is preinstalled in
`/tools/toolchains/arc-linux-gnu`.

Use `gf_archs38_haps_defconfig` configuration file for this example and enter
a configuration menu of Buildroot:

```shell
make gf_archs38_haps_defconfig
make menuconfig
```

Deselect an option for the Linux kernel and select an appropriate filesystem
for your needs:

```text
Kernel -> [ ] Linux kernel
Filesystem images -> [*] cpio the root filesystem # For use as an initial RAM filesystem
                  -> ext2/3/4 root filesystem     # For mounting from a storage
```

Choose a preinstalled toolchain (a list of available features may differ
depending on a version of a toolchain, in case of errors follow Buildroot's
recommendations in error messages):

```text
Toolchain -> Toolchain type -> (X) External toolchain
          -> (X) Custom toolchain
          -> Toolchain origin -> (X) Pre-installed toolchain
          -> Toolchain path -> /tools/toolchains/arc-linux-gnu
          -> External toolchain gcc version -> (X) 14.x
          -> External toolchain kernel headers series -> (X) 5.16.x
          -> External toolchain C library -> (X) glibc
          -> [ ] Toolchain has RPC support?
          -> [X] Toolchain has C++ support?
          -> [X] Toolchain has Fortran support?
```

Build the filesystem image:

```shell
make
```

Clone a Linux kernel repository and choose a version you want:

```shell
git clone https://github.com/torvalds/linux
cd linux
git checkout v6.4
```

To link the Linux kernel with previously built root filesystem image (with `.cpio` extension), it's necessary to
set `CONFIG_INITRAMFS_SOURCE` options for the kernel. Configure the kernel and enter a configuration menu:

```shell
make ARCH=arc haps_hs_defconfig
make ARCH=arc menuconfig
```

Fill `CONFIG_INITRAMFS_SOURCE` parameter with a path to `rootfs.cpio`, which was built previously:

```text
General setup -> Initial RAM filesystem and RAM disk support
                 -> Initramfs source file(s) -> <Buildroot-source-tree>/output/images/rootfs.cpio
```

Put the toolchain to `PATH` environment variable and build the kernel:

```shell
export PATH=/tools/toolchains/arc-linux-gnu:$PATH
make ARCH=arc CROSS_COMPILE=arc-linux-
```

The Linux kernel with root file system is saved as `vmlinux` file.
