# WiFi Disconnection Fix - MediaTek MT7922

## Problem
WiFi randomly disconnecting after system updates. KDE network icon shows no available networks temporarily, then networks reappear. Issue started after recent kernel updates.

## System Info
- **Hardware**: MediaTek MT7922 802.11ax PCI Express Wireless Network Adapter
- **Kernel**: 6.17.7-1-default (problem started with recent updates)
- **Previous working kernel**: 6.17.6-1-default
- **NetworkManager**: 1.54.1-2.1.x86_64
- **Network**: NET_5GE734D0 (5GHz)

## Symptoms
- Random WiFi disconnections
- KDE network manager shows empty network list temporarily
- Mobile devices on same network work normally
- Automatic reconnection after few seconds

## Log Evidence
```
[ 5362.648318] wlo1: deauthenticating from 4c:12:65:df:e9:4a by local choice (Reason: 3=DEAUTH_LEAVING)
[ 5367.588167] wlo1: authenticate with 4c:12:65:df:e9:4a (local address=3c:0a:f3:f1:7c:21)
```

## Solution Applied

### Step 1: Configure Driver Parameters
Created `/etc/modprobe.d/mt7921e.conf` with:
```
options mt7921e disable_aspm=1
```

**Command used:**
```bash
sudo tee /etc/modprobe.d/mt7921e.conf << EOF
options mt7921e disable_aspm=1
EOF
```

### Step 2: Reload WiFi Module
```bash
sudo modprobe -r mt7921e && sudo modprobe mt7921e
```

## Testing
- **Applied**: 2025-11-10 11:19
- **Status**: Monitoring for disconnections
- **Next check**: Monitor for 24-48 hours

## Alternative Solutions (if needed)
1. **Revert to previous kernel**: Boot with 6.17.6-1-default
2. **Additional driver options**: Add `power_save=0` to modprobe config
3. **WiFi watchdog script**: Auto-reconnect on disconnection

## Notes
- ASPM (Active State Power Management) can cause issues with some WiFi cards
- This fix disables power management features that may cause instability
- Monitor system for any power consumption changes
