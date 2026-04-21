# Baseline Técnico: Lenovo Legion Slim 5 (Abril 2026)

Este documento registra a configuração estável de hardware e software que resolveu os problemas de Xid 154 e habilitou o uso de Wayland/X11 com TV 4K.

---

## 🏗️ Hardware Principal
- **Modelo:** Lenovo Legion Slim 5 16AHP9 (83DH)
- **CPU:** AMD Ryzen 7 8845HS w/ Radeon 780M Graphics (16 cores)
- **Memória RAM:** 32GB (27.2GB úteis reportados pelo kernel)
- **BIOS:** `NRCN25WW` (Data: 19/09/2025)
- **Armazenamento:**
  - 2TB Lexar SSD NM620 (Primário)
  - 1TB XPG GAMMIX S11 Pro (Secundário)

---

## 🎮 Unidades Gráficas (GPUs)
### 1. dGPU (NVIDIA)
- **Modelo:** GeForce RTX 4070 Laptop GPU (AD106M / Ada Lovelace)
- **Device ID:** `10de:2860` (rev a1)
- **VBIOS:** `95.06.31.00.85`
- **Capacidade:** 8GB VRAM GDDR6

### 2. iGPU (AMD)
- **Modelo:** Radeon 780M (HawkPoint1)
- **Device ID:** `1002:1900` (rev c5)

---

## 💿 Software & Drivers (Estado Estável)
- **OS:** openSUSE Tumbleweed (Rolling Release)
- **Kernel:** `6.19.9-1-default` (x86_64)
- **Driver NVIDIA:** `580.142` (GSP Firmware Desativado)
- **CUDA suportado:** 13.0+
- **Compositor:** KDE Plasma (Wayland/X11 OK)

---

## 🛠️ Notas de Integração
- **HDMI Traseiro:** Conectado fisicamente à NVIDIA (RTX 4070).
- **USB-C Esquerdo (DisplayPort):** Conectado fisicamente à AMD (Radeon 780M).
- **Setup de Ouro:** TV 4K via USB-C -> HDMI (Para economizar VRAM da NVIDIA para LLMs).

---

## 🔧 Parâmetros Críticos do Kernel
Sempre garantir que estes parâmetros estejam ativos no `/etc/modprobe.d/nvidia.conf` e no `dracut`:
- `NVreg_EnableGpuFirmware=0`
- `nvidia-drm modeset=1 fbdev=1`
- `NVreg_PreserveVideoMemoryAllocations=1`

**Última Atualização:** 07 de Abril de 2026
