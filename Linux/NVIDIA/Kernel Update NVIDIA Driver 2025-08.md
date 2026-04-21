# Kernel Update and NVIDIA Driver Issue - 2025-08-20

## Problem
After updating the kernel to 6.16.1, encountered NVIDIA persistence daemon failure:

```
Job for nvidia-persistenced.service failed because the control process exited with error code.
See "systemctl status nvidia-persistenced.service" and "journalctl -xeu nvidia-persistenced.service" for details.
error: lua script failed: [string "%transfiletriggerin(systemd-257.7-6.1.x86_64)..."]:5: exit code
Error from %transfiletriggerin(systemd-257.7-6.1.x86_64)
```

## Diagnosis

### Service Status
```bash
systemctl status nvidia-persistenced.service
```
Output:
```
× nvidia-persistenced.service - NVIDIA Persistence Daemon
     Loaded: loaded (/usr/lib/systemd/system/nvidia-persistenced.service; enabled; preset: enabled)
     Active: failed (Result: exit-code) since Wed 2025-08-20 14:34:57 -03; 1min 17s ago
   Duration: 9min 31.602s
 Invocation: 082cc9caf3bb497c8ec6278a5eb17b36
    Process: 33070 ExecStart=/usr/bin/nvidia-persistenced --verbose (code=exited, status=1/FAILURE)
```

### NVIDIA Driver Status
```bash
nvidia-smi
```
Output:
```
Failed to initialize NVML: Driver/library version mismatch
NVML library version: 580.76
```

### Root Cause
- Updated kernel to 6.16.1 but still running kernel 6.13.8 (no reboot yet)
- NVIDIA driver compiled for kernel 6.15.8: `nvidia-driver-G06-kmp-default-580.76.05_k6.15.8_1-39.1.x86_64`
- Driver/library version mismatch due to kernel version incompatibility

### Available Kernels
```bash
ls /boot/vmlinuz-* | sort -V
```
Output:
```
/boot/vmlinuz-6.13.8-1-default
/boot/vmlinuz-6.14.0-1-default
/boot/vmlinuz-6.16.1-1-default
```

### Current Running Kernel
```bash
uname -r
```
Output: `6.13.8-1-default`

## Solution Steps

1. **Reboot to load kernel 6.16.1:**
   ```bash
   sudo reboot
   ```

2. **After reboot, verify kernel version:**
   ```bash
   uname -r  # Should show 6.16.1-1-default
   ```

3. **Reinstall NVIDIA drivers for new kernel:**
   ```bash
   # Remove old kernel module package
   sudo zypper remove nvidia-driver-G06-kmp-default
   
   # Reinstall for current kernel
   sudo zypper install nvidia-driver-G06-kmp-default
   
   # Start services
   sudo systemctl start nvidia-persistenced
   ```

4. **Verify fix:**
   ```bash
   nvidia-smi
   systemctl status nvidia-persistenced
   ```

## Key Lesson
Always reboot after kernel updates before reinstalling kernel-dependent drivers like NVIDIA. The driver needs to be compiled against the actual running kernel version.

## NVIDIA Packages Installed
```
nvidia-driver-G06-kmp-default-580.76.05_k6.15.8_1-39.1.x86_64
nvidia-persistenced-580.76.05-2.1.x86_64
nvidia-settings-580.76.05-41.1.x86_64
nvidia-compute-G06-580.76.05-39.1.x86_64
nvidia-gl-G06-580.76.05-39.1.x86_64
[... and other NVIDIA packages ...]
```
