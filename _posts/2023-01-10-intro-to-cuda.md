---
layout: post
title:  "Intro to CUDA"
category: software
---

Getting my feet wet with Nvidia's CUDA ([Compute Unified Device Architecture](https://en.wikipedia.org/wiki/CUDA)) library.

CUDA installation (for me: Windows 11, x86_64):
<https://developer.nvidia.com/cuda-downloads>

I found this developer blog to get me going:
<https://developer.nvidia.com/blog/even-easier-introduction-cuda/>

In less than 1 hour I was able to install CUDA and have a sample program compiled and built with Visual Studio 2022. Very cool!

The blog above does 4 iterations on a simple program that creates 2 arrays of 1 million elements, the first array all 1s, the second all 2s, sums them into the second one. I've written them all out separately here, along with my profiling results.

## 1. Sample program, no GPU

No GPU in this one as a baseline. Visual Studio 2022, C++ Console App.

```
#include <iostream>
#include <math.h>

// function to add the elements of two arrays
void add(int n, float* x, float* y)
{
    for (int i = 0; i < n; i++)
        y[i] = x[i] + y[i];
}

int main(void)
{
    int N = 1 << 20; // 1M elements

    float* x = new float[N];
    float* y = new float[N];

    // initialize x and y arrays on the host
    for (int i = 0; i < N; i++) {
        x[i] = 1.0f;
        y[i] = 2.0f;
    }

    // Run kernel on 1M elements on the CPU
    add(N, x, y);

    // Check for errors (all values should be 3.0f)
    float maxError = 0.0f;
    for (int i = 0; i < N; i++)
        maxError = fmax(maxError, fabs(y[i] - 3.0f));
    std::cout << "Max error: " << maxError << std::endl;

    // Free memory
    delete[] x;
    delete[] y;

    return 0;
}
```

## 2. Sample program, 1 CUDA Thread

```
#include "cuda_runtime.h"
#include <iostream>
#include <math.h>
__global__
void add(int n, float* x, float* y)
{
    for (int i = 0; i < n; i++)
        y[i] = x[i] + y[i];
}

int main(void)
{
    int N = 1 << 20;
    float* x, * y;

    // Allocate Unified Memory – accessible from CPU or GPU
    cudaMallocManaged(&x, N * sizeof(float));
    cudaMallocManaged(&y, N * sizeof(float));

    // initialize x and y arrays on the host
    for (int i = 0; i < N; i++) {
        x[i] = 1.0f;
        y[i] = 2.0f;
    }

    // Run kernel on 1M elements on the GPU
    add <<<1, 1 >>> (N, x, y);

    // Wait for GPU to finish before accessing on host
    cudaDeviceSynchronize();

    // Check for errors (all values should be 3.0f)
    float maxError = 0.0f;
    for (int i = 0; i < N; i++)
        maxError = fmax(maxError, fabs(y[i] - 3.0f));
    std::cout << "Max error: " << maxError << std::endl;

    // Free memory
    cudaFree(x);
    cudaFree(y);

    return 0;
}
```

## 3. Sample program, 1 CUDA Block

```
#include "cuda_runtime.h"
#include "device_launch_parameters.h"

#include <iostream>
#include <math.h>

// Kernel function to add the elements of two arrays
__global__
void add(int n, float* x, float* y)
{
    const int index = threadIdx.x;
    const int stride = blockDim.x;
    for (int i = index; i < n; i += stride) {
        y[i] = x[i] + y[i];
    }
}

int main(void)
{
    int N = 1 << 20;
    float* x, * y;

    // Allocate Unified Memory – accessible from CPU or GPU
    cudaMallocManaged(&x, N * sizeof(float));
    cudaMallocManaged(&y, N * sizeof(float));

    // initialize x and y arrays on the host
    for (int i = 0; i < N; i++) {
        x[i] = 1.0f;
        y[i] = 2.0f;
    }

    // Run kernel on 1M elements on the GPU
    add<<<1, 256>>>(N, x, y);

    // Wait for GPU to finish before accessing on host
    cudaDeviceSynchronize();

    // Check for errors (all values should be 3.0f)
    float maxError = 0.0f;
    for (int i = 0; i < N; i++) {
        maxError = fmax(maxError, fabs(y[i] - 3.0f));
    }
    std::cout << "Max error: " << maxError << std::endl;

    // Free memory
    cudaFree(x);
    cudaFree(y);

    return 0;
}
```

## 4. Sample program, Many CUDA Blocks

