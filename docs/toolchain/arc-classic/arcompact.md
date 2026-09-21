# Options for ARCompact

## Target Options

Here is a list of target specific options for ARCompact with
information about availability for ARC 700 and ARC 600 targets:

| Feature                                                             | GCC option           | ARC 700   | ARC 600   |
|---------------------------------------------------------------------|----------------------|-----------|-----------|
| Select a set of predefined values for target options                | `-mcpu=...`          | Available | Available |
| Enable shift instructions                                           | `-mbarrel-shifter`   | Available | Available |
| Enable bitscan instructions                                         | `-mnorm`             | Available | Available |
| Enable swap instruction                                             | `-mswap`             | Available | Available |
| Enable swap byte ordering instruction                               | `-mswape`            | Available | Available |
| Use 16 register file                                                | `-mrf16`[^1]         | Available | Available |
| Enable atomic instructions                                          | `-matomic`           | Available | N/A       |
| Generate Single Precision FPX (compact) instructions                | `-mspfp`             | Available | N/A       |
| Generate Single Precision FPX (compact) instructions                | `-mspfp-compact`     | Available | N/A       |
| Generate Single Precision FPX (fast) instructions                   | `-mspfp-fast`        | Available | N/A       |
| Generate Double Precision FPX (compact) instructions                | `-mdpfp`             | Available | N/A       |
| Generate Double Precision FPX (compact) instructions                | `-mdpfp-compact`     | Available | N/A       |
| Generate Double Precision FPX (fast) instructions                   | `-mdpfp-fast`        | Available | N/A       |
| Enable generation of SIMD instructions via target-specific builtins | `-msimd`             | Available | N/A       |
| Enable Argonaut double precision floating point extensions          | `-margonaut`         | Available | Available |
| Generate extended arithmetic instructions                           | `-mea`               | Available | N/A       |
| Generate `mul64` and `mulu64` instructions                          | `-mmul64`            | N/A       | Available |
| Generate 32x16 multiply and mac instructions                        | `-mmul32x16`         | N/A       | Available |
| Enable unaligned word and halfword accesses to packed data          | `-munaligned-access` | Available | Available |
| Enable XY memory extension for the assembler (DSP version 3)        | `-mxy`               | Available | Available |
| Enable uncached access for volatile memories                        | `-mvolatile-di`      | Available | Available |

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
$ arc-gf-elf-gcc -mcpu=arc700 --target-help -Q
The following options are target specific:
...
  -mbitops                              [disabled]
  -mbranch-index                        [disabled]
  -mcase-vector-pcrel                   [disabled]
  -mcmem                                [disabled]
  -mcode-density                        [disabled]
  -mcode-density-frame                  [disabled]
  -mcompact-casesi                      [disabled]
  -mcpu=CPU                             arc700
...
```

## Default Options for Base Targets

A particular ARCompact target is selected by `-mcpu=` option. Each `-mcpu=` value selects
a corresponding prebuilt library and a set of options:

* `-mcpu=` values for ARC 700: `arc700`.
* `-mcpu=` values for ARC 600: `arc600`, `arc600_norm`, `arc600_mul64`,
  `arc600_mul32x16`, `arc601`, `arc601_norm`, `arc601_mul64`, `arc601_mul32x16`.

Here is a table of default values for targets options for base ARC 700 (`-mcpu=arc700`) and ARC 600 (`-mcpu=arc601`) configurations:

| GCC option           | `-mcpu=arc700` | `-mcpu=arc601` |
|----------------------|----------------|----------------|
| `-mbarrel-shifter`   | On             | Off            |
| `-mnorm`             | On             | Off            |
| `-mswap`             | On             | Off            |
| `-mswape`            | Off            | Off            |
| `-mrf16`             | Off            | Off            |
| `-matomic`           | Off            | N/A            |
| `-mspfp`             | Off            | N/A            |
| `-mspfp-compact`     | Off            | N/A            |
| `-mspfp-fast`        | Off            | N/A            |
| `-mdpfp`             | Off            | N/A            |
| `-mdpfp-compact`     | Off            | N/A            |
| `-mdpfp-fast`        | Off            | N/A            |
| `-msimd`             | Off            | N/A            |
| `-mmargonaut`        | Off            | Off            |
| `-mea`               | Off            | N/A            |
| `-mmul64`            | N/A            | Off            |
| `-mmul32x16`         | N/A            | Off            |
| `-munaligned-access` | Off            | Off            |
| `-mxy`               | Off            | Off            |
| `-mvolatile-di`      | On             | On             |

## Selecting Targets

A particular ARCompact target is selected by `-mcpu=` option. Each `-mcpu=` value selects
a corresponding prebuilt library and a set of options in addition to default values
set by `-mcpu=arc700` (for ARC 700) or `-mcpu=arc601` (for ARC 600):

| `-mcpu=`          | `-mnorm` | `-mswap` | `-mbarrel-shifter` | Multiplier   |
|-------------------|----------|----------|--------------------|--------------|
| `arc700`          | On       | On       | On                 | Full support |
| `arc600`          |          |          | On                 |              |
| `arc600_norm`     | On       |          | On                 |              |
| `arc600_mul64`    | On       |          | On                 | `-mmul64`    |
| `arc600_mul32x16` | On       |          | On                 | `-mmul32x16` |
| `arc601`          |          |          |                    |              |
| `arc601_norm`     | On       |          |                    |              |
| `arc601_mul64`    | On       |          |                    | `-mmul64`    |
| `arc601_mul32x16` | On       |          |                    | `-mmul32x16` |

## Compatibility with Other Tools

Refer to [Compatibility with Other Tools for ARCv2](./arcv2.md#compatibility-with-other-tools) for compatibility
information for options common to ARCompact and ARCv2. For other ARCompact specific options see below.

Multiplication Options for ARC 700:

| GCC    | CCAC/MDB | nSIM             |
|--------|----------|------------------|
| `-mea` | `-Xea`   | `nsim_isa_sat=1` |

Multiplication Options for ARC 600:

| GCC          | CCAC/MDB     | nSIM                  |
|--------------|--------------|-----------------------|
| `-mmul32x16` | `-Xmul32x16` | `nsim_isa_mul32x16=1` |
| `-mmul64`    | `-Xmult32d`  | `nsim_isa_mult32d=1`  |
| N/A          | `-Xea`       | `nsim_isa_sat=1`      |
