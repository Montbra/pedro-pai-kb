# Legion 5 Slim Display Disconnect Issue Fix

## Problem Description
When turning off the external 49-inch 4K TV connected via HDMI, the Legion 5 Slim laptop would hang with only the mouse remaining active, requiring a power reset to recover.

## Root Cause Analysis
Analysis of system logs revealed that when the external display was disconnected, the system experienced:

1. **Display Output Loss**: Multiple instances of `"There are no outputs - creating placeholder screen"` messages
2. **Framebuffer and DRM Errors**:
   - `kwin_scene_opengl: Invalid framebuffer status: "GL_FRAMEBUFFER_INCOMPLETE_MISSING_ATTACHMENT"`
   - `kwin_wayland_drm: Failed to create framebuffer: Invalid argument`
3. **Atomic Mode Setting Failures**:
   - `kwin_wayland_drm: atomic commit failed: Invalid argument`
4. **OpenGL Errors**:
   - `GL_INVALID_ENUM error generated. Invalid <face>.`
   - `GL_INVALID_OPERATION error generated. <image> and <target> are incompatible`

The issue was related to the hybrid graphics setup (AMD Radeon 780M + NVIDIA RTX 4070 Max-Q) and how the Wayland compositor handled display disconnection events.

## Applied Solution
Created a DRM configuration file to enable proper Direct Rendering Manager (DRM) kernel mode setting for both GPUs:

**File**: `/etc/modprobe.d/drm.conf`
```
options nvidia-drm modeset=1
options amdgpu dc=1
```

### What This Does
- `nvidia-drm modeset=1`: Enables kernel mode setting for the NVIDIA driver, which allows for proper integration with the Linux DRM subsystem
- `amdgpu dc=1`: Enables the Display Core (DC) feature for the AMD GPU driver, which provides better display handling capabilities

### Installation Steps
1. Created the configuration file:
   ```bash
   sudo nano /etc/modprobe.d/drm.conf
   ```

2. Added the following content:
   ```
   options nvidia-drm modeset=1
   options amdgpu dc=1
   ```

3. Saved the file and rebooted the system:
   ```bash
   sudo reboot
   ```

## Additional Potential Solutions (Not Implemented)
1. **NVIDIA Power Management Settings**:
   ```
   options nvidia NVreg_PreserveVideoMemoryAllocations=1 NVreg_TemporaryFilePath="/var/tmp" NVreg_DynamicPowerManagement=0x02 NVreg_UsePageAttributeTable=1
   ```

2. **Disable Atomic Mode Setting**:
   ```
   options drm_kms_helper atomic=0
   ```

## System Configuration
- **Laptop**: Lenovo Legion 5 Slim (16AHP9)
- **CPU**: AMD Ryzen 7 8845HS
- **GPUs**: AMD Radeon 780M (integrated) + NVIDIA RTX 4070 Max-Q (discrete)
- **OS**: openSUSE Tumbleweed
- **Kernel**: 6.13.8-1-default
- **Desktop Environment**: KDE Plasma 6.3.4 (Wayland)
- **NVIDIA Driver**: 570.133.07

## References
- System logs from journalctl
- Hardware specifications from `/home/henrique/pedro-pai-kb/Legion 5 Slim/hardware_specs.md`
- Sleep issue diagnosis from `/home/henrique/pedro-pai-kb/Legion 5 Slim/sleep_issue_diagnosis.md`
