# Options for ARCv3

## Target Options

Here is a list of target specific options for ARCv3:

| Feature                                                          | GCC option                    |
|------------------------------------------------------------------|-------------------------------|
| Select a set of predefined values for target options             | `-mcpu={hs5x,hs58,hs6x,hs68}` |
| Enable 128-bit load and store operations (ARC HS6x only)         | `-m128`                       |
| Enable 64-bit load and store operations (ARC HS5x only)          | `-mll64`                      |
| Enable atomic instructions                                       | `-matomic={0,1,2,3}`          |
| Enable `NORM`, `NORMH`, `FFS`, `FLS`, `NORML`, `FFSL` and `FLSL` | `-mbitscan`                   |
| Enable code-density instructions                                 | `-mcode-density`              |
| Enable integer SIMD instructions                                 | `-msimd`                      |
| Enable `DIV` and `REM` instructions                              | `-mdiv-rem`                   |
| Enable unaligned access for packed data                          | `-munaligned-access`          |
| Generate `BBITx` instructions during combiner step               | `-mbbit`                      |
| Generate `BRcc` instructions during combiner step                | `-mbrcc`                      |
| Enable uncached access for volatile memories                     | `-mvolatile-di`               |
| Floating point unit (none, single-precision or double-precision) | `-mfpu={none,fpus,fpud}`      |
| Enable wide floating point SIMD support                          | `-mwide`                      |
| Use FPRs for memory operations like `memcpy` to free GPRs        | `-mfpmov`                     |

A particular ARCv3 target is selected by `-mcpu=` option. Each `-mcpu=` value selects
a corresponding prebuilt library and a set of options:

* `-mcpu=hs5x` - ARC HS5x targets without support of 64-bit load and stores. It corresponds to `hs58_base.tcf` template.
* `-mcpu=hs58` - ARC HS5x targets with support of 64-bit load and stores. It corresponds to `hs58_full.tcf` template.
* `-mcpu=hs6x` - ARC HS6x targets without support of 128-bit load and stores (default). It corresponds to `hs68_base.tcf` template.
* `-mcpu=hs68` - ARC HS6x targets with support of 128-bit load and stores. It corresponds to `hs68_full.tcf` template.

Here is a list of default values selected for target options:

| Options              | `-mcpu=hs5x` | `-mcpu=hs58` | `-mcpu=hs6x` | `-mcpu=hs68` |
|----------------------|--------------|--------------|--------------|--------------|
| `-m128`              | —            | —            | Off          | On           |
| `-mll64`             | Off          | On           | —            | —            |
| `-matomic=...`       | `1`          | `1`          | `1`          | `1`          |
| `-mbitscan`          | On           | On           | On           | On           |
| `-mcode-density`     | On           | On           | On           | On           |
| `-msimd`             | On           | On           | On           | On           |
| `-mdiv-rem`          | On           | On           | On           | On           |
| `-munaligned-access` | On           | On           | On           | On           |
| `-mbbit`             | Off          | Off          | Off          | Off          |
| `-mbrcc`             | Off          | Off          | Off          | Off          |
| `-mfpu=...`          | `none`       | `none`       | `none`       | `none`       |
| `-mwide`             | On           | On           | On           | On           |
| `-mfpmov`            | Off          | Off          | Off          | Off          |
| `-mvolatile-di`      | Off          | Off          | Off          | Off          |

You can find a short description for all target options using `--target-help` option:

```
$ arc64-gf-elf-gcc --target-help
The following options are target specific:
  -m128                       Enable wide data transfer support.
  -matomic=                   Enable atomic instructions: {0, 1, 2, 3}.
  -mbbit                      Generate BBITx instructions during combiner step.
  -mbitscan                   Enable NORM, NORMH, FFS, FLS, NORML, FFSL, and FLSL bitscan instructions.
  -mbrcc                      Generate BRcc instructions during combiner step.
...
```

You can find out what target options are enabled or disabled with a particular set of options using `-Q` flag:

```
$ arc64-gf-elf-gcc -mcpu=hs68 --target-help -Q
The following options are target specific:
  -m128                                 [enabled]
  -matomic=                             1
  -mbbit                                [disabled]
  -mbitscan                             [enabled]
  -mbrcc                                [disabled]
...
```

## Controlling the Memory Model

| `-mcmodel` | Memory size for Data/Code     |
|------------|-------------------------------|
| `small`    | 1MB region                    |
| `medium`   | 4GB region (used by `-fpic`)  |
| `large`    | Full memory (used by `-fPIC`) |

## Compatibility with Other Tools

Here is a list of corresponding options in other tools:

| GCC                  | CCAC                 | ARChitect                  | nSIM                                          |
|----------------------|----------------------|----------------------------|-----------------------------------------------|
| `-mcpu={hs68,hs6x}`  | `-arc64 -core0`      |                            | `-p nsim_isa_family=arc64 -p nsim_isa_core=0` |
| `-mcpu={hs58,hs5x}`  | `-av3hs -core0`      |                            | `-p nsim_isa_family=av3hs -p nsim_isa_core=0` |
| `-m128` (HS6x)       | `-Xm128`             | `-m128_option`             | `-p nsim_isa_m128_option=1`                   |
| `-mll64` (HS5x)      | `-Xll64`             | `-ll64_option`             | `-p nsim_isa_ll64_option=1`                   |
| `-matomic={0,1,2,3}` | `-Xatomic={0,1,2,3}` | `-atomic_option={0,1,2,3}` | `-p nsim_isa_atomic_option={0,1,2,3}`         |
| `-mbitscan`          | —                    | —                          | —                                             |
| `-mcode-density`     | —                    | —                          | —                                             |
| `-msimd` (HS5x)      | `-Xmpy_option=qmpyh` | —                          | `-p nsim_isa_mpy_option=9`                    |
| `-msimd` (HS6x)      | —                    | —                          | —                                             |
| `-mdiv-rem` (HS5x)   | `-Xdiv_rem=radix4`   | —                          | `-p nsim_isa_div_rem_option=2`                |
| `-mdiv-rem` (HS6x)   | —                    | —                          | —                                             |
| `-munaligned-access` | `-Xunaligned`        | —                          | `-p nsim_isa_unaligned_option=1`              |
| `-mbbit`             | —                    | —                          | —                                             |
| `-mbrcc`             | —                    | —                          | —                                             |
| `-mvolatile-di`      | —                    | —                          | —                                             |

