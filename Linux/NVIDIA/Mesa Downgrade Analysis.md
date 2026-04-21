# Mesa Downgrade Analysis - System Freeze Issue

## Issue Summary
- **Date of Incident**: June 1, 2025
- **Symptom**: System freeze during YouTube video playback
- **Behavior**: Visual interface became unresponsive, but audio continued to play
- **Resolution**: Forced power reboot was required

## Root Cause Identified
A significant Mesa graphics library downgrade occurred on the same day as the system freeze:
- **Previous Version**: Mesa 25.1.1 (installed on May 31, 2025)
- **Downgraded Version**: Mesa 25.0.5 (installed on June 1, 2025)

## Technical Details

### System Configuration
- **Laptop**: Lenovo Legion 5 Slim (16AHP9)
- **Operating System**: openSUSE Tumbleweed (20250505)
- **Kernel**: 6.13.8-1-default
- **Desktop Environment**: KDE Plasma 6.3.4 on Wayland

### Graphics Hardware
- **Integrated GPU**: AMD Radeon 780M Graphics (Phoenix3)
- **Discrete GPU**: NVIDIA GeForce RTX 4070 Max-Q
- **Display Setup**: External 4K monitor connected via HDMI to NVIDIA GPU
- **Rendering**: Despite HDMI connection to NVIDIA, system was using AMD GPU for rendering

### Error Logs
KWin (Wayland compositor) showed multiple OpenGL errors:
```
kwin_scene_opengl: Invalid framebuffer status: "GL_FRAMEBUFFER_INCOMPLETE_MISSING_ATTACHMENT"
kwin_scene_opengl: GL_INVALID_OPERATION error generated. <image> and <target> are incompatible
kwin_scene_opengl: Invalid framebuffer status: "GL_FRAMEBUFFER_INCOMPLETE_ATTACHMENT"
```

### Package Changes
The following Mesa packages were downgraded on June 1, 2025:
- Mesa-libva (25.1.1 → 25.0.5)
- Mesa-gallium-32bit (25.1.1 → 25.0.5)
- Mesa-vulkan-device-select-32bit (25.1.1 → 25.0.5)
- Mesa-vulkan-device-select (25.1.1 → 25.0.5)
- Mesa-dri-32bit (25.1.1 → 25.0.5)
- Mesa-dri (25.1.1 → 25.0.5)
- Mesa-libEGL1 (25.1.1 → 25.0.5)
- Mesa (25.1.1 → 25.0.5)
- Mesa-libGL1 (25.1.1 → 25.0.5)
- Mesa-gallium (25.1.1 → 25.0.5)
- Mesa-32bit (25.1.1 → 25.0.5)
- Mesa-libGL1-32bit (25.1.1 → 25.0.5)

## Diagnostic Tests Performed

### System Logs Analysis
```bash
# Checking system logs from previous boot for errors
journalctl -b -1 -n 100 | grep -i "error\|fail\|crash\|killed"

# Output showed multiple DrKonqi crash reports and KWin OpenGL errors
```

### GPU Status Check
```bash
# Checking NVIDIA GPU status and configuration
nvidia-smi

# Output:
# NVIDIA-SMI 570.153.02             Driver Version: 570.153.02     CUDA Version: 12.8
# NVIDIA GeForce RTX 4070 ...    On  |   00000000:01:00.0  On
# 48C    P8              3W /   55W |     168MiB /   8188MiB |      0%      Default
```

### Display Configuration Check
```bash
# Checking current display configuration
xrandr --listproviders
# Output: Providers: number : 0

# Checking which GPU is being used for rendering
glxinfo | grep "OpenGL renderer"
# Output: OpenGL renderer string: AMD Radeon Graphics (radeonsi, phoenix, LLVM 20.1.4, DRM 3.61, 6.13.8-1-default)

# Checking connected displays
kscreen-doctor -o
# Output showed:
# - eDP-1 (internal display): disabled
# - HDMI-A-1 (external monitor): enabled, 3840x2160@60
```

### Video Acceleration Check
```bash
# Installing video acceleration info tool
sudo zypper install -y libva-utils

# Checking video acceleration capabilities
vainfo
# Output showed VA-API working through AMD driver:
# Driver: Mesa Gallium driver 25.0.5 for AMD Radeon Graphics
```

### Package History Analysis
```bash
# Checking recent package installations
sudo grep -i "2025-06-01" /var/log/zypp/history | grep -i "install\|update\|downgrade" | grep -i "mesa\|libva\|drm\|wayland\|kwin\|plasma"

# Checking previous Mesa version
sudo grep -i "mesa" /var/log/zypp/history | grep -i "install\|update" | grep -v "2025-06-01" | tail -10
```

## Recommended Solutions

### Short-term Fix
Upgrade Mesa back to the previous working version:
```bash
sudo zypper install --oldpackage Mesa-25.1.1 Mesa-dri-25.1.1 Mesa-gallium-25.1.1 Mesa-libGL1-25.1.1 Mesa-libEGL1-25.1.1 Mesa-libva-25.1.1 Mesa-vulkan-device-select-25.1.1
```

### Prevent Future Downgrades
Pin Mesa packages to prevent automatic downgrades:
```bash
sudo zypper addlock Mesa* libva*
```

### Report the Issue
Report this regression to the openSUSE Tumbleweed team:
```bash
sudo zypper install bugzilla-submit
bugzilla-submit
```

## Investigation Notes
Further investigation needed to determine:
1. Why the downgrade occurred (repository configuration issue?)
2. Whether this is a known issue with Mesa 25.0.5
3. If there are specific compatibility issues with hybrid AMD/NVIDIA setups
4. Whether the Packman repository is causing conflicts with official packages

## References
- System hardware specifications: `/home/henrique/pedro-pai-kb/Legion5Slim/hardware_specs.md`
- Package history: `/var/log/zypp/history`
- System logs: `journalctl -b -1`
