# openSUSE Tumbleweed NVIDIA Boot Issue

## Problem
After running `sudo zypper dup` to update the system, the computer rebooted to a TTY console instead of the graphical interface (SDDM). This happened after a kernel update from 6.13.7 to 6.13.8.

## Cause
The NVIDIA kernel module failed to load with the new kernel. System logs showed:
```
modprobe[1676]: ERROR: could not find module by name='nvidia'
modprobe[1676]: ERROR: could not insert 'nvidia': Unknown symbol in module, or unknown parameter
```

This is a common issue when the kernel is updated but the NVIDIA drivers aren't properly rebuilt for the new kernel version.

## Solution

1. **Boot into a working snapshot** using the GRUB menu at boot time.

2. **Make the rollback permanent**:
   ```bash
   sudo snapper rollback
   ```

3. **Reinstall the NVIDIA drivers** for the current kernel:
   ```bash
   sudo zypper in --force nvidia-compute-G06 nvidia-gfxG06-kmp-default nvidia-glG06
   ```

4. **Update the system more carefully**:
   ```bash
   sudo zypper dup --no-allow-vendor-change
   ```

## Prevention for Future Updates

When updating Tumbleweed in the future, consider this safer approach:

1. Update the kernel separately:
   ```bash
   sudo zypper up kernel-*
   ```

2. Reinstall NVIDIA drivers:
   ```bash
   sudo zypper in --force nvidia-compute-G06 nvidia-gfxG06-kmp-default nvidia-glG06
   ```

3. Update the rest of the system:
   ```bash
   sudo zypper dup --no-allow-vendor-change
   ```

This approach helps ensure that the NVIDIA drivers are properly rebuilt for the new kernel before you reboot.

## System Information
- openSUSE Tumbleweed
- Kernel: 6.13.8-1-default
- Graphics: NVIDIA GeForce RTX 4070 Max-Q / Mobile + AMD Radeon (hybrid graphics)
