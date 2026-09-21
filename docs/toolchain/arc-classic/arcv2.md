# Options for ARCv2

## Target Options

Here is a list of target specific options for ARCv2 with
information about availability for ARC HS and ARC EM targets:

| Feature                                                             | GCC option             | ARC HS    | ARC EM    |
|---------------------------------------------------------------------|------------------------|-----------|-----------|
| Select a set of predefined values for target options                | `-mcpu=...`            | Available | Available |
| Enable shift instructions                                           | `-mbarrel-shifter`     | Available | Available |
| Enable bitscan instructions                                         | `-mnorm`               | Available | Available |
| Enable swap instruction                                             | `-mswap`               | Available | Available |
| Enable swap byte ordering instruction                               | `-mswape`              | Available | Available |
| Use 16 register file                                                | `-mrf16`[^1]           | Available | Available |
| [Multiplication instructions](#multiplication-options)              | `-mmpy-option=...`     | Available | Available |
| [Floating point unit configuration](#floating-point-unit-options)   | `-mfpu=...`            | Available | Available |
| Enable code density instructions                                    | `-mcode-density`       | Available | Available |
| Generate `ENTER_S` and `LEAVE_S` instructions                       | `-mcode-density-frame` | Available | Available |
| Enable `DIV` and `REM` instructions                                 | `-mdiv-rem`            | Available | Available |
| Enable atomic instructions                                          | `-matomic`             | Available | N/A       |
| Enable double load and store instructions                           | `-mll64`               | Available | N/A       |
| Generate Single Precision FPX (compact) instructions                | `-mspfp`               | N/A       | Available |
| Generate Single Precision FPX (compact) instructions                | `-mspfp-compact`       | N/A       | Available |
| Generate Single Precision FPX (fast) instructions                   | `-mspfp-fast`          | N/A       | Available |
| Generate Double Precision FPX (compact) instructions                | `-mdpfp`               | N/A       | Available |
| Generate Double Precision FPX (compact) instructions                | `-mdpfp-compact`       | N/A       | Available |
| Generate Double Precision FPX (fast) instructions                   | `-mdpfp-fast`          | N/A       | Available |
| Enable generation of SIMD instructions via target-specific builtins | `-msimd`               | N/A       | Available |
| Enable unaligned word and halfword accesses to packed data          | `-munaligned-access`   | Available | Available |
| Enable XY memory extension for the assembler (DSP version 3)        | `-mxy`                 | N/A       | Available |
| Enable uncached access for volatile memories                        | `-mvolatile-di`        | Available | Available |

[^1]: Note that `-mrf16` may be used only with `-mcpu=em_mini` option.

You can find a short description for all target options using `--target-help` option:

```
$ arc-gf-elf-gcc --target-help
The following options are target specific:
...
  -mbitops                    Enable use of NPS400 bit operations.
  -mbranch-index              Enable use of BI/BIH instructions when available.
  -mcase-vector-pcrel         Use pc-relative switch case tables - this enables case table shortening.
  -mcmem                      Enable use of NPS400 xld/xst extension.
  -mcode-density              Enable code density instructions for ARCv2.
  -mcode-density-frame        Enable ENTER_S and LEAVE_S opcodes for ARCv2.
  -mcompact-casesi            Enable compact casesi pattern.  Uses of this option are diagnosed.
  -mcpu=CPU                   Compile code for ARC variant CPU.
...
```

You can find out what target options are enabled or disabled with a particular set of options using `-Q` flag:

```
$ arc-gf-elf-gcc -mcpu=hs38 --target-help -Q
The following options are target specific:
...
  -mbitops                              [disabled]
  -mbranch-index                        [disabled]
  -mcase-vector-pcrel                   [disabled]
  -mcmem                                [disabled]
  -mcode-density                        [enabled]
  -mcode-density-frame                  [disabled]
  -mcompact-casesi                      [disabled]
  -mcpu=CPU                             hs38
...
```

## Multiplication Options

You can choose a subset of multiplication options to be generate for a
particular binary. The subset is chosen by `-mmpy-option=...` option.

Here is a table which matches `-mmpy-option=` values to corresponding features
for ARC HS3x/4x and ARC EM as they defined in ARChitect.

| `-mmpy-option=` | Aliases       | ARChitect option for ARC HS | ARChitect option for ARC EM |
|----------------:|---------------|-----------------------------|-----------------------------|
|               0 | `none`        | `-mpy_option=none`          | `-mpy_option=none`          |
|               2 | `wlh1`, `mpy` | `-mpy_option=mpy`           | `-mpy_option=wlh1`          |
|               3 | `wlh2`        | —                           | `-mpy_option=wlh2`          |
|               4 | `wlh3`        | —                           | `-mpy_option=wlh3`          |
|               5 | `wlh4`        | —                           | `-mpy_option=wlh4`          |
|               6 | `wlh5`        | —                           | `-mpy_option=wlh5`          |
|               7 | `plus_dmpy`   | `-mpy_option=plus_dmpy`     | —                           |
|               8 | `plus_macd`   | `-mpy_option=plus_macd`     | —                           |
|               9 | `plus_qmacw`  | `-mpy_option=plus_qmacw`    | —                           |

## Floating Point Unit Options

You can choose a subset of floating point options to be generate for a
particular binary. The subset is chosen by `-mfpu=...` option.

Here is the table that matches `-mfpu=` values to corresponding features for
ARC HS3x/4x and as they defined in ARChitect.

| `-mfpu=`   | `-has_fpu` | `-fpu_dp_option` | `-fpu_div_option` | `-fpu_fma_option` |
|------------|------------|------------------|-------------------|-------------------|
| `fpus`     | On         | —                | —                 | —                 |
| `fpus_div` | On         | —                | On                | —                 |
| `fpus_fma` | On         | —                | —                 | On                |
| `fpus_all` | On         | —                | On                | On                |
| `fpud`     | On         | On               | —                 | —                 |
| `fpud_div` | On         | On               | On                | —                 |
| `fpud_fma` | On         | On               | —                 | On                |
| `fpud_all` | On         | On               | On                | On                |

Here is the table that matches `-mfpu=` values to corresponding features for
ARC EM as they defined in ARChitect.

| `-mfpu=`    | `-has_fpu` | `-fpu_div_option` | `-fpu_fma_option` | `-fpu_dp_assist` |
|-------------|------------|-------------------|-------------------|------------------|
| `fpus`      | On         | —                 | —                 | —                |
| `fpus_div`  | On         | On                | —                 | —                |
| `fpus_fma`  | On         | —                 | On                | —                |
| `fpus_all`  | On         | On                | On                | —                |
| `fpuda`     | On         | —                 | —                 | On               |
| `fpuda_div` | On         | On                | —                 | On               |
| `fpuda_fma` | On         | —                 | On                | On               |
| `fpuda_all` | On         | On                | On                | On               |

## Default Options for Base Targets

A particular ARCv2 target is selected by `-mcpu=` option. Each `-mcpu=` value selects
a corresponding prebuilt library and a set of options:

* [`-mcpu=` values for ARC HS](#selecting-targets-for-arc-hs): `hs`, `hs34`, `archs`, `hs38`, `hs4x`, `hs4xd`, `hs38_linux`.
* [`-mcpu=` values for ARC EM](#selecting-targets-for-arc-em): `em`, `em_mini`, `em4`, `arcem`, `em4_dmips`, `em4_fpus`, `em4_fpuda`, `quarkse_em`.

Here is a table of default values for targets options for base ARC HS (`-mcpu=hs`) and ARC EM (`-mcpu=em`) configurations:

| GCC option             | `-mcpu=hs` | `-mcpu=em` |
|------------------------|------------|------------|
| `-mbarrel-shifter`     | On         | Off        |
| `-mnorm`               | On         | Off        |
| `-mswap`               | On         | Off        |
| `-mswape`              | Off        | Off        |
| `-mrf16`               | Off        | Off        |
| `-mmpy-option=...`     | `none`     | `none`     |
| `-mfpu=...`            | Off        | Off        |
| `-mcode-density`       | On         | Off        |
| `-mcode-density-frame` | Off        | Off        |
| `-mdiv-rem`            | Off        | Off        |
| `-matomic`             | On         | N/A        |
| `-mll64`               | Off        | N/A        |
| `-mspfp`               | N/A        | Off        |
| `-mspfp-compact`       | N/A        | Off        |
| `-mspfp-fast`          | N/A        | Off        |
| `-mdpfp`               | N/A        | Off        |
| `-mdpfp-compact`       | N/A        | Off        |
| `-mdpfp-fast`          | N/A        | Off        |
| `-msimd`               | N/A        | Off        |
| `-munaligned-access`   | On         | Off        |
| `-mxy`                 | N/A        | Off        |
| `-mvolatile-di`        | On         | On         |

## Selecting Targets for ARC HS

A particular ARC HS target is selected by `-mcpu=` option. Each `-mcpu=` value selects
a corresponding prebuilt library and a set of options in addition to default values
set by `-mcpu=hs`:

| `-mcpu=`     | `-mdiv-rem` | `-matomic` | `-mll64` | `-mmpy-option=` | `-mfpu=`   |
|--------------|-------------|------------|----------|-----------------|------------|
| `hs`         |             | On         |          |                 |            |
| `hs34`       |             | On         |          | `mpy`           |            |
| `archs`      | On          | On         | On       | `mpy`           |            |
| `hs38`       | On          | On         | On       | `plus_qmacw`    |            |
| `hs4x`       | On          | On         | On       | `plus_qmacw`    |            |
| `hs4xd`      | On          | On         | On       | `plus_qmacw`    |            |
| `hs38_linux` | On          | On         | On       | `plus_qmacw`    | `fpud_all` |

The above `-mcpu` values correspond to specific ARC HS processor templates presented in the ARChitect tool:

* `-mcpu=hs` corresponds to a basic ARC HS with only atomic instructions enabled. It corresponds to
  the following ARC HS templates in ARChitect: `hs34_base`, `hs36_base` and `hs38_base`.
* `-mcpu=hs34` is like `hs` but with with additional support for standard hardware multiplier.
  It corresponds to the following ARC HS templates in ARChitect: `hs34`, `hs36` and `hs38`.
* `-mcpu=archs` is a generic configuration, which corresponds to the default configuration in
  older GNU toolchain versions.
* `-mcpu=hs38` is a full-featured ARC HS. It corresponds to the following ARC HS template in ARChitect: `hs38_full`.
* `-mcpu=hs4x` and `-mcpu=hs4xd` have the same option set as `-mcpu=hs38`, but compiler will optimize
  instruction scheduling for specified processors.
* `-mcpu=hs38_linux` is a full-featured ARC HS with additional support of double-precision FPU.

## Selecting Targets for ARC EM

A particular ARC EM target is selected by `-mcpu=` option. Each `-mcpu=` value selects
a corresponding prebuilt library and a set of options in addition to default values
set by `-mcpu=em`:

| `-mcpu`      | `-mcode-density` | `-mnorm` | `-mswap` | `-mbarrel-shifter` | `-mdiv-rem` | `-mmpy-option=` | `-mfpu=` | `-mrf16` | `-mspfp` | `-mdpfp` |
|--------------|------------------|----------|----------|--------------------|-------------|-----------------|----------|----------|----------|----------|
| `em`         |                  |          |          |                    |             |                 |          |          |          |          |
| `em_mini`    |                  |          |          |                    |             |                 |          | On       |          |          |
| `em4`        | On               |          |          |                    |             |                 |          |          |          |          |
| `arcem`      | On               |          |          | On                 |             | `wlh1`          |          |          |          |          |
| `em4_dmips`  | On               | On       | On       | On                 | On          | `wlh1`          |          |          |          |          |
| `em4_fpus`   | On               | On       | On       | On                 | On          | `wlh1`          | `fpus`   |          |          |          |
| `em4_fpuda`  | On               | On       | On       | On                 | On          | `wlh1`          | `fpuda`  |          |          |          |
| `quarkse_em` | On               | On       | On       | On                 | On          | `wlh2`          |          |          | On       | On       |

The above `-mcpu` values correspond to specific ARC EM processor templates presented in the ARChitect tool:

* `-mcpu=em` doesn't correspond to any specific template, it simply defines the base ARC EM configuration without any optional instructions.
* `-mcpu=em_mini` is same as `em`, but uses reduced register file with only 16 core registers.
* `-mcpu=em4` is a base ARC EM core configuration with `-mcode-density` option. It mostly corresponds to
  the following ARC EM template in ARChitect: `em4_ecc`.
* `-mcpu=arcem` is a generic configuration, which corresponds to the default configuration in
  older GNU toolchain versions.
* `-mcpu=em4_dmips` is a full-featured ARC EM configuration for integer operations. It corresponds to the following
  ARC EM templates in ARChitect: `em4_dmips`, `em4_rtos`, `em6_dmips`, `em4_dmips_v3`, `em4_parity`, `em6_dmips_v3`,
  `em6_gp`, `em5d_voice_audio`, `em5d_nrg_v3`, `em7d_nrg_v3`, `em7d_voice_audio`, `em9d_nrg`, `em9d_voice_audio`,
  `em11d_nrg` and `em11d_voice_audio`.
* `-mcpu=em4_fpus` is like `em4_dmips` but with additional support for single-precision floating point unit.
  It corresponds to the following ARC EM templates in ARChitect: `em4_dmips_fpusp`, `em4_dmips_fpusp_v3`, `em5d_nrg_fpusp`
  and `em9d_nrg_fpusp`.
* `-mcpu=em4_fpuda` is like `em4_fpus` but with additional support for double-precision assist instructions.
  It corresponds to the following ARC EM templates in ARChitect: `em4_dmips_fpuspdp` and `em4_dmips_fpuspdp_v3`.
* `-mcpu=quarkse_em` is a configuration for ARC processor in Intel Quark SE chip. It enables additional floating
  point instructions which cannot be enable using a particular target option.

## Compatibility with Other Tools

Here is a list of corresponding options in other tools:

| GCC                    | CCAC             | ARChitect                          | nSIM                                          |
|------------------------|------------------|------------------------------------|-----------------------------------------------|
| `-mcpu=*hs*`           | `-av2hs -core4`  |                                    | `-p nsim_isa_family=av2hs -p nsim_isa_core=4` |
| `-mcpu=*em*`           | `-av2em -core6`  |                                    | `-p nsim_isa_family=av2em -p nsim_isa_core=6` |
| `-mbarrel-shifter`     | `-Xsa -Xbs`      | `-shift_option=3 -bitfield_option` | `-p nsim_isa_shift_option=3`                  |
| `-mnorm`               | `-Xnorm`         | `-bitscan_option`                  | `-p nsim_isa_bitscan_option=1`                |
| `-mswap`               | `-Xswap`         | `-swap_option`                     | `-p nsim_isa_swap_option=1`                   |
| `-mswape`              | `-Xswap`         | `-swap_option`                     | `-p nsim_isa_swap_option=1`                   |
| `-mrf16`               | `-rf16`          | `-rgf_num_regs=16`                 | `-p nsim_isa_rgf_num_regs=16`                 |
| `-mcode-density`       | `-Xcode_density` | `-code_density_option`             | `-p nsim_isa_code_density_option=2`           |
| `-mcode-density-frame` | `-Xcode_density` | `-code_density_option`             | `-p nsim_isa_code_density_option=2`           |
| `-mdiv-rem`            | `-Xdiv_rem`      | `-div_rem_option={1,2}`            | `-p nsim_isa_div_rem_option={1,2}`            |
| `-matomic`             | `-Xatomic`       | `-atomic_option`                   | `-p nsim_isa_atomic_option=1`                 |
| `-mll64`               | `-Xll64`         | `-ll64_option`                     | `-p nsim_isa_ll64_option=1`                   |
| `-munaligned-access`   | `-Xunaligned`    | `-unaligned_option`                | `-p nsim_isa_unaligned_option=1`              |
| `-mxy`                 | `-Xxy`           | XY memory option                   | `-p nsim_isa_xy=1`                            |


Multiplication options for ARC HS (`-mcpu=*hs*`):

| GCC                           | CCAC                     | ARChitect                | nSIM                       |
|-------------------------------|--------------------------|--------------------------|----------------------------|
| `-mmpy-option=0`              | `-Xmpy_option=0`         | `-mpy_option=none`       | `-p nsim_isa_mpy_option=0` |
| `-mmpy-option={2,mpy}`        | `-Xmpy_option={2,mpy}`   | `-mpy_option=mpy`        | `-p nsim_isa_mpy_option=2` |
| `-mmpy-option={3,mpy}`        | `-Xmpy_option={3,mpy}`   | `-mpy_option=mpy`        | `-p nsim_isa_mpy_option=3` |
| `-mmpy-option={4,mpy}`        | `-Xmpy_option={4,mpy}`   | `-mpy_option=mpy`        | `-p nsim_isa_mpy_option=4` |
| `-mmpy-option={5,mpy}`        | `-Xmpy_option={5,mpy}`   | `-mpy_option=mpy`        | `-p nsim_isa_mpy_option=5` |
| `-mmpy-option={6,mpy}`        | `-Xmpy_option={6,mpy}`   | `-mpy_option=mpy`        | `-p nsim_isa_mpy_option=6` |
| `-mmpy-option={7,plus_dmpy}`  | `-Xmpy_option={7,mac}`   | `-mpy_option=plus_dmpy`  | `-p nsim_isa_mpy_option=7` |
| `-mmpy-option={8,plus_macd}`  | `-Xmpy_option={8,mpyd}`  | `-mpy_option=plus_macd`  | `-p nsim_isa_mpy_option=8` |
| `-mmpy-option={9,plus_qmacw}` | `-Xmpy_option={9,qmpyh}` | `-mpy_option=plus_qmacw` | `-p nsim_isa_mpy_option=9` |

FPU options for ARC HS (`-mcpu=*hs*`):

| GCC              | CCAC                          | ARChitect                                                 | nSIM                                                                                   |
|------------------|-------------------------------|-----------------------------------------------------------|----------------------------------------------------------------------------------------|
| `-mfpu=fpus`     | `-Xfpus`                      | `-has_fpu`                                                | `-p nsim_isa_fpus_option=1`                                                            |
| `-mfpu=fpus_div` | `-Xfpus -Xfpus_div`           | `-has_fpu -fpu_div_option`                                | `-p nsim_isa_fpus_option=1 -p nsim_isa_fpus_div_option=1`                              |
| `-mfpu=fpus_fma` | `-Xfpus -Xfpu_mac`            | `-has_fpu -fpu_fma_option`                                | `-p nsim_isa_fpus_option=1 -p nsim_isa_fpu_mac_option=1`                               |
| `-mfpu=fpus_all` | `-Xfpus -Xfpus_div -Xfpu_mac` | `-has_fpu -fpu_div_option -fpu_fma_option`                | `-p nsim_isa_fpus_option=1 -p nsim_isa_fpus_div_option=1 -p nsim_isa_fpu_mac_option=1` |
| `-mfpu=fpud`     | `-Xfpud`                      | `-has_fpu -fpu_dp_option`                                 | `-p nsim_isa_fpud_option=1`                                                            |
| `-mfpu=fpud_div` | `-Xfpud -Xfpud_div`           | `-has_fpu -fpu_div_option -fpu_dp_option`                 | `-p nsim_isa_fpud_option=1 -p nsim_isa_fpud_div_option=1`                              |
| `-mfpu=fpud_fma` | `-Xfpud -Xfpu_mac`            | `-has_fpu -fpu_fma_option -fpu_dp_option`                 | `-p nsim_isa_fpud_option=1 -p nsim_isa_fpu_mac_option=1`                               |
| `-mfpu=fpud_all` | `-Xfpud -Xfpud_div -Xfpu_mac` | `-has_fpu -fpu_div_option -fpu_fma_option -fpu_dp_option` | `-p nsim_isa_fpud_option=1 -p nsim_isa_fpud_div_option=1 -p nsim_isa_fpu_mac_option=1` |


Multiplication options for ARC EM (`-mcpu=*em*`):

| GCC                     | CCAC                    | ARChitect          | nSIM                       |
|-------------------------|-------------------------|--------------------|----------------------------|
| `-mmpy-option=0`        | `-Xmpy_option=0`        | `-mpy_option=none` | `-p nsim_isa_mpy_option=0` |
| `-mmpy-option={2,wlh1}` | `-Xmpy_option={2,wlh1}` | `-mpy_option=wlh1` | `-p nsim_isa_mpy_option=2` |
| `-mmpy-option={3,wlh2}` | `-Xmpy_option={3,wlh2}` | `-mpy_option=wlh2` | `-p nsim_isa_mpy_option=3` |
| `-mmpy-option={4,wlh3}` | `-Xmpy_option={4,wlh3}` | `-mpy_option=wlh3` | `-p nsim_isa_mpy_option=4` |
| `-mmpy-option={5,wlh4}` | `-Xmpy_option={5,wlh4}` | `-mpy_option=wlh4` | `-p nsim_isa_mpy_option=5` |
| `-mmpy-option={6,wlh5}` | `-Xmpy_option={6,wlh5}` | `-mpy_option=wlh5` | `-p nsim_isa_mpy_option=6` |

FPU options for ARC EM (`-mcpu=*em*`):

| GCC               | CCAC                           | ARChitect                                        | nSIM                                                                                    |
|-------------------|--------------------------------|--------------------------------------------------|-----------------------------------------------------------------------------------------|
| `-mfpu=fpus`      | `-Xfpus`                       | `-has_fpu`                                       | `-p nsim_isa_fpus_option=1`                                                             |
| `-mfpu=fpus_div`  | `-Xfpus -Xfpus_div`            | `-has_fpu -fpu_div_option`                       | `-p nsim_isa_fpus_option=1 -p nsim_isa_fpus_div_option=1`                               |
| `-mfpu=fpus_fma`  | `-Xfpus -Xfpu_mac`             | `-has_fpu -fpu_fma_option`                       | `-p nsim_isa_fpus_option=1 -p nsim_isa_fpu_mac_option=1`                                |
| `-mfpu=fpus_all`  | `-Xfpus -Xfpus_div -Xfpu_mac`  | `-has_fpu -fpu_div_option -fpu_fma_option`       | `-p nsim_isa_fpus_option=1 -p nsim_isa_fpus_div_option=1 -p nsim_isa_fpu_mac_option=1`  |
| `-mfpu=fpuda`     | `-Xfpuda`                      | `-fpu_dp_assist`                                 | `-p nsim_isa_fpuda_option=1`                                                            |
| `-mfpu=fpuda_div` | `-Xfpuda -Xfpus_div`           | `-fpu_dp_assist -fpu_div_option`                 | `-p nsim_isa_fpuda_option=1 -p nsim_isa_fpud_div_option=1`                              |
| `-mfpu=fpuda_fma` | `-Xfpuda -Xfpu_mac`            | `-fpu_dp_assist -fpu_fma_option`                 | `-p nsim_isa_fpuda_option=1 -p nsim_isa_fpu_mac_option=1`                               |
| `-mfpu=fpuda_all` | `-Xfpuda -Xfpus_div -Xfpu_mac` | `-fpu_dp_assist -fpu_div_option -fpu_fma_option` | `-p nsim_isa_fpuda_option=1 -p nsim_isa_fpud_div_option=1 -p nsim_isa_fpu_mac_option=1` |

## Compatibility for Predefined Targets for ARC HS

Here is a list of compatible command lines for `arc-gf-elf-gcc`, `ccac` and `nsimdrv` for
all `-mcpu=` values:

* Command lines for `-mcpu=hs`:

    ```
    $ arc-gf-elf-gcc -mcpu=hs -specs=hl.specs sample.c -o sample.elf
    $ ccac -av2hs \
           -core4 \
           -Xsa \
           -Xbs \
           -Xswap \
           -Xnorm \
           -Xcode_density \
           -Xatomic \
           -Xunaligned \
           sample.c -o sample.elf
    $ nsimdrv -p nsim_isa_family=av2hs \
              -p nsim_isa_core=4 \
              -p nsim_isa_shift_option=3 \
              -p nsim_isa_bitscan_option=1 \
              -p nsim_isa_swap_option=1 \
              -p nsim_isa_code_density_option=2 \
              -p nsim_isa_atomic_option=1 \
              -p nsim_isa_unaligned_option=1 \
              sample.elf
    ```

* Command lines for `-mcpu=hs34`:

    ```
    $ arc-gf-elf-gcc -mcpu=hs34 -specs=hl.specs sample.c -o sample.elf
    $ ccac -av2hs \
           -core4 \
           -Xsa \
           -Xbs \
           -Xswap \
           -Xnorm \
           -Xcode_density \
           -Xatomic \
           -Xunaligned \
           -Xmpy_option=mpy \
           sample.c -o sample.elf
    $ nsimdrv -p nsim_isa_family=av2hs \
              -p nsim_isa_core=4 \
              -p nsim_isa_shift_option=3 \
              -p nsim_isa_bitscan_option=1 \
              -p nsim_isa_swap_option=1 \
              -p nsim_isa_code_density_option=2 \
              -p nsim_isa_atomic_option=1 \
              -p nsim_isa_unaligned_option=1 \
              -p nsim_isa_mpy_option=2 \
              sample.elf
    ```

* Command lines for `-mcpu=archs`:

    ```
    $ arc-gf-elf-gcc -mcpu=archs -specs=hl.specs sample.c -o sample.elf
    $ ccac -av2hs \
           -core4 \
           -Xsa \
           -Xbs \
           -Xswap \
           -Xnorm \
           -Xcode_density \
           -Xatomic \
           -Xunaligned \
           -Xmpy_option=mpy \
           -Xdiv_rem \
           -Xll64 \
           sample.c -o sample.elf
    $ nsimdrv -p nsim_isa_family=av2hs \
              -p nsim_isa_core=4 \
              -p nsim_isa_shift_option=3 \
              -p nsim_isa_bitscan_option=1 \
              -p nsim_isa_swap_option=1 \
              -p nsim_isa_code_density_option=2 \
              -p nsim_isa_atomic_option=1 \
              -p nsim_isa_unaligned_option=1 \
              -p nsim_isa_mpy_option=2 \
              -p nsim_isa_div_rem_option=2 \
              -p nsim_isa_ll64_option=1 \
              sample.elf
    ```

* Command lines for `-mcpu=hs38` (the same is applicable for `hs4x` and `hs4xd`):

    ```
    $ arc-gf-elf-gcc -mcpu=hs38 -specs=hl.specs sample.c -o sample.elf
    $ ccac -av2hs \
           -core4 \
           -Xsa \
           -Xbs \
           -Xswap \
           -Xnorm \
           -Xcode_density \
           -Xatomic \
           -Xunaligned \
           -Xmpy_option=qmpyh \
           -Xdiv_rem \
           -Xll64 \
           sample.c -o sample.elf
    $ nsimdrv -p nsim_isa_family=av2hs \
              -p nsim_isa_core=4 \
              -p nsim_isa_shift_option=3 \
              -p nsim_isa_bitscan_option=1 \
              -p nsim_isa_swap_option=1 \
              -p nsim_isa_code_density_option=2 \
              -p nsim_isa_atomic_option=1 \
              -p nsim_isa_unaligned_option=1 \
              -p nsim_isa_mpy_option=9 \
              -p nsim_isa_div_rem_option=2 \
              -p nsim_isa_ll64_option=1 \
              sample.elf
    ```

* Command lines for `-mcpu=hs38_linux`:

    ```
    $ arc-gf-elf-gcc -mcpu=hs38_linux -specs=hl.specs sample.c -o sample.elf
    $ ccac -av2hs \
           -core4 \
           -Xsa \
           -Xbs \
           -Xswap \
           -Xnorm \
           -Xcode_density \
           -Xatomic \
           -Xunaligned \
           -Xmpy_option=qmpyh \
           -Xdiv_rem \
           -Xll64 \
           -Xfpud \
           -Xfpud_div \
           -Xfpu_mac \
           sample.c -o sample.elf
        $ nsimdrv -p nsim_isa_family=av2hs \
                -p nsim_isa_core=4 \
                -p nsim_isa_shift_option=3 \
                -p nsim_isa_bitscan_option=1 \
                -p nsim_isa_swap_option=1 \
                -p nsim_isa_code_density_option=2 \
                -p nsim_isa_atomic_option=1 \
                -p nsim_isa_unaligned_option=1 \
                -p nsim_isa_mpy_option=9 \
                -p nsim_isa_div_rem_option=2 \
                -p nsim_isa_ll64_option=1 \
                -p nsim_isa_fpud_option=1 \
                -p nsim_isa_fpud_div_option=1 \
                -p nsim_isa_fpu_mac_option=1 \
                sample.elf
    ```

## Compatibility for Predefined Targets for ARC EM

Here is a list of compatible command lines for `arc-gf-elf-gcc`, `ccac` and `nsimdrv` for
all `-mcpu=` values:

* Command lines for `-mcpu=em`:

    ```
    $ arc-gf-elf-gcc -mcpu=em -specs=hl.specs sample.c -o sample.elf
    $ ccac -av2em \
           -core6 \
           sample.c -o sample.elf
    $ nsimdrv -p nsim_isa_family=av2em \
              -p nsim_isa_core=6 \
              sample.elf
    ```

* Command lines for `-mcpu=em_mini`:

    ```
    $ arc-gf-elf-gcc -mcpu=em_mini -specs=hl.specs sample.c -o sample.elf
    $ ccac -av2em \
           -core6 \
           -rf16 \
           sample.c -o sample.elf
    $ nsimdrv -p nsim_isa_family=av2em \
              -p nsim_isa_core=6 \
              -p nsim_isa_rgf_num_regs=16 \
              sample.elf
    ```

* Command lines for `-mcpu=em4`:

    ```
    $ arc-gf-elf-gcc -mcpu=em4 -specs=hl.specs sample.c -o sample.elf
    $ ccac -av2em \
           -core6 \
           -Xcode_density \
           sample.c -o sample.elf
    $ nsimdrv -p nsim_isa_family=av2em \
              -p nsim_isa_core=6 \
              -p nsim_isa_code_density_option=2 \
              sample.elf
    ```

* Command lines for `-mcpu=arcem`:

    ```
    $ arc-gf-elf-gcc -mcpu=arcem -specs=hl.specs sample.c -o sample.elf
    $ ccac -av2em \
           -core6 \
           -Xcode_density \
           -Xsa \
           -Xbs \
           -Xmpy_option=wlh1 \
           sample.c -o sample.elf
    $ nsimdrv -p nsim_isa_family=av2em \
              -p nsim_isa_core=6 \
              -p nsim_isa_code_density_option=2 \
              -p nsim_isa_shift_option=3 \
              -p nsim_isa_mpy_option=2 \
              sample.elf
    ```

* Command lines for `-mcpu=em4_dmips`:

    ```
    $ arc-gf-elf-gcc -mcpu=em4_dmips -specs=hl.specs sample.c -o sample.elf
    $ ccac -av2em \
           -core6 \
           -Xcode_density \
           -Xsa \
           -Xbs \
           -Xmpy_option=wlh1 \
           -Xnorm \
           -Xswap \
           -Xdiv_rem \
           sample.c -o sample.elf
    $ nsimdrv -p nsim_isa_family=av2em \
              -p nsim_isa_core=6 \
              -p nsim_isa_code_density_option=2 \
              -p nsim_isa_shift_option=3 \
              -p nsim_isa_mpy_option=2 \
              -p nsim_isa_bitscan_option=1 \
              -p nsim_isa_swap_option=1 \
              -p nsim_isa_div_rem_option=2 \
              sample.elf
    ```

* Command lines for `-mcpu=em4_fpus`:

    ```
    $ arc-gf-elf-gcc -mcpu=em4_fpus -specs=hl.specs sample.c -o sample.elf
    $ ccac -av2em \
           -core6 \
           -Xcode_density \
           -Xsa \
           -Xbs \
           -Xmpy_option=wlh1 \
           -Xnorm \
           -Xswap \
           -Xdiv_rem \
           -Xfpus \
           sample.c -o sample.elf
    $ nsimdrv -p nsim_isa_family=av2em \
              -p nsim_isa_core=6 \
              -p nsim_isa_code_density_option=2 \
              -p nsim_isa_shift_option=3 \
              -p nsim_isa_mpy_option=2 \
              -p nsim_isa_bitscan_option=1 \
              -p nsim_isa_swap_option=1 \
              -p nsim_isa_div_rem_option=2 \
              -p nsim_isa_fpus_option=1 \
              sample.elf
    ```

* Command lines for `-mcpu=em4_fpuda`:

    ```
    $ arc-gf-elf-gcc -mcpu=em4_fpuda -specs=hl.specs sample.c -o sample.elf
    $ ccac -av2em \
           -core6 \
           -Xcode_density \
           -Xsa \
           -Xbs \
           -Xmpy_option=wlh1 \
           -Xnorm \
           -Xswap \
           -Xdiv_rem \
           -Xfpuda \
           sample.c -o sample.elf
    $ nsimdrv -p nsim_isa_family=av2em \
              -p nsim_isa_core=6 \
              -p nsim_isa_code_density_option=2 \
              -p nsim_isa_shift_option=3 \
              -p nsim_isa_mpy_option=2 \
              -p nsim_isa_bitscan_option=1 \
              -p nsim_isa_swap_option=1 \
              -p nsim_isa_div_rem_option=2 \
              -p nsim_isa_fpuda_option=1 \
              sample.elf
    ```
