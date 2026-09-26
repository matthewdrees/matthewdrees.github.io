---
layout: post
title:  "Benchmarking Python with pyperf"
category: software
---

Some notes on benchmarking Python 3 code with [pyperf](https://github.com/psf/pyperf).

FYI pyperf is used by [pyperformance](https://github.com/python/pyperformance) for benchmarking cpython. Results are in the [faster-cpython/benchmarking-public](https://github.com/faster-cpython/benchmarking-public) repo.

Benchmark time is measured with [time.perf_counter()](https://docs.python.org/3/library/time.html#time.perf_counter), which measures wall clock (fractional) seconds since an undefined point in time. (Not [time.process_time()](https://docs.python.org/3/library/time.html#time.process_time), which measures only the time spent while the process is actually running.) This calculation is done in the [pyperf/pyperf/_process_time.py bench_process()](https://github.com/psf/pyperf/blob/main/pyperf/_process_time.py#:~:text=bench_process) function.

[Comparison of pyperf to timeit](https://pyperf.readthedocs.io/en/latest/cli.html#timeit-cmd): timeit is quick and dirty: only runs the benchmark 3 times, using a single process, gives the minimum execution time, and disables the garbage collector. pyperf does a lot more, see the [timeit versus pyperf timeit](https://pyperf.readthedocs.io/en/latest/cli.html#timeit-cmd) section.

## Options for running

Options with my explanations.

[Options for running](https://pyperf.readthedocs.io/en/latest/runner.html#) (defaults are cpython. Pypy with JIT is more difficult.):
* -p number of processes. Default = 20.
* -n values. Default = 3. Number of measured "runs" to do on each process, with each run performing enough loops to reach --min-time. From the docs: should be enough values to get a uniform distribution of run times.
* -l loops. Default = calibrated to --min-time. Number of loops to do on each run. If specified this seems to override --min-time.
* -w warmups. Default = 1. Number of warmup runs to throw away.
* --min-time in seconds. Default = 0.1. Sets the number of loops for each run. Seemingly ignored if -l is set.

Why not just have a single run (-n = 1) and increase the --min-time to accommodate? The best I can tell: it allows for more timing data points to detect unstable benchmark conditions.

## Tuning and unstable conditions

[Tune the system for benchmarks](https://pyperf.readthedocs.io/en/latest/system.html#system) lists all the operations pyperf does to reduce system jitter.

Unstable benchmark conditions. Will get a warning under any of the following conditions ([Code in pyperf/pyperf/_cli.py](https://github.com/psf/pyperf/blob/main/pyperf/_cli.py)):
* The standard deviation is >= 10% of the mean.
* The shortest run time is < 1 millisecond.
* The min/max values are >= 50% of the mean

## How to structure benchmarks

You can run simple python snippets directly from the pyperf module:

    $ python3 -m pyperf '" abc ".strip()'
    .....................
    Mean +- std dev: 59.9 ns +- 3.2 ns

From the command line you can enter in the -p, n, etc values from above to speed up the tests a bit, or slow them down for greater accuracy:

    $ python3 -m pyperf timeit -p10 --min-time=0.05 '" abc ".strip()'
    ...........
    Mean +- std dev: 59.1 ns +- 2.2 ns

The pyperf Runner API has a useful parse_args command, so you can call your module using the pyperf options, so you have flexibility when running performance tests. Could structure this way ...

    # In module m.py
    from typing import List

    def add_nums(nums: List[int]):
        """A simple function to test performance."""
        return sum(nums)

    def get_random_list_with_length(min_i: int, max_i: int, n: int):
        import random

        l = []
        random.seed(42)
        for n in range(n):
            l.append(random.randint(min_i, max_i))
        return l

    def perf_test():
        import pyperf

        runner = pyperf.Runner()
        runner.parse_args()
        for exp in (0, 1, 2, 4, 8, 16):
            n = 2**exp
            l = get_random_list_with_length(-100, 100, n)
            runner.bench_func(f"add_nums(len={n})", add_nums, l)

... and run it like this ...

    $ python3 m.py -p 10 --min-time=0.05
    ...........
    add_nums(len=1): Mean +- std dev: 122 ns +- 8 ns
    ...........
    add_nums(len=2): Mean +- std dev: 137 ns +- 8 ns
    ...........
    add_nums(len=4): Mean +- std dev: 144 ns +- 8 ns
    ...........
    add_nums(len=16): Mean +- std dev: 187 ns +- 8 ns
    ...........
    add_nums(len=256): Mean +- std dev: 1.52 us +- 0.06 us
    ...........
    add_nums(len=65536): Mean +- std dev: 604 us +- 19 us

Now can export results to a json file with -o parameter and compare performance to previous versions.

I thought it would be clever to run the performance tests with a command line flag like --pyperf, but couldn't get Runner.parse_args() to play friendly with the returned arguments from argparse.parse_known_args(). Likely an error on my part but ran out of time while investigating. Oh well, can just put the performance tests in their own module, which is what pyperformance does.
