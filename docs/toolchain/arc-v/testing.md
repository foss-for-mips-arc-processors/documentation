# ARC-V micro-DSP Extension

You can check support of ARC-V micro-DSP instructions using examples from
MetaWare Development Toolkit. Ensure, that environment for MetaWare
Development Toolkit is configured properly and `METAWARE_HOME` variable
is set.

Copy the FFT example, build it using Picolibc-based toolchain and run
it using nSIM:

```
$ cp -f ${METAWARE_ROOT}/arc/examples/arcv_micro_dsp/micro_fft/test.c ./test.c
$ riscv64-gf-elf-gcc \
    -march=rv32e_zicsr_zifencei_zihintpause_zca_zcb_zcmp_zcmt_zba_zbb_zbs_zicond_zicbom_zicbop_xarcvudsp \
    -mno-strict-align -mabi=ilp32e -mtune=arc-v-rmx-100-series -mmpy-option=1c \
    -specs=picolibc.specs --crt0=arcv-semihost --oslib=semihost -DTYPE=short -DTYPE_W=int \
    -DSCALE_DOWN=16 -DPASS_DOWNSCALE=1 -DFFT_LOG2_LEN=9 -DEL_SIZE=16 -DPREGENERATED_REFERENCE \
    -DNEED_BITREV_REORDER -DBITREV_INSTRUCTION_ -DGENERATE_REFERENCE_ \
    test.c -o test.elf
$ nsimdrv -tcf=${METAWARE_ROOT}/arc/tcf/rmx100_udsp.tcf -on nsim_semihosting -on nsim_ncam_experimental_option test.elf
Preparation: 54
Cycles: 621213
SNR:  55.1531 dB
PASS
```

It's expected that the sample prints `PASS`. Also, verify that the
binary contains micro-DSP instructions:

```
$ riscv64-gf-elf-objdump -d test.elf | grep "arcv\."
     212:       84e7a757                arcv.xvsadd.vv  a4,a5,a4,e16,m1
     224:       a6e6a757                arcv.xvsra.vx   a4,a3,a4,e16,m1
     23c:       8cf72757                arcv.xvssub.vv  a4,a4,a5,e16,m1
     250:       a6e6a757                arcv.xvsra.vx   a4,a3,a4,e16,m1
     2d0:       20f727db                arcv.xvscmul.vv a5,a4,a5,e16,m1
     2f8:       84e7a7d7                arcv.xvsadd.vv  a5,a5,a4,e16,m1
     2fe:       a6f72757                arcv.xvsra.vx   a4,a4,a5,e16,m1
     310:       8cf727d7                arcv.xvssub.vv  a5,a4,a5,e16,m1
     316:       a6f72757                arcv.xvsra.vx   a4,a4,a5,e16,m1
```
