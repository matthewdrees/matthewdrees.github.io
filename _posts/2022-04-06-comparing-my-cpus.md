---
layout: post
title:  "Comparing my CPUs"
category: hardware
---

Some comparisons of the CPUs that I own, and a couple that I might purchase if buying new today.

|         | Ryzen 7 2700 [^1] | A12 Bionic [^2] | Core i5-2500 [^3] | Raspberry Pi 2 [^4] | Ryzen 5 5600X [^5] | Apple M1 [^6] |
|:-------:|:-----------------:|:---------------:|:-----------------:|:-------------------:|:------------------:|:-------------:|
| Year    | 2018              | 2018            | 2011              | 2011                | 2020               | 2020          |
| Family  | Zen 1+            | ARMv8.3-A       | Sandy Bridge      | ARMv7-A             | Zen 3              | ARMv8.4-A     |
| Manufacturing technology | 12 nm | 7 nm       | 32 nm             | 40 nm               | 7 nm               | 5 nm          |
| # transistors (millions) [^7] | 4800 | 6900   | 1160              | 26*                 | 4150               | 16000         |
| Clock freq | 3.2 GHz        | 2.49 GHz        | 3.3 GHz           | 900 MHz             | 3.7 - 4.6 GHz      | 3.2 GHz       |
| # cores | 8                 | 6               | 4                 | 4                   | 6                  | 8             |
| L1 cache inst/data (KB) | 64 /32 | 128 / 128  | 32 / 32           | 64 / 64             | 32 / 32            | 192 / 128     |
| L2 cache | 512 (KB)         | 8 MB            | 256 KB            | 256 KB              | 512 KB             | 12 MB         |
| L3 cache | 8 MB             | N/A             | 6 MB              | N/A                 | 32 MB              | N/A           |
| TDP (watts) | 65            | 6               | 95                | 5                   | 65                 | 15            |
| CPU Mark [^8] | 15650       | 22109           | 4062              | 244                 | 22103  | 14752     |               |
| Geekbench 5 (single core) [^9] | 944          | 1107              | 700                 | 48     | 1615      | 1730          |
| Geekbench 5 (multi core) [^9]  | 6252         | 2509              | 2214                | 89     | 8153      | 7550          |
| Time to build Linux (seconds) [^10] | 118 | ? | 314               | 2941*               | 110    | ?                         |
| MOV cycles/inst [^11] [^12] | 0.3       | ?   | 0.5               | 0.5                 | 0.33   | ?                         |
| ADD cycles/inst [^11] [^12] | 0.5       | ?   | 1                 | 1                   | 0.33   | ?                         |
| IMUL cycles/inst [^11] [^12] | 1        | ?   | 1                 | 1                   | 1      | ?                         |
| IDIV cycles/inst [^11] [^12] | 14 - 30  | ?   | 11 - 18           | 4 - 20              | 7 - 12 | ?                         |

* Transistor count for "ARM Cortex-A9" [^7]
* Time to build Linux for "Intel Core i7-2700K"
* For instruction cycles showing register, memory (r, m) operation values and reciprocal throughput.

## Notes

* Transistor sizes dropped from 40 nm to 5 nm in just 10 years!
* The number of transistors quadrupled from my Core i5-2500 in 2011 to my Ryzen 7 in 2018, but then *dropped* a bit with the latest Ryzen 5.
* The number of transistors on the new Apple M1 is insane at 16 billion.
* The recent Apple M1 does similar benchmarks to comparable x86_64 chips but with 1/3 the power consumption!
* It's interesting to see the tech specs but CPUs are so much more complicated than clock frequency, cache size, etc. It's important to have the benchmark scores, especially for applications that are interesting to you.
* I can't believe that the A12 Bionic in my phone out-performs the Ryzen 7 desktop I bought in the same year in CPU Mark!
* Regarding Linux build time:
  + There was a big jump from my i5 to my current Ryzen 7, 314 seconds to 118 (easy enough, twice as many cores!), though with the new Ryzen 5 5600X only modest improvement to 110 seconds.
  + I'm surprised that OpenBenchmarking hasn't measured this on M1 yet, if nothing else as a fun data point.
  + Good for the Raspberry Pi for eventually finishing! (49 hours later but still!)
* I threw in some assembly instruction cycles/inst for fun, and was amazed to find that the vendors don't provide cycles/instruction values. Folks like [Agner Fog](https://www.agner.org/) reverse-engineer them. The Ryzen IDIV instruction became nearly twice as efficient between 2018 and 2020 (Zen 1+ vs Zen 3).
* My son is using my old computer and is recommending that I upgrade so he can get mine, but I'm not seeing the value yet (for me, I see the value for him :) ).

## References

[^1]: [Zen 1+](https://en.wikipedia.org/wiki/Zen%2B)
[^2]: [Apple A12](https://en.wikipedia.org/wiki/Apple_A12)
[^3]: [Sandy Bridge](https://en.wikipedia.org/wiki/Sandy_Bridge)
[^4]: [Raspberry Pi 2 Model B](https://www.raspberrypi.com/products/raspberry-pi-2-model-b/)
[^5]: [Zen 3](https://en.wikipedia.org/wiki/Zen_3)
[^6]: [Apple M1](https://en.wikipedia.org/wiki/Apple_M1)
[^7]: [Transistor count - wikipedia](https://en.wikipedia.org/wiki/Transistor_count)
[^8]: [CPU Benchmark](https://www.cpubenchmark.net/)
[^9]: [Geekbench 5](https://www.geekbench.com/)
[^10]: [OpenBenchmarking - Timed Linux Kernel Compilation](https://openbenchmarking.org/test/pts/build-linux-kernel)
[^11]: [Agner Fog Instruction tables](https://www.agner.org/optimize/instruction_tables.pdf)
[^12]: [Hardwarebug Cortex-A7 instruction cycle timings](https://hardwarebug.org/2014/05/15/cortex-a7-instruction-cycle-timings/)