Here is a list of single-precision FPU features enabled by `-mfpu=fpus`:

| Feature                       | CCAC       | ARChitect        | nSIM                          |
|-------------------------------|------------|------------------|-------------------------------|
| Single-precision instructions | `-Xfp_sp`  | `-has_fp`        | `-p nsim_isa_has_fp=1`        |
| Half-precision instructions   | `-Xfp_hp`  | `-fp_hp_option`  | `-p nsim_isa_fp_hp_option=1`  |
| Division instructions         | `-Xfp_div` | `-fp_div_option` | `-p nsim_isa_fp_div_option=1` |
| Vector instructions           | `-Xfp_vec` | `-fp_vec_option` | `-p nsim_isa_fp_vec_option=1` |

Here is a list of double-precision FPU features enabled by `-mfpu=fpud`:

| Feature                       | CCAC       | ARChitect        | nSIM                          |
|-------------------------------|------------|------------------|-------------------------------|
| Single-precision instructions | `-Xfp_sp`  | `-has_fp`        | `-p nsim_isa_has_fp=1`        |
| Double-precision instructions | `-Xfp_dp`  | `-fp_dp_option`  | `-p nsim_isa_fp_dp_option=1`  |
| Half-precision instructions   | `-Xfp_hp`  | `-fp_hp_option`  | `-p nsim_isa_fp_hp_option=1`  |
| Division instructions         | `-Xfp_div` | `-fp_div_option` | `-p nsim_isa_fp_div_option=1` |
| Vector instructions           | `-Xfp_vec` | `-fp_vec_option` | `-p nsim_isa_fp_vec_option=1` |

Other FPU options:

| GCC       | CCAC        | ARChitect         | nSIM                           |
|-----------|-------------|-------------------|--------------------------------|
| `-mwide`  | `-Xfp_wide` | `-fp_wide_option` | `-p nsim_isa_fp_wide_option=1` |
| `-mfpmov` | —           | —                 | —                              |

## Compatibility for Predefined Targets

Here is a list of compatible command lines for `arc64-gf-elf-gcc`, `ccac` and `nsimdrv` for
all `-mcpu=` values:

* Command lines for `-mcpu=hs68`:

    ```
    $ arc64-gf-elf-gcc -mcpu=hs68 -specs=hl.specs sample.c -o sample.elf
    $ ccac -arc64 -core0 -Xm128 -Xatomic=1 -Xunaligned sample.c -o sample.elf
    $ nsimdrv -p nsim_isa_family=arc64 \
              -p nsim_isa_core=0 \
              -p nsim_isa_atomic_option=1 \
              -p nsim_isa_unaligned_option=1 \
              -p nsim_isa_m128_option=1 \
              sample.elf
    ```

* Command lines for `-mcpu=hs6x`:

    ```
    $ arc64-gf-elf-gcc -mcpu=hs6x -specs=hl.specs sample.c -o sample.elf
    $ ccac -arc64 -core0 -Xatomic=1 -Xunaligned sample.c -o sample.elf
    $ nsimdrv -p nsim_isa_family=arc64 \
              -p nsim_isa_core=0 \
              -p nsim_isa_atomic_option=1 \
              -p nsim_isa_unaligned_option=1 \
              sample.elf
    ```

* Command lines for `-mcpu=hs58`:

    ```
    $ arc64-gf-elf-gcc -mcpu=hs58 -specs=hl.specs sample.c -o sample.elf
    $ ccac -av3hs -core0 -Xll64 -Xatomic=1 -Xunaligned \
           -Xmpy_option=qmpyh -Xdiv_rem=radix4 sample.c -o sample.elf
    $ nsimdrv -p nsim_isa_family=av3hs \
              -p nsim_isa_core=0 \
              -p nsim_isa_atomic_option=1 \
              -p nsim_isa_unaligned_option=1 \
              -p nsim_isa_ll64_option=1 \
              -p nsim_isa_mpy_option=9 \
              -p nsim_isa_div_rem_option=2 \
              sample.elf
    ```

* Command lines for `-mcpu=hs5x`:

    ```
    $ arc64-gf-elf-gcc -mcpu=hs5x -specs=hl.specs sample.c -o sample.elf
    $ ccac -av3hs -core0 -Xatomic=1 -Xunaligned -Xmpy_option=qmpyh \
           -Xdiv_rem=radix4 sample.c -o sample.elf
    $ nsimdrv -p nsim_isa_family=av3hs \
              -p nsim_isa_core=0 \
              -p nsim_isa_atomic_option=1 \
              -p nsim_isa_unaligned_option=1 \
              -p nsim_isa_mpy_option=9 \
              -p nsim_isa_div_rem_option=2 \
              sample.elf
    ```