```
#include "cuda_runtime.h"
#include "device_launch_parameters.h"

#include <iostream>
#include <math.h>

// Kernel function to add the elements of two arrays
__global__
void add(int n, float* x, float* y)
{
    const int index = blockIdx.x * blockDim.x + threadIdx.x;
    const int stride = blockDim.x * gridDim.x;
    for (int i = index; i < n; i += stride) {
        y[i] = x[i] + y[i];
    }
}

int main(void)
{
    int N = 1 << 20;
    float* x, * y;

    // Allocate Unified Memory – accessible from CPU or GPU
    cudaMallocManaged(&x, N * sizeof(float));
    cudaMallocManaged(&y, N * sizeof(float));

    // initialize x and y arrays on the host
    for (int i = 0; i < N; i++) {
        x[i] = 1.0f;
        y[i] = 2.0f;
    }

    const int blockSize = 256;
    const int numBlocks = (N + blockSize - 1) / blockSize;
    // Run kernel on 1M elements on the GPU
    add <<<numBlocks, blockSize >>> (N, x, y);

    // Wait for GPU to finish before accessing on host
    cudaDeviceSynchronize();

    // Check for errors (all values should be 3.0f)
    float maxError = 0.0f;
    for (int i = 0; i < N; i++) {
        maxError = fmax(maxError, fabs(y[i] - 3.0f));
    }
    std::cout << "Max error: " << maxError << std::endl;

    // Free memory
    cudaFree(x);
    cudaFree(y);

    return 0;
}
```

## Profiling results

| Program                         | Total execution time |  add kernel execution time on GPU |
|:-------------------------------:|:--------------------:|:---------------------------------:|
| Console App (No GPU)            | 26 ms                | N/A                               |
| CUDA Runtime, 1 CUDA Thread     | 562 ms               | 59.8 ms                           |
| CUDA Runtime, 1 CUDA Block      | 511 ms               | 1.4 ms                            |
| CUDA Runtime, Many CUDA Blocks  | 497 ms               | .041 ms                           |

Notes:
* Visual Studio 2022, 4.8.04161. Build: x64 Release with default settings.
* CUDA 11.8.0
* NVIDIA GeForce RTX 2060
* Total execution time: Ran this command ~3 times and took the lowest execution time (milliseconds)

```
    > Measure-Command {.\CudaRuntime1.exe}
```

* add kernel execution time on GPU: Note: [nvprof was deprecated in 2019 and is no longer available](https://developer.nvidia.com/blog/migrating-nvidia-nsight-tools-nvvp-nvprof/). There still is an nvprof.exe executable but it doesn't do anything which is pretty confusing! (Presumably to not break any existing scripts?) Nsight Systems is the intended replacement for it. Have to run in powershell as an administrator ...

```
    & 'C:\Program Files\NVIDIA Corporation\Nsight Systems 2022.4.2\target-windows-x64\nsys.exe' profile .\CudaRuntime1.exe
```

... and then view the output reportN.nsys-rep in NVIDIA Nsight Systems. Timeline View: Processes -> CudaRuntime1.exe -> CUDA HW -> 100.0% Kernels -> 100.0% add -> 100.0% add(int, float*, float*), hovered the mouse over that blob, and took the end - begin delta. (Alternatively, Processes ->CudaRuntime1.exe -> Threads -> CudaRuntime1.exe -> CUDA API -> Right click -> Show in Events View.)

## Takeaways

Using the GPU took 20 times longer (500 milliseconds) than just using the CPU (26 milliseconds)! Disappointing! Although once the GPU set in to the task of actually doing the math it was very fast (.041 milliseconds). What the heck?

Using NVIDIA Nsight Systems timeline it spends ~300-400 ms in the first cudaMallocManaged() call. Though after doing some digging it looks like the first cuda call (whatever it is) will take a little while. In fact, this simple program takes 200-300 ms, spending all of its time in cudaFree ([suggested here for initializing a context](https://stackoverflow.com/questions/12126252/what-does-nvidia-gpu-do-with-device-memory-0x0/12311042#12311042)):

```
#include "cuda_runtime.h"
#include <iostream>
int main(void) {
	cudaFree(0);
	std::cout << "Did nothing, Ha!\n";
	return 0;
}
```

Then I ran across [this suggestion that the slowness could be caused by needing to run the CUDA JIT](https://github.com/microsoft/onnxruntime/issues/10746), which linked to [an explanation on understanding fat binaries and JIT caching](https://developer.nvidia.com/blog/cuda-pro-tip-understand-fat-binaries-jit-caching/). Then I spent some time in [Visual Studio CUDA project properties](https://docs.nvidia.com/nsight-visual-studio-edition/3.2/Content/CUDA_Properties_Config.htm), and added *-gencode=arch=compute_75, code=\"sm_75, compute_75\"* to match my GeForce RTX 2060, which is a [Turing Architecture](https://en.wikipedia.org/wiki/Turing_(microarchitecture)), and confirmed that the elf version is included in my executable with cuobjdump:

```
> cuobjdump .\CudaRuntime1.exe

Fatbin elf code:
================
arch = sm_75
code version = [1,7]
host = windows
compile_size = 64bit

Fatbin ptx code:
================
arch = sm_75
code version = [7,7]
host = windows
compile_size = 64bit
compressed
```

... AND... it didn't seem to improve execution speed at all. Bummer. For all I know there might not be a way around that first ~300 ms of startup time for a CUDA runtime app.

That means for CUDA to be useful you need a problem that uses enough batching that it can amortize the startup cost. If you only want to add 2 arrays of 1 million floats, you are better off just using the CPU.
