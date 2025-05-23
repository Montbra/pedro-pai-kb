# NVIDIA Driver and Kernel Upgrade Notes

## Problem
After upgrading to kernel 6.14, the GUI wouldn't load, likely due to incompatibility with the existing NVIDIA drivers.

## Solution
1. Rolled back to a snapshot with working kernel (6.13.8-1-default)
2. Upgraded all NVIDIA components to version 570.133.07
3. Switched from X11 to Wayland display server
4. Continued using kernel 6.13.8-1-default as it works properly with the new drivers

## Steps Taken

### 1. Checked Current Kernel Version
```bash
uname -r
# Output: 6.13.8-1-default
```

### 2. Checked Available Kernel Versions
```bash
rpm -qa | grep kernel | sort
```

### 3. Upgraded NVIDIA Drivers
```bash
sudo zypper install nvidia-compute-G06-570.133.07 nvidia-compute-G06-32bit-570.133.07 nvidia-compute-utils-G06-570.133.07 nvidia-gl-G06-570.133.07 nvidia-gl-G06-32bit-570.133.07 nvidia-video-G06-570.133.07 nvidia-video-G06-32bit-570.133.07 nvidia-driver-G06-kmp-default-570.133.07 nvidia-settings-570.133.07 nvidia-modprobe-570.133.07 nvidia-persistenced-570.133.07 nvidia-libXNVCtrl-570.133.07
```

### 4. Verified NVIDIA Driver Installation
```bash
rpm -qa | grep nvidia | grep 570
# All components now at version 570.133.07
```

### 5. Switched to Wayland
Changed the display server from X11 to Wayland in the login screen settings.

## Issues Encountered

### nvidia-persistenced Service Failure
The nvidia-persistenced service failed to start with the error:
```
Failed to query NVIDIA devices. Please ensure that the NVIDIA device files (/dev/nvidia*) exist, and that user 455 has read and write permissions for those files.
```

Attempted fixes:
- Changed permissions on NVIDIA device files
- Created and set permissions on /var/run/nvidia-persistenced directory
- Restarted the service

Note: This service is not critical for basic GPU functionality - it's mainly used for persistence mode which helps with faster application startup times.

### X11 Compatibility Issues
After upgrading to the NVIDIA 570.133.07 drivers, the system would not function properly with X11. The GUI would either not load correctly or experience significant issues.

**Solution:** Switching from X11 to Wayland display server resolved the issues. The NVIDIA 570 series drivers appear to work better with Wayland than with X11 on this system configuration with kernel 6.13.8-1-default.

## Next Steps
1. Continue monitoring system stability with the current configuration (kernel 6.13.8-1-default + NVIDIA 570.133.07 drivers + Wayland)
2. Consider testing kernel 6.14 again in the future when newer NVIDIA drivers are released
3. Check for any performance differences between X11 and Wayland with the NVIDIA 570 drivers

## If Problems Persist
1. Boot into the previous kernel (6.13.8-1-default) from the GRUB menu
2. Consider creating a new snapshot before further troubleshooting
3. Check logs for specific errors:
   ```bash
   journalctl -b | grep -i nvidia
   dmesg | grep -i nvidia
   ```

## Kernel 6.14 Test Results (2025-04-09)

**Test:**
- Rebooted system and selected kernel `6.14.0-1.1.x86_64` from GRUB menu.
- NVIDIA driver version: `570.133.07`
- Display server: Wayland

**Outcome:**
- As expected, the GUI (KDE Plasma) failed to load, dropping the system to TTY.

**Log Analysis (`journalctl -b 0`):**
- Key errors originated from `kwin_wayland`:
  ```
  kwin_scene_opengl: Invalid framebuffer status: "GL_FRAMEBUFFER_INCOMPLETE_MISSING_ATTACHMENT"
  kwin_wayland_drm: Failed to create framebuffer: Invalid argument
  ```
- Further investigation using `ls /lib/modules/6.14.0-1.1.x86_64/updates/nvidia.ko` revealed that the NVIDIA kernel module (`nvidia.ko`) was **not** built or installed for kernel 6.14.0-1.1. This is the root cause of the GUI failure.

**Driver Availability Check:**
- `zypper info nvidia-driver-G06-kmp-default` confirmed that `570.133.07` is the latest version available in the configured `repo-non-free`. No newer driver is currently available via standard repositories.

**Conclusion & Decision:**
- The NVIDIA driver `570.133.07` is incompatible with kernel `6.14.0-1.1` on this system, primarily because the required kernel module failed to build.
- The necessary kernel and NVIDIA packages have been locked using `zypper addlock` (as confirmed by the user) to maintain the stable configuration (Kernel 6.13.8 + NVIDIA 570.133.07).
- Will wait for a future NVIDIA driver release that officially supports kernel 6.14+ on openSUSE Tumbleweed before attempting to upgrade the kernel again.
