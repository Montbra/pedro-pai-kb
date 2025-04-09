# Lenovo Legion 5 Slim (16AHP9) Specifications

This document provides a comprehensive overview of the hardware and software configuration of my Lenovo Legion 5 Slim laptop. It's intended to serve as reference documentation and to facilitate future troubleshooting.

## Hardware Specifications

### System Overview
- **Model**: Lenovo Legion Slim 5 16AHP9
- **Type**: Laptop
- **Serial Number**: [REDACTED]
- **BIOS/UEFI**: LENOVO NRCN20WW (Date: 10/04/2024)

### Processor
- **CPU**: AMD Ryzen 7 8845HS w/ Radeon 780M Graphics
- **Architecture**: x86_64 (64-bit)
- **Cores**: 8 cores, 16 threads
- **Clock Speed**: 400 MHz - 3.8 GHz (boost enabled)
- **Cache**: 
  - L1: 512 KiB (256 KiB L1d + 256 KiB L1i, 8 instances)
  - L2: 8 MiB (8 instances)
  - L3: 16 MiB (1 instance)

### Memory
- **RAM**: 28 GB (27.21 GiB usable)
- **Swap**: 27.21 GiB (zram)

### Graphics
- **Discrete GPU**: NVIDIA GeForce RTX 4070 Max-Q / Mobile (AD106M)
  - Driver: NVIDIA v570.133.07
  - Architecture: Lovelace
  - PCIe: 2.5 GT/s (8 lanes)
  
- **Integrated GPU**: AMD Radeon 780M Graphics (Phoenix3)
  - Driver: amdgpu (kernel)
  - Architecture: RDNA-3
  - PCIe: 16 GT/s (16 lanes)

### Display
- **Primary Display**: BOE Display 0x0b38
  - Resolution: 2560x1600
  - Size: 344x215mm (16 inches)
  - DPI: 189
  
- **External Display Support**:
  - HDMI-A-1
  - DisplayPort (via USB-C)

### Storage
1. **Primary SSD**: Seagate XPG GAMMIX S11 Pro
   - Capacity: 953.87 GiB
   - Interface: NVMe PCIe 4.0 (31.6 Gb/s, 4 lanes)
   - Partitioning:
     - `/dev/nvme0n1p1`: 512 MiB (EFI System Partition, FAT32)
     - `/dev/nvme0n1p2`: 953.4 GiB (Btrfs, mounted at /, /home, /opt, /var)

2. **Secondary SSD**: Lexar SSD NM620 2TB
   - Capacity: 1.86 TiB
   - Interface: NVMe PCIe 4.0 (31.6 Gb/s, 4 lanes)
   - Partitioning:
     - `/dev/nvme1n1p1`: 260 MiB (FAT32)
     - `/dev/nvme1n1p2`: 16 MiB
     - `/dev/nvme1n1p3`: 1.9 TiB (NTFS)
     - `/dev/nvme1n1p4`: 2 GiB (NTFS)

### Network
- **Ethernet**: Realtek RTL8111/8168/8211/8411 PCI Express Gigabit Ethernet
  - Interface: enp2s0
  
- **Wi-Fi**: MEDIATEK MT7922 802.11ax PCI Express Wireless
  - Interface: wlo1
  - Standard: Wi-Fi 6E (802.11ax)
  
- **Bluetooth**: Foxconn / Hon Hai Bluetooth 5.2 Adapter [MediaTek MT7922]
  - Version: 5.2

### Audio
- NVIDIA AD106M High Definition Audio
- AMD Rembrandt Radeon High Definition Audio
- AMD ACP/ACP3X/ACP6x Audio Coprocessor
- AMD Family 17h/19h/1ah HD Audio

### Camera
- Chicony Integrated Camera (USB)
- ID: 04f2:b7b6

### Battery
- **Model**: Sunwoda L22D4PC2
- **Type**: Li-polymer
- **Capacity**: 80.0 Wh (current condition: 83.7 Wh)
- **Voltage**: 16.1V

### Ports
- USB-A ports
- USB-C ports
- HDMI port
- Ethernet port
- 3.5mm audio jack

### Input Devices
- Built-in keyboard
- Touchpad
- External Logitech Wireless Keyboard (connected via USB receiver)

## Software Configuration

### Operating System
- **Distribution**: openSUSE Tumbleweed
- **Version**: 20250403
- **Kernel**: 6.13.8-1-default

### Desktop Environment
- **DE**: KDE Plasma 6.3.4
- **Display Server**: Wayland (with Xwayland 24.1.6)
- **Window Manager**: KWin (Wayland)
- **Display Manager**: SDDM

### Graphics Configuration
- **Current Renderer**: AMD Radeon Graphics (amdgpu driver)
- **NVIDIA Driver**: 570.133.07 (installed)
- **OpenGL**: v4.6.0 (AMD Mesa 25.0.2)
- **Vulkan**: v1.4.309

### Audio Configuration
- **Audio Server**: PipeWire 1.4.1
  - pipewire-pulse (active)
  - wireplumber (active)
  - pipewire-alsa (plugin)
  - pw-jack (plugin)

### Network Configuration
- **Wi-Fi Connected**: Yes (wlo1)
- **Ethernet**: Not connected (enp2s0)
- **Docker Network Interfaces**:
  - docker0 (172.17.0.1/16)
  - br-c6b5203bfe1c (172.18.0.1/16)

### Amazon Q Configuration
- **Installation Path**: `/home/henrique/AppImages/amazon-q.appimage`
- **Autostart**: Enabled via `/home/henrique/.config/autostart/amazon-q.desktop`
- **Custom Startup Script**: `/home/henrique/.local/bin/start-amazon-q-minimized.sh`
  - Uses wmctrl to minimize Amazon Q on startup

## Troubleshooting Notes

### Graphics Switching
- System uses AMD integrated graphics by default for Wayland session
- NVIDIA GPU is available for applications that require it
- No prime-select utility installed (uses native hybrid graphics)

### Power Management
- System supports suspend states: freeze, mem, disk
- Default suspend mode: s2idle

### Known Issues and Solutions
- **Amazon Q Starting Maximized**: Solved with custom wrapper script using wmctrl
  - See `/home/henrique/AmazonQ.md` for detailed solution

## Maintenance Information

### Firmware/BIOS Updates
- Current BIOS version: NRCN20WW (10/04/2024)
- Check Lenovo support site for updates: https://support.lenovo.com/

### Driver Information
- NVIDIA driver: 570.133.07
- AMD graphics driver: kernel amdgpu
- Wi-Fi driver: mt7921e (kernel)
- Bluetooth driver: btusb v0.8

### System Health
- CPU temperature: ~40°C (idle)
- GPU temperature: ~44°C (AMD), ~N/A (NVIDIA)
- SSD temperatures: 34.9°C (Seagate), 38.9°C (Lexar)
- Battery health: 104.7% (83.7/80.0 Wh)
- Battery cycles: 5

## Additional Resources

- [Lenovo Legion 5 Slim Support Page](https://support.lenovo.com/)
- [openSUSE Tumbleweed Documentation](https://en.opensuse.org/Portal:Tumbleweed)
- [KDE Plasma Documentation](https://kde.org/plasma-desktop/)
- [NVIDIA Linux Driver Documentation](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/)
- [AMD GPU Documentation](https://docs.kernel.org/gpu/amdgpu.html)
