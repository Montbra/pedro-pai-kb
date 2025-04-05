# Locking Kernel Version in openSUSE Tumbleweed

## Overview
This guide explains how to lock your current kernel version in openSUSE Tumbleweed to prevent automatic updates during system updates.

## Why Lock Kernel Versions?
- Maintain stability with a known working kernel
- Avoid regressions from newer kernels
- Ensure compatibility with specific hardware or software
- Prevent issues with proprietary drivers

## Prerequisites
- openSUSE Tumbleweed installation
- Administrative (sudo) privileges

## Steps to Lock Current Kernel

### 1. Identify Your Current Kernel Version
```bash
uname -r
```
This will display your current kernel version (e.g., `6.13.8-1-default`).

### 2. Lock Kernel Packages
Run the following command to prevent the kernel packages from being updated:

```bash
sudo zypper addlock kernel-default kernel-default-base kernel-default-devel
```

### 3. Verify Locks Are Applied
To confirm that the locks have been successfully applied:

```bash
sudo zypper locks
```

You should see the kernel packages listed in the output.

## Managing Kernel Locks

### Removing Kernel Locks
If you want to update your kernel in the future, remove the locks with:

```bash
sudo zypper removelock kernel-default kernel-default-base kernel-default-devel
```

### Temporarily Installing a Specific Kernel
If you want to try a specific kernel version without removing your locks:

```bash
sudo zypper install --from <repository> kernel-default-<version>
```

## Important Considerations

### Security Updates
Locking your kernel means you won't receive kernel security updates. Consider periodically checking for important security fixes and temporarily removing the lock to apply them if necessary.

### Hardware Compatibility
If you add new hardware to your system, you might need a newer kernel for proper support. In such cases, consider temporarily removing the lock.

### System Stability
While locking the kernel can prevent regressions, it also prevents you from getting bug fixes. Balance stability needs with security considerations.

## Troubleshooting

### If You Can't Boot After Kernel Update
If you've updated your kernel and can't boot:
1. At the GRUB menu, select "Advanced options"
2. Choose a previous kernel version
3. After booting, lock the working kernel version using the steps above

### Checking Available Kernels
To see all installed kernels:

```bash
rpm -qa | grep kernel-default
```

## Conclusion
Locking your kernel version can help maintain system stability, but remember to periodically review whether you should update for security reasons.
