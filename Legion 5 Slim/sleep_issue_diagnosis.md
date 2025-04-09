# Legion 5 Slim Sleep Issue Diagnosis

## Problem
The laptop entered a sleep-like state after a period of inactivity and didn't wake up properly, requiring a forced power reset.

## Diagnosis

### System Configuration
- **Sleep Mode**: Using s2idle (confirmed by `/sys/power/mem_sleep`)
- **Kernel Parameters**: `BOOT_IMAGE=/boot/vmlinuz-6.13.8-1-default root=UUID=65b2d050-362b-4e3a-96fb-e10798d4f9ff amd_pstate=active splash=silent quiet security=apparmor mitigations=auto rd.driver.blacklist=nouveau`
- **Hardware**: Lenovo Legion 5 Slim with AMD Ryzen 7 8845HS and hybrid graphics (AMD Radeon 780M + NVIDIA RTX 4070 Max-Q)

### Root Causes Identified

1. **S2idle Sleep Mode Issue**
   - S2idle (also known as "freeze") is a light sleep state that keeps RAM powered but puts other components to sleep
   - This mode is known to cause issues with AMD Ryzen 8000 series processors and hybrid graphics setups

2. **Hybrid Graphics Complications**
   - The logs show OpenGL errors from KWin: `GL_INVALID_ENUM error` and `GL_INVALID_OPERATION error`
   - These graphics errors might be related to the system's inability to properly resume from sleep

3. **No Specific Power Management Parameters**
   - The kernel command line doesn't include specific power management parameters for the hybrid graphics setup

4. **Inactivity Timeout**
   - No explicit suspend event was recorded in the logs
   - System likely entered a low-power state due to inactivity but failed to maintain proper state management

## Solutions

1. **Change the Sleep Mode**
   - Modify GRUB to use "deep" sleep mode instead of "s2idle"
   - Add `mem_sleep_default=deep` to GRUB_CMDLINE_LINUX

2. **Improve NVIDIA Power Management**
   - Create NVIDIA power management settings in `/etc/modprobe.d/nvidia-pm.conf`
   - Add: `options nvidia NVreg_PreserveVideoMemoryAllocations=1 NVreg_TemporaryFilePath="/var/tmp"`

3. **Disable Automatic Suspend or Increase Timeout**
   - Configure power management settings in KDE System Settings

4. **Check for BIOS Updates**
   - Current BIOS: NRCN20WW (April 10, 2024)
   - Check Lenovo's support site for newer updates

5. **Try X11 Instead of Wayland**
   - Temporarily switch to X11 session to test if Wayland is contributing to the issue

6. **Add AMD Power Management Parameter**
   - Add `amd_pstate=guided` to kernel parameters

## System Logs
```
abr 06 16:02:49 ostw-1 kwin_wayland[2415]: kwin_scene_opengl: 0x500: GL_INVALID_ENUM error generated. Invalid <face>.
abr 06 16:02:49 ostw-1 kwin_wayland[2415]: kwin_scene_opengl: 0x502: GL_INVALID_OPERATION error generated. <image> and <target> are incompatible
[...]
abr 06 23:41:39 ostw-1 kwin_wayland[2415]: kwin_scene_opengl: 0x500: GL_INVALID_ENUM error generated. Invalid <face>.
abr 06 23:41:39 ostw-1 kwin_wayland[2415]: kwin_scene_opengl: 0x502: GL_INVALID_OPERATION error generated. <image> and <target> are incompatible
```

## Implementation Status

- [ ] Changed sleep mode to "deep"
- [ ] Improved NVIDIA power management
- [ ] Adjusted automatic suspend settings
- [ ] Checked for BIOS updates
- [ ] Tested with X11 session
- [ ] Added AMD power management parameter
