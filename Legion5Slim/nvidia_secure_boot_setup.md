# NVIDIA Driver Secure Boot Setup

## Overview
This document records the process of creating and enrolling a public key for NVIDIA drivers to work with Secure Boot enabled on a Lenovo Legion 5 Slim laptop running openSUSE Tumbleweed.

## System Information
- **Laptop**: Lenovo Legion 5 Slim (16AHP9)
- **OS**: openSUSE Tumbleweed
- **NVIDIA Driver Version**: 570.144
- **GPU**: NVIDIA GeForce RTX 4070 Max-Q / Mobile (AD106M)

## Process

### 1. Creating the Public Key

First, we checked if the NVIDIA public keys directory existed:

```bash
ls -la /usr/share | grep nvidia-pubkeys
```

The directory existed and contained one key:

```bash
ls -la /usr/share/nvidia-pubkeys/
# Output:
# total 4
# drwxr-xr-x 1 root root   92 jan 14 11:20 .
# dr-xr-xr-x 1 root root 5562 mai  3 13:18 ..
# -rw-r--r-- 1 root root  894 jan 14 11:20 MOK-nvidia-driver-G06-550.135-28.1-default.der
```

We created a new public key for the current NVIDIA driver version (570.144):

```bash
sudo openssl req -new -x509 -newkey rsa:2048 -keyout /tmp/MOK.priv -outform DER -out /usr/share/nvidia-pubkeys/MOK-nvidia-driver-G06-570.144.der -nodes -days 36500 -subj "/CN=NVIDIA Driver 570.144/"
```

Set proper permissions on the key file:

```bash
sudo chmod 644 /usr/share/nvidia-pubkeys/MOK-nvidia-driver-G06-570.144.der
```

### 2. Enrolling the Key in MOK Database

We imported the key into the MOK (Machine Owner Key) database:

```bash
sudo mokutil --import /usr/share/nvidia-pubkeys/MOK-nvidia-driver-G06-570.144.der
```

Verified the key was in the enrollment queue:

```bash
sudo mokutil --list-new
```

The output confirmed the key was properly formatted with:
- Common Name: "NVIDIA Driver 570.144"
- Validity: May 11, 2025 to April 17, 2125
- Properly set up as a CA certificate

### 3. Completing the Enrollment Process

To complete the enrollment process:

1. Reboot the system and enter BIOS/UEFI settings (F2 or Fn+F2 on Lenovo Legion)
2. Navigate to "Security" or "Boot" section
3. Enable "Secure Boot" option
4. Save BIOS settings and exit

5. When the MOK management interface appears (blue screen):
   - Select "Enroll MOK"
   - Select "Continue"
   - Select "Yes" to enroll the key
   - Enter the one-time password set during the import process
   - Select "OK" to reboot

After this process, the system should boot normally with Secure Boot enabled, and the NVIDIA drivers should load properly since they're signed with a key in the MOK database.

## References
- [Secure Boot - openSUSE Wiki](https://en.opensuse.org/openSUSE:UEFI_Secure_Boot)
- [Working with Secure Boot - NVIDIA Documentation](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#secure-boot)
