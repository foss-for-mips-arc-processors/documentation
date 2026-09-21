# Running on nSIM

Consider this code example which prints all passed command
line arguments:

```c
#include <stdio.h>

int main(int argc, char *argv[]) {
    for (int i = 0; i < argc; i++) {
        printf("%d: %s\n", i, argv[i]);
    }
    return 0;
}
```

## Build and Run for Base RMX-100

Build with Picolibc-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv32imac_zcb_zba_zbb_zbs \
        -mabi=ilp32 \
        -mtune=arc-v-rmx-100-series \
        -specs=picolibc.specs \
        --oslib=semihost \
        --crt0=arcv-semihost \
        args.c -o args.elf
```

Build with Newlib-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv32imac_zcb_zba_zbb_zbs \
        -mabi=ilp32 \
        -mtune=arc-v-rmx-100-series \
        -specs=semihost.specs \
        -specs=arcv.specs \
        -T arcv.ld \
        args.c -o args.elf
```

Run on nSIM:

```
$ nsimdrv -p nsim_isa_family=rv32 \
          -p nsim_isa_ext=-all.i.m.a.c.zcb.zba.zbb.zbs.zicsr \
          -p nsim_semihosting=1 \
          -p enable_exceptions=0 \
          -- args.elf one two three
0: args.elf
1: one
2: two
3: three
```

## Build and Run for Base RMX-500

Build with Picolibc-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv32imac_zcb_zba_zbb_zbs \
        -mabi=ilp32 \
        -mtune=arc-v-rmx-500-series \
        -specs=picolibc.specs \
        --oslib=semihost \
        --crt0=arcv-semihost \
        args.c -o args.elf
```

Build with Newlib-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv32imac_zcb_zba_zbb_zbs \
        -mabi=ilp32 \
        -mtune=arc-v-rmx-500-series \
        -specs=semihost.specs \
        -specs=arcv.specs \
        -T arcv.ld \
        args.c -o args.elf
```

Run on nSIM:

```
$ nsimdrv -p nsim_isa_family=rv32 \
          -p nsim_isa_ext=-all.i.m.a.c.zcb.zba.zbb.zbs.zicsr \
          -p nsim_semihosting=1 \
          -p enable_exceptions=0 \
          -- args.elf one two three
0: args.elf
1: one
2: two
3: three
```

## Build and Run for Base RHX-100

Build with Picolibc-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv32imafc_zcb_zba_zbb_zbs \
        -mabi=ilp32f \
        -mtune=arc-v-rhx-100-series \
        -specs=picolibc.specs \
        --oslib=semihost \
        --crt0=arcv-semihost \
        args.c -o args.elf
```

Build with Newlib-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv32imafc_zcb_zba_zbb_zbs \
        -mabi=ilp32f \
        -mtune=arc-v-rhx-100-series \
        -specs=semihost.specs \
        -specs=arcv.specs \
        -T arcv.ld \
        args.c -o args.elf
```

Run on nSIM:

```
$ nsimdrv -p nsim_isa_family=rv32 \
          -p nsim_isa_ext=-all.i.m.a.f.c.zcb.zba.zbb.zbs.zicsr \
          -p nsim_semihosting=1 \
          -p enable_exceptions=0 \
          -- args.elf one two three
0: args.elf
1: one
2: two
3: three
```

## Build and Run for Base RPX-100

Build with Picolibc-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv64imafdc_zcb_zba_zbb_zbs \
        -mabi=lp64d \
        -mtune=arc-v-rpx-100-series \
        -mcmodel=medany \
        -specs=picolibc.specs \
        --oslib=semihost \
        --crt0=arcv-semihost \
        args.c -o args.elf
```

Build with Newlib-based toolchain:

```
$ riscv64-gf-elf-gcc \
        -march=rv64imafdc_zcb_zba_zbb_zbs \
        -mabi=lp64d \
        -mtune=arc-v-rpx-100-series \
        -mcmodel=medany \
        -specs=semihost.specs \
        -specs=arcv.specs \
        -T arcv.ld \
        args.c -o args.elf
```

Run on nSIM:

```
$ nsimdrv -p nsim_isa_family=rv64 \
          -p nsim_isa_ext=-all.i.m.a.f.d.c.zcb.zba.zbb.zbs.zicsr \
          -p nsim_semihosting=1 \
          -p enable_exceptions=0 \
          -- args.elf one two three
0: args.elf
1: one
2: two
3: three
```
