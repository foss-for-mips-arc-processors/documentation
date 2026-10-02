# Understanding Multilib Configurations

GNU toolchain for ARC-V is shipped with a set of prebuilt libraries for each
available configuration (so called __multilib__). A configuration is a combination of
`-march`, `-mabi`, `-mtune` and `-mcmodel` values. The default value for `-mtune`
(if it's not passed explicitly) is `arc-v-rmx-100-series` and the default value for
`-mcmodel` is `medlow`.

Below you can find lists of all combinations of options for which prebuilt libraries are
presented in the toolchain.

## How Multilib Works

On linkage stage, when a set of target options is passed, GCC tries to select standard libraries
that match this set of options. If you are not sure what prebuilt libraries are selected for linking
with an application, you can use `-print-multi-os-directory` option to check this:

```
$ riscv64-gf-elf-gcc -march=rv32imafd_zca_zcmp -mabi=ilp32d -mtune=arc-v-rhx-100-series -print-multi-os-directory
rv32imafd_zca/ilp32d/rhx100
```

Output tells that the library compiled with `-march=rv32imafd_zca -mabi=ilp32d -mtune=arc-v-rhx-100-series`
is chosen for linking. `-march=rv32imafd_zca_zcmp` is a superset of `-march=rv32imafd_zca` and the most
compatible variant is chosen.

Consider another example:

```
$ riscv64-gf-elf-gcc -march=rv32imafd -mabi=ilp32d -mtune=arc-v-rhx-100-series -print-multi-os-directory
.
```

There is no prebuilt libraries for this set of options since the nearest compatible `-march`
value for this combination is `rv32imafd_zca`. Thus, output is just "." . That means, that
the default library is chosen.

You can find a full list of all multilib configurations this way:


```
$ riscv64-gf-elf-gcc -print-multi-lib
.;
rv32e/ilp32e;@march=rv32e@mabi=ilp32e
rv32em/ilp32e;@march=rv32em@mabi=ilp32e
rv32ea/ilp32e;@march=rv32ea@mabi=ilp32e
rv32ema/ilp32e;@march=rv32ema@mabi=ilp32e
rv32emac_zcb_zba_zbb_zbs_zfinx_zdinx/ilp32e;@march=rv32emac_zcb_zba_zbb_zbs_zfinx_zdinx@mabi=ilp32e
rv32i/ilp32;@march=rv32i@mabi=ilp32
rv32i/ilp32/rmx500;@march=rv32i@mabi=ilp32@mtune=arc-v-rmx-500-series
rv32i/ilp32/rmx700;@march=rv32i@mabi=ilp32@mtune=arc-v-rmx-700-series
rv32i/ilp32/rhx100;@march=rv32i@mabi=ilp32@mtune=arc-v-rhx-100-series
rv32ic/ilp32;@march=rv32ic@mabi=ilp32
rv32ic/ilp32/rmx500;@march=rv32ic@mabi=ilp32@mtune=arc-v-rmx-500-series
rv32ic/ilp32/rmx700;@march=rv32ic@mabi=ilp32@mtune=arc-v-rmx-700-series
rv32ic/ilp32/rhx100;@march=rv32ic@mabi=ilp32@mtune=arc-v-rhx-100-series
rv32im/ilp32;@march=rv32im@mabi=ilp32
...
```


## Configurations for RMX-100 mini Targets

| ARC-V profile              | `-march`                               | `-mabi`  | `-mtune`               | `-mcmodel` |
|----------------------------|----------------------------------------|----------|------------------------|------------|
| Base RMX-100 mini          | `rv32e`                                | `ilp32e` | `arc-v-rmx-100-series` | `medlow`   |
| Base RMX-100 mini + `M`    | `rv32em`                               | `ilp32e` | `arc-v-rmx-100-series` | `medlow`   |
| Base RMX-100 mini + `A`    | `rv32ea`                               | `ilp32e` | `arc-v-rmx-100-series` | `medlow`   |
| Base RMX-100 mini + `MA`   | `rv32ema`                              | `ilp32e` | `arc-v-rmx-100-series` | `medlow`   |
| Full-featured RMX-100 mini | `rv32emac_zcb_zba_zbb_zbs_zfinx_zdinx` | `ilp32e` | `arc-v-rmx-100-series` | `medlow`   |

## Configurations for RMX-100 Targets

| ARC-V profile                                     | `-march`                               | `-mabi` | `-mtune`               | `-mcmodel` |
|---------------------------------------------------|----------------------------------------|---------|------------------------|------------|
| Generic RISC-V                                    | `rv32i`                                | `ilp32` | `arc-v-rmx-100-series` | `medlow`   |
| Generic RISC-V                                    | `rv32ic`                               | `ilp32` | `arc-v-rmx-100-series` | `medlow`   |
| Generic RISC-V                                    | `rv32im`                               | `ilp32` | `arc-v-rmx-100-series` | `medlow`   |
| Generic RISC-V                                    | `rv32ia`                               | `ilp32` | `arc-v-rmx-100-series` | `medlow`   |
| Generic RISC-V                                    | `rv32ima`                              | `ilp32` | `arc-v-rmx-100-series` | `medlow`   |
| Generic RISC-V                                    | `rv32iac`                              | `ilp32` | `arc-v-rmx-100-series` | `medlow`   |
| Generic RISC-V                                    | `rv32imac`                             | `ilp32` | `arc-v-rmx-100-series` | `medlow`   |
| Base RMX-100                                      | `rv32ic_zcb_zba_zbb_zbs`               | `ilp32` | `arc-v-rmx-100-series` | `medlow`   |
| Base RMX-100 + `M`                                | `rv32imc_zcb_zba_zbb_zbs`              | `ilp32` | `arc-v-rmx-100-series` | `medlow`   |
| Base RMX-100 + `MA`                               | `rv32imac_zcb_zba_zbb_zbs`             | `ilp32` | `arc-v-rmx-100-series` | `medlow`   |
| Base RMX-100 + `MA`, `Zfinx`                      | `rv32imac_zcb_zba_zbb_zbs_zfinx`       | `ilp32` | `arc-v-rmx-100-series` | `medlow`   |
| Base RMX-100 + `MA`, `Zfinx`, `Zdinx`             | `rv32imac_zcb_zba_zbb_zbs_zfinx_zdinx` | `ilp32` | `arc-v-rmx-100-series` | `medlow`   |
| Base RMX-100 without `C`                          | `rv32i_zba_zbb_zbs`                    | `ilp32` | `arc-v-rmx-100-series` | `medlow`   |
| Base RMX-100 + `M` without `C`                    | `rv32im_zba_zbb_zbs`                   | `ilp32` | `arc-v-rmx-100-series` | `medlow`   |
| Base RMX-100 + `MA` without `C`                   | `rv32ima_zba_zbb_zbs`                  | `ilp32` | `arc-v-rmx-100-series` | `medlow`   |
| Base RMX-100 + `MA`, `Zfinx` without `C`          | `rv32ima_zba_zbb_zbs_zfinx`            | `ilp32` | `arc-v-rmx-100-series` | `medlow`   |
| Base RMX-100 + `MA`, `Zfinx`, `Zdinx` without `C` | `rv32ima_zba_zbb_zbs_zfinx_zdinx`      | `ilp32` | `arc-v-rmx-100-series` | `medlow`   |

## Configurations for RMX-500 Targets

| ARC-V profile                         | `-march`                               | `-mabi` | `-mtune`               | `-mcmodel` |
|---------------------------------------|----------------------------------------|---------|------------------------|------------|
| Generic RISC-V                        | `rv32i`                                | `ilp32` | `arc-v-rmx-500-series` | `medlow`   |
| Generic RISC-V                        | `rv32ic`                               | `ilp32` | `arc-v-rmx-500-series` | `medlow`   |
| Generic RISC-V                        | `rv32im`                               | `ilp32` | `arc-v-rmx-500-series` | `medlow`   |
| Generic RISC-V                        | `rv32ia`                               | `ilp32` | `arc-v-rmx-500-series` | `medlow`   |
| Generic RISC-V                        | `rv32ima`                              | `ilp32` | `arc-v-rmx-500-series` | `medlow`   |
| Generic RISC-V                        | `rv32iac`                              | `ilp32` | `arc-v-rmx-500-series` | `medlow`   |
| Generic RISC-V                        | `rv32imac`                             | `ilp32` | `arc-v-rmx-500-series` | `medlow`   |
| Base RMX-500                          | `rv32ic_zcb_zba_zbb_zbs`               | `ilp32` | `arc-v-rmx-500-series` | `medlow`   |
| Base RMX-500 + `M`                    | `rv32imc_zcb_zba_zbb_zbs`              | `ilp32` | `arc-v-rmx-500-series` | `medlow`   |
| Base RMX-500 + `MA`                   | `rv32imac_zcb_zba_zbb_zbs`             | `ilp32` | `arc-v-rmx-500-series` | `medlow`   |
| Base RMX-500 + `MA`, `Zfinx`          | `rv32imac_zcb_zba_zbb_zbs_zfinx`       | `ilp32` | `arc-v-rmx-500-series` | `medlow`   |
| Base RMX-500 + `MA`, `Zfinx`, `Zdinx` | `rv32imac_zcb_zba_zbb_zbs_zfinx_zdinx` | `ilp32` | `arc-v-rmx-500-series` | `medlow`   |

## Configurations for RMX-700 Targets

| ARC-V profile             | `-march`                        | `-mabi`  | `-mtune`               | `-mcmodel` |
|---------------------------|---------------------------------|----------|------------------------|------------|
| Generic RISC-V            | `rv32i`                         | `ilp32`  | `arc-v-rmx-700-series` | `medlow`   |
| Generic RISC-V            | `rv32ic`                        | `ilp32`  | `arc-v-rmx-700-series` | `medlow`   |
| Generic RISC-V            | `rv32im`                        | `ilp32`  | `arc-v-rmx-700-series` | `medlow`   |
| Generic RISC-V            | `rv32ia`                        | `ilp32`  | `arc-v-rmx-700-series` | `medlow`   |
| Generic RISC-V            | `rv32ima`                       | `ilp32`  | `arc-v-rmx-700-series` | `medlow`   |
| Generic RISC-V            | `rv32iac`                       | `ilp32`  | `arc-v-rmx-700-series` | `medlow`   |
| Generic RISC-V            | `rv32imac`                      | `ilp32`  | `arc-v-rmx-700-series` | `medlow`   |
| Base RMX-700              | `rv32ic_zcb_zba_zbb_zbs`        | `ilp32`  | `arc-v-rmx-700-series` | `medlow`   |
| Base RMX-700 + `M`        | `rv32imc_zcb_zba_zbb_zbs`       | `ilp32`  | `arc-v-rmx-700-series` | `medlow`   |
| Base RMX-700 + `MA`       | `rv32imac_zcb_zba_zbb_zbs`      | `ilp32`  | `arc-v-rmx-700-series` | `medlow`   |
| Base RMX-700 + `MA`, `F`  | `rv32imafc_zcb_zba_zbb_zbs`     | `ilp32f` | `arc-v-rmx-700-series` | `medlow`   |
| Base RMX-700 + `MA`, `FD` | `rv32imafd_zca_zcb_zba_zbb_zbs` | `ilp32d` | `arc-v-rmx-700-series` | `medlow`   |

## Configurations for RHX-100 Targets

| ARC-V profile       | `-march`                            | `-mabi`  | `-mtune`               | `-mcmodel` |
|---------------------|-------------------------------------|----------|------------------------|------------|
| Generic RISC-V      | `rv32i`                             | `ilp32`  | `arc-v-rhx-100-series` | `medlow`   |
| Generic RISC-V      | `rv32ic`                            | `ilp32`  | `arc-v-rhx-100-series` | `medlow`   |
| Generic RISC-V      | `rv32im`                            | `ilp32`  | `arc-v-rhx-100-series` | `medlow`   |
| Generic RISC-V      | `rv32ia`                            | `ilp32`  | `arc-v-rhx-100-series` | `medlow`   |
| Generic RISC-V      | `rv32ima`                           | `ilp32`  | `arc-v-rhx-100-series` | `medlow`   |
| Generic RISC-V      | `rv32iac`                           | `ilp32`  | `arc-v-rhx-100-series` | `medlow`   |
| Generic RISC-V      | `rv32imac`                          | `ilp32`  | `arc-v-rhx-100-series` | `medlow`   |
| Generic RISC-V      | `rv32imafc`                         | `ilp32f` | `arc-v-rhx-100-series` | `medlow`   |
| Generic RISC-V      | `rv32imafd_zca`[^1]                 | `ilp32d` | `arc-v-rhx-100-series` | `medlow`   |
| Base RHX-100        | `rv32imac_zcb_zba_zbb_zbs`          | `ilp32`  | `arc-v-rhx-100-series` | `medlow`   |
| Base RHX-100 + `F`  | `rv32imafc_zcb_zba_zbb_zbs`         | `ilp32f` | `arc-v-rhx-100-series` | `medlow`   |
| Base RHX-100 + `FD` | `rv32imafd_zca_zcb_zba_zbb_zbs`[^1] | `ilp32d` | `arc-v-rhx-100-series` | `medlow`   |

## Configurations for RPX-100 Targets

| ARC-V profile  | `-march`                       | `-mabi` | `-mtune`               | `-mcmodel`   |
|----------------|--------------------------------|---------|------------------------|--------------|
| Generic RISC-V | `rv64i`                        | `lp64`  | `arc-v-rpx-100-series` | `medany`[^2] |
| Generic RISC-V | `rv64ic`                       | `lp64`  | `arc-v-rpx-100-series` | `medany`     |
| Generic RISC-V | `rv64im`                       | `lp64`  | `arc-v-rpx-100-series` | `medany`     |
| Generic RISC-V | `rv64imc`                      | `lp64`  | `arc-v-rpx-100-series` | `medany`     |
| Generic RISC-V | `rv64ia`                       | `lp64`  | `arc-v-rpx-100-series` | `medany`     |
| Generic RISC-V | `rv64ima`                      | `lp64`  | `arc-v-rpx-100-series` | `medany`     |
| Generic RISC-V | `rv64iac`                      | `lp64`  | `arc-v-rpx-100-series` | `medany`     |
| Generic RISC-V | `rv64imac`                     | `lp64`  | `arc-v-rpx-100-series` | `medany`     |
| Generic RISC-V | `rv64imafdc`                   | `lp64d` | `arc-v-rpx-100-series` | `medany`     |
| Base RPX-100   | `rv64imac_zcb_zba_zbb_zbs`[^1] | `lp64`  | `arc-v-rpx-100-series` | `medany`     |
| Base RPX-100   | `rv64imafdc_zcb_zba_zbb_zbs`   | `lp64d` | `arc-v-rpx-100-series` | `medany`     |

## Configurations for M8500 Targets

| Profile                       | `-march`                                                                                      | `-mabi`  | `-mtune`     | `-mcmodel` |
|-------------------------------|-----------------------------------------------------------------------------------------------|----------|--------------|------------|
| Base M8500 without `C`        | `rv32ima_zba_zbb_zbs_zabha_zicond_zihintpause_zicbop_zimop`                                   | `ilp32`  | `mips-m8500` | `medlow`   |
| Base M8500                    | `rv32imac_zcb_zba_zbb_zbs_zabha_zcmp_zcmt_zicond_zihintpause_zicbop_zimop_zcmop`              | `ilp32`  | `mips-m8500` | `medlow`   |
| Base M8500 + `FD` without `C` | `rv32imafd_zba_zbb_zbs_zabha_zicond_zihintpause_zicbop_zimop_zfa`                             | `ilp32d` | `mips-m8500` | `medlow`   |
| Base M8500 + `FD`             | `rv32imafd_zba_zbb_zbs_zabha_zca_zcb_zcmp_zcmt_zcf_zicond_zihintpause_zicbop_zimop_zcmop_zfa` | `ilp32d` | `mips-m8500` | `medlow`   |

## Using Buildlib for Building Libraries

!!! warning

    Check the [System Requirements](../index.md#system-requirements) for the Buildlib script.

[The Buildlib tool](https://github.com/foss-for-mips-arc-processors/arcv-tcf-wrapper?tab=readme-ov-file#the-buildlib-tool)
allows building all toolchain libraries for a particular set of
target and optimization options and then building applications using these
prebuilt libraries. Buildlib builds `libgcc`, Picolibc, and `libstdc++`.

Consider a simple code example with `hello.c` filename:

```c
#include <stdio.h>

int main()
{
        printf("Hello, World!\n");
        return 0;
}
```

Here is an example of building of libraries for a custom set of
target options, using them for building an application and
running this application on nSIM:

```
$ riscv64-gf-elf-buildlib \
    --output buildlib \
    --march rv32imac_zbb \
    --mabi ilp32 \
    --mtune arc-v-rmx-100-series \
    --cflags="-g -O3" \
    -j 4

...

$ riscv64-gf-elf-tcf-gcc \
    -march=rv32imac_zbb \
    -mabi=ilp32 \
    -mtune=arc-v-rmx-100-series \
    -tcf-buildlib=./buildlib \
    -specs=picolibc.specs \
    --oslib=semihost \
    --crt0=semihost \
    hello.c -o hello.elf

$ nsimdrv \
    -p nsim_isa_family=rv32 \
    -p nsim_isa_ext=-all.i.m.a.c.zbb.zicsr \
    -p nsim_semihosting=1 \
    hello.elf

Hello, World!
```

[^1]: Note that this configuration does not include `zcd` in `-march` since it's not supported by RHX-100.
[^2]: In the GNU toolchain 64-bit targets are available only for `-mcmodel=medany` memory model.
