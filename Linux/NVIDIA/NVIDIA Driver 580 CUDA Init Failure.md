# NVIDIA Driver 580.126.18 - CUDA Runtime Initialization Failure

## System Information

### Hardware
- **Laptop:** Lenovo Legion Slim 5 16AHP9
- **CPU:** AMD Ryzen 7 8845HS w/ Radeon 780M Graphics (16 cores)
- **GPU (Primary):** AMD Radeon 780M (integrated)
- **GPU (Discrete):** NVIDIA GeForce RTX 4070 Laptop GPU
  - Device ID: 10de:2860
  - Architecture: Ada Lovelace
  - Compute Capability: 8.9
  - PCI Bus: 01:00.0
  - IOMMU Group: 15
  - VRAM: 8 GB

### Software
- **OS:** openSUSE Tumbleweed 20260304
- **Kernel:** 6.19.5-1-default #1 SMP PREEMPT_DYNAMIC (ababe9c)
- **NVIDIA Driver:** 580.126.18_k6.19.2_1-45.5
- **CUDA Version Supported by Driver:** 13.0
- **CUDA Toolkit Installed:** 13.0 (/usr/local/cuda-13.0)

### Driver Package
```
nvidia-driver-G06-kmp-default-580.126.18_k6.19.2_1-45.5.x86_64
```

## Problem Description

Driver 580.126.18 fails to initialize CUDA runtime on kernel 6.19.5. The `cudaGetDeviceCount()` function returns `CUDA_ERROR_UNKNOWN` (error 999), and `cuInit(0)` from the CUDA Driver API also returns error 999, preventing any CUDA application from detecting or using the GPU.

## Steps to Reproduce

1. Install NVIDIA driver 580.126.18 on openSUSE Tumbleweed with kernel 6.19.5
2. Install CUDA Toolkit 13.0
3. Compile and run the following test:

```c
#include <cuda_runtime.h>
#include <stdio.h>

int main() {
    int deviceCount = 0;
    cudaError_t error = cudaGetDeviceCount(&deviceCount);
    
    if (error != cudaSuccess) {
        printf("CUDA Error: %s\n", cudaGetErrorString(error));
        return 1;
    }
    
    printf("CUDA Devices: %d\n", deviceCount);
    
    for (int i = 0; i < deviceCount; i++) {
        struct cudaDeviceProp prop;
        cudaGetDeviceProperties(&prop, i);
        printf("Device %d: %s\n", i, prop.name);
    }
    
    return 0;
}
```

Compile:
```bash
export PATH=/usr/local/cuda-13.0/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-13.0/lib64:$LD_LIBRARY_PATH
nvcc test.c -o test
```

Run:
```bash
./test
```

## Expected Result
```
CUDA Devices: 1
Device 0: NVIDIA GeForce RTX 4070 Laptop GPU
```

## Actual Result
```
CUDA Error: unknown error
```

## Additional Testing

### CUDA Driver API Test
```c
#include <cuda.h>
#include <stdio.h>

int main() {
    CUresult res = cuInit(0);
    printf("cuInit: %d\n", res);
    
    if (res != CUDA_SUCCESS) {
        const char *errStr;
        cuGetErrorString(res, &errStr);
        printf("Error: %s\n", errStr);
        return 1;
    }
    
    int count;
    res = cuDeviceGetCount(&count);
    printf("Device count: %d (result: %d)\n", count, res);
    
    return 0;
}
```

Result:
```
cuInit: 999
Error: unknown error
```

### Detailed CUDA Runtime Test
```c
#include <cuda_runtime.h>
#include <stdio.h>

int main() {
    printf("Testing CUDA initialization...\n");
    
    int runtimeVersion;
    cudaError_t err = cudaRuntimeGetVersion(&runtimeVersion);
    printf("Runtime version check: %s (version: %d)\n", 
           cudaGetErrorString(err), runtimeVersion);
    
    int driverVersion;
    err = cudaDriverGetVersion(&driverVersion);
    printf("Driver version check: %s (version: %d)\n", 
           cudaGetErrorString(err), driverVersion);
    
    int deviceCount = 0;
    err = cudaGetDeviceCount(&deviceCount);
    printf("Device count check: %s (count: %d)\n", 
           cudaGetErrorString(err), deviceCount);
    
    return (err != cudaSuccess);
}
```

Result:
```
Testing CUDA initialization...
Runtime version check: no error (version: 13000)
Driver version check: no error (version: 13000)
Device count check: unknown error (count: 0)
```

**Analysis:** Runtime CUDA loads successfully, but device enumeration fails.

## What Works

- `nvidia-smi` works correctly and shows the GPU:
```
Driver Version: 580.126.18
CUDA Version: 13.0
GPU 0: NVIDIA GeForce RTX 4070 Laptop GPU
```

- Driver modules load without errors:
```bash
$ lsmod | grep nvidia
nvidia_uvm            2134016  0
nvidia_drm             98304  8
nvidia_modeset       1622016  1 nvidia_drm
nvidia              73089024  138 nvidia_uvm,nvidia_modeset
```

- OpenGL works correctly with NVIDIA
- GPU is recognized by the driver:
```bash
$ cat /proc/driver/nvidia/gpus/*/information
Model:           NVIDIA GeForce RTX 4070 Laptop GPU
IRQ:             104
```

- User has correct permissions (member of `video` group)
- Persistence mode is enabled

## Evidence That Driver 570 Works

Attempted downgrade showed that driver 570 series works correctly:

1. Installed driver 570.172.08_k6.15.6_1 package (compiled for kernel 6.15.6)
2. Without rebooting, tested CUDA with the driver 570 modules still loaded in memory
3. **Result:** CUDA worked perfectly:
   - Test program detected GPU successfully
   - Ollama server detected GPU and used it (854 MiB VRAM usage confirmed with nvidia-smi)
   - All CUDA applications worked

4. After reboot, system loaded driver 580 again because kernel 6.19.5 only has modules for driver 580

This confirms the issue is specifically with driver 580.126.18, not with the hardware, kernel, or CUDA toolkit.

## Impact

This bug prevents **all CUDA applications** from using the GPU:
- Ollama (LLM inference)
- LM Studio  
- PyTorch
- TensorFlow
- Any CUDA-based scientific computing

The GPU is completely unusable for compute workloads, forcing CPU-only operation.

## Additional Notes

- This is a **hybrid GPU system** (AMD integrated + NVIDIA discrete)
- The issue persists even in containers (distrobox) since they share the host's kernel driver
- No kernel errors or warnings in dmesg related to NVIDIA
- The bug appears to be in the driver's CUDA initialization code, not in the kernel module loading
- Driver 570.x series works correctly with the same hardware and CUDA toolkit

## Requested Fix

Either:
1. Fix driver 580.126.18 to properly initialize CUDA runtime
2. Provide driver 570.x compiled for kernel 6.19.x as a temporary workaround

## nvidia-bug-report.log

Will be attached: nvidia-bug-report.log.gz (generated with `nvidia-bug-report.sh`)
