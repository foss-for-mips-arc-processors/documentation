# M8500 xmipstrig Extension

Validate with a MIPS-enabled QEMU build. The `xmipstrig` extension is not supported on nSIM yet.

`check_trig_test.c`:

```c
#include <stdio.h>
#include <math.h>
#include <stdbool.h>
#include <riscv_mips.h>

/* asin in Hz units.  Same sequence as riscv_output_asinf_hz.  */
static inline float asinhz(float x) {
  float x2 = x * x;
  float one_minus_x2 = 1.0f - x2;
  float atan_a = __riscv_mips_fatan_hz(__riscv_mips_ffrecip(x)
                                       * __riscv_mips_ffsqrt(one_minus_x2));
  float atan_b = __riscv_mips_fatan_hz(x
                                       * __riscv_mips_ffrsqrt(one_minus_x2));
  float quarter = __builtin_copysignf(0.25f, x);
  return __builtin_fmaxf(atan_b, quarter - atan_a);
}

static inline float tanhz(float x) {
  float y = 0.25f - x;
  float a = __riscv_mips_ftan_hz(x);
  float b = __riscv_mips_ffrecip(__riscv_mips_ftan_hz(y));
  return __builtin_fmaxf(a, b);
}

static inline float atanhz(float x) {
  float a = __riscv_mips_fatan_hz(x);
  float b = 0.25f - __riscv_mips_fatan_hz(__riscv_mips_ffrecip(x));
  return __builtin_fminf(a, b);
}

const float kTwoPi = 2*M_PI;
const float kInvTwoPi = 1 / kTwoPi;

int pass_count = 0;
int fail_count = 0;

bool compare_results(float a, float b, float tolerance) {
  return (isnan(a) && isnan(b)) || (isinf(a) && isinf(b)) || (fabs(a-b) <= tolerance);
}

#define RUN_TEST(name, ref, result) \
  do { \
    bool pass = compare_results((ref), (result), tolerance); \
    if (pass) pass_count++; else fail_count++; \
  } while (0)

int main() {
  float test_values[] = { -1.0, -0.875, -0.75, -0.625 ,-0.5, -0.375, -0.125, 0.0, 0.125, 0.25, 0.375, 0.5, 0.625, 0.75, 0.875, 1.0 };

  int num_tests = sizeof(test_values) / sizeof(test_values[0]);
  float tolerance = 1e-6;

  for (int i = 0; i < num_tests; i++) {
    float x = test_values[i];
    float xhz = x * kInvTwoPi;

    RUN_TEST("sinhz", sinf(x), __riscv_mips_fsin_hz(xhz));
    RUN_TEST("asinhz", asinf(x), asinhz(x) * kTwoPi);
    RUN_TEST("coshz", cosf(x), __riscv_mips_fcos_hz(xhz));
    RUN_TEST("tanhz", tanf(x), tanhz(xhz));
    RUN_TEST("atanhz", atanf(x), atanhz(x) * kTwoPi);

    if (fabsf(xhz) <= 0.125f)
      RUN_TEST("versinhz", 1.0f - cosf(x), __riscv_mips_fversin_hz(xhz));
    RUN_TEST("log2", log2(x), __riscv_mips_fflog2(x));
    RUN_TEST("exp2", exp2(x), __riscv_mips_ffexp2(x));
    RUN_TEST("recip", 1.0f / x, __riscv_mips_ffrecip(x));
    RUN_TEST("sqrt", sqrt(x), __riscv_mips_ffsqrt(x));
    RUN_TEST("rcpsq", 1.0f / sqrt(x), __riscv_mips_ffrsqrt(x));
  }

  printf("\nTotal tests: %d\n", pass_count + fail_count);
  printf("  PASS: %d\n", pass_count);
  printf("  FAIL: %d\n", fail_count);

  return fail_count ? 1 : 0;
}

```

Compile it and run it on QEMU:

```
$ riscv64-gf-elf-gcc \
    --specs=picolibc.specs --oslib=semihost --crt0=semihost \
    -mabi=ilp32d -O2 -mcpu=mips-m8500 \
    -Wl,--defsym=__flash=0x80000000 -Wl,--defsym=__ram=0x82000000 \
    check_trig_test.c -o check_trig_test.elf
$ qemu-system-riscv32 \
    -semihosting-config enable=on,target=native \
    -monitor none -serial none -nographic \
    -machine virt -cpu max,c=off,zcmp=on,zcd=off,xmipstrig=on,xmipslsp=on,xmipscmov=on -bios none \
    -kernel check_trig_test.elf
```

It's expected that the sample prints:

```
Total tests: 172
  PASS: 172
  FAIL: 0
```

Also, verify that the binary contains trigonometric instructions:

```
$ riscv64-gf-elf-objdump -d check_trig_test.elf | grep "mips\."
80000118:       5017c80b                mips.lwp  a6,a0,0(a5)
8000011c:       6097c58b                mips.lwp  a1,a2,8(a5)
80000122:       5107508b                mips.swp  a6,a0,0(a4)
80000126:       60b7548b                mips.swp  a1,a2,8(a4)
8000016e:       e194078b                mips.ffexp2.s  fa5,fs0
800001e2:       e1c4070b                mips.ffsqrt.s  fa4,fs0
80000206:       e1d4040b                mips.ffrsqrt.s  fs0,fs0
8000025a:       e004878b                mips.fsinhz.s  fa5,fs1
8000029c:       e1b40a8b                mips.ffrecip.s  fs5,fs0
800002b0:       e1c7868b                mips.ffsqrt.s  fa3,fa5
800002b4:       e1d7878b                mips.ffrsqrt.s  fa5,fa5
800002c0:       e066868b                mips.fatanhz.s  fa3,fa3
800002c4:       e067878b                mips.fatanhz.s  fa5,fa5
80000302:       e014878b                mips.fcoshz.s  fa5,fs1
80000340:       e024870b                mips.ftanhz.s  fa4,fs1
8000034c:       e027878b                mips.ftanhz.s  fa5,fa5
80000350:       e1b7878b                mips.ffrecip.s  fa5,fa5
80000386:       e06a868b                mips.fatanhz.s  fa3,fs5
8000038e:       e064078b                mips.fatanhz.s  fa5,fs0
800003da:       e034848b                mips.fversinhz.s  fs1,fs1
8000041c:       e184078b                mips.fflog2.s  fa5,fs0
80000462:       e194078b                mips.ffexp2.s  fa5,fs0
80000528:       e1d4040b                mips.ffrsqrt.s  fs0,fs0
800005ca:       e1c4078b                mips.ffsqrt.s  fa5,fs0
800005e8:       e1d4040b                mips.ffrsqrt.s  fs0,fs0
8000063a:       e1c4070b                mips.ffsqrt.s  fa4,fs0
80000680:       e014878b                mips.fcoshz.s  fa5,fs1
800006d2:       e1d4040b                mips.ffrsqrt.s  fs0,fs0
```
