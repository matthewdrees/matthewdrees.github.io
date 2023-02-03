---
layout: post
title:  "Performance Ninja Class"
category: software
---

I heard about Denis Bakhvalov's [Performance Ninja Class](https://github.com/dendibakh/perf-ninja) on a recent episode of [cppcast](https://cppcast.com/performance-tuning/).

## Warmup lab

It took a little bit of work to get [perf running on WSL2](https://stackoverflow.com/questions/60237123/is-there-any-method-to-run-perf-under-wsl). Note: apt install linux-tools-generic. Don't clone/install!

perf stat --topdown [doesn't seem to be supported on WSL2 yet](https://github.com/microsoft/WSL/issues/8480)

4 pomodoros.

## vectorization_1

The first lab is in the "core bound" section and focuses on using vectorization instructions for parallel data processing. Not the easiest lab assignment! I spent a good chunk of time walking through the [sequence alignment](https://en.wikipedia.org/wiki/Sequence_alignment) algorithm figuring out what it did. The performance fix was to transpose the matrix and adjust the algorithm accordingly, which enabled vectorization assembly instructions. I got a 3x performance increase.

16 pomodoros.

## vectorization_2

A follow up vectorization problem. This should have been the first lab! It was *much* easier than vectorization_1. Re-write a checksum algorithm to allow for auto vectorization. I saw a 9x performance increase.

3 pomodoros.
