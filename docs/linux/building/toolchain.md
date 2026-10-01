# Using External Toolchains

If an external toolchain is used then a number of Buildroot options must
be configured properly, since Buildroot cannot detected all available toolchain's
features itself. This guide describes how to properly configure an external
toolchain.

## Selecting an External Toolchain

By default, Buildroot builds a Linux toolchain for ARC from scratch and may use
relatively old sources for tools like GCC, Binutils, etc. If you want to use
the latest available toolchain for ARC processors, then consider using
prebuilt components from [the official releases page](https://github.com/foss-for-mips-arc-processors/toolchain/releases).

Here is an example of how you can select the external toolchain for ARC HS3x/4x
targets based on glibc:

```text
Toolchain -> Toolchain type -> (X) External toolchain
          -> (X) Custom toolchain
          -> Toolchain origin -> (X) Toolchain to be downloaded and installed
          -> Toolchain URL -> <Your .tar.gz url from GitHub>
```

If the toolchain is already downloaded in installed then you can pick it up this
way:

```text
Toolchain -> Toolchain type -> (X) External toolchain
          -> (X) Custom toolchain
          -> Toolchain origin -> (X) Pre-installed toolchain
          -> Toolchain path -> /tools/toolchains/arc-linux-gnu
```

## Kernel Headers Version

All Linux toolchains are shipped with Linux headers for a particular Linux
kernel version. You can configure it this way:

```text
Toolchain -> External toolchain kernel headers series -> <version>
```

This option corresponds to `BR2_TOOLCHAIN_EXTERNAL_HEADERS_<major>_<minor>=y` configuration
line, where `<major>` stands for a major version and `<minor>` stands for a minor version.
For example, if the Linux kernel's version is 5.16 then `5.16.x` value must
be selected in the configuration menu of `BR2_TOOLCHAIN_EXTERNAL_HEADERS_5_16=y`
configuration line must be added.

Here is a table of kernel versions for different toolchain releases:

| Toolchain release | Linux headers version |
|-------------------|-----------------------|
| 2026.09 — 2022.09 | 5.16                  |
| 2021.09 — 2021.03 | 5.1                   |
| 2020.09           | 5.7                   |
| 2020.03 — 2018.03 | 4.15                  |
| 2017.09           | 4.12                  |
| 2017.03           | 4.9                   |
| 2016.09           | 4.8                   |
| 2016.03           | 4.6                   |
| 2015.12 — 2015.06 | 3.18                  |
| earlier           | 3.13                  |

## Options for 2026.09 Toolchain Release

!!! note

    RPC support must be explicitly disabled since Buildroot configurator
    may enable it by default.

### Options for HS6x Family (glibc-based)

Additional options for the toolchain:

```text
Toolchain -> External toolchain gcc version -> 16.x
             External toolchain kernel headers series -> 5.16.x
             External toolchain C library -> glibc
             [*] Toolchain has SSP support?
             [ ] Toolchain has RPC support?
             [*] Toolchain has C++ support?
             [*] Toolchain has Fortran support?
```

Configuration file lines:

```text
BR2_TOOLCHAIN_EXTERNAL_GCC_16=y
BR2_TOOLCHAIN_EXTERNAL_HEADERS_5_16=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM_GLIBC=y
BR2_TOOLCHAIN_EXTERNAL_HAS_SSP=y
BR2_TOOLCHAIN_EXTERNAL_INET_RPC=n
BR2_TOOLCHAIN_EXTERNAL_CXX=y
BR2_TOOLCHAIN_EXTERNAL_FORTRAN=y
```

### Options for HS5x Family (uClibc-based)

Additional options for the toolchain:

```text
Toolchain -> External toolchain gcc version -> 16.x
             External toolchain kernel headers series -> 5.16.x
             External toolchain C library -> uClibc/uClibc-ng
             [*] Toolchain has WCHAR support?
             [*] Toolchain has SSP support?
             [ ] Toolchain has RPC support?
             [*] Toolchain has C++ support?
```

Configuration file lines:

```text
BR2_TOOLCHAIN_EXTERNAL_GCC_16=y
BR2_TOOLCHAIN_EXTERNAL_HEADERS_5_16=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM_UCLIBC=y
BR2_TOOLCHAIN_EXTERNAL_WCHAR=y
BR2_TOOLCHAIN_EXTERNAL_HAS_SSP=y
BR2_TOOLCHAIN_EXTERNAL_INET_RPC=n
BR2_TOOLCHAIN_EXTERNAL_CXX=y
```

### Options for HS3x/4x Families (glibc-based)

Additional options for the toolchain:

```text
Toolchain -> External toolchain gcc version -> 16.x
             External toolchain kernel headers series -> 5.16.x
             External toolchain C library -> glibc
             [*] Toolchain has SSP support?
             [ ] Toolchain has RPC support?
             [*] Toolchain has C++ support?
             [*] Toolchain has Fortran support?
```

Configuration file lines:

```text
BR2_TOOLCHAIN_EXTERNAL_GCC_16=y
BR2_TOOLCHAIN_EXTERNAL_HEADERS_5_16=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM_GLIBC=y
BR2_TOOLCHAIN_EXTERNAL_HAS_SSP=y
BR2_TOOLCHAIN_EXTERNAL_INET_RPC=n
BR2_TOOLCHAIN_EXTERNAL_CXX=y
BR2_TOOLCHAIN_EXTERNAL_FORTRAN=y
```

### Options for HS3x/4x Families (uClibc-based)

Additional options for uClibc-based toolchain:

```text
Toolchain -> External toolchain gcc version -> 16.x
             External toolchain kernel headers series -> 5.16.x
             External toolchain C library -> uClibc/uClibc-ng
             [*] Toolchain has WCHAR support?
             [*] Toolchain has SSP support?
             [ ] Toolchain has RPC support?
             [*] Toolchain has C++ support?
             [*] Toolchain has Fortran support?
```

Configuration file lines:

```text
BR2_TOOLCHAIN_EXTERNAL_GCC_16=y
BR2_TOOLCHAIN_EXTERNAL_HEADERS_5_16=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM_UCLIBC=y
BR2_TOOLCHAIN_EXTERNAL_WCHAR=y
BR2_TOOLCHAIN_EXTERNAL_HAS_SSP=y
BR2_TOOLCHAIN_EXTERNAL_INET_RPC=n
BR2_TOOLCHAIN_EXTERNAL_CXX=y
BR2_TOOLCHAIN_EXTERNAL_FORTRAN=y
```

### Options for ARC700 Family (uClibc-based)

Additional options for uClibc-based toolchain:

```text
Toolchain -> External toolchain gcc version -> 16.x
             External toolchain kernel headers series -> 5.16.x
             External toolchain C library -> uClibc/uClibc-ng
             [*] Toolchain has WCHAR support?
             [*] Toolchain has SSP support?
             [ ] Toolchain has RPC support?
             [*] Toolchain has C++ support?
```

Configuration file lines:

```text
BR2_TOOLCHAIN_EXTERNAL_GCC_16=y
BR2_TOOLCHAIN_EXTERNAL_HEADERS_5_16=y
BR2_TOOLCHAIN_EXTERNAL_CUSTOM_UCLIBC=y
BR2_TOOLCHAIN_EXTERNAL_WCHAR=y
BR2_TOOLCHAIN_EXTERNAL_HAS_SSP=y
BR2_TOOLCHAIN_EXTERNAL_INET_RPC=n
BR2_TOOLCHAIN_EXTERNAL_CXX=y
```
