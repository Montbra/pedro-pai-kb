# Audio Troubleshooting for Gaming Issues

## Problem Description
- Audio issues occurring specifically during gaming sessions
- Symptoms include crackling and brief audio cuts
- Issues present in both headphone and HDMI audio outputs
- Regular audio playback in browsers seems to work fine

## System Configuration
- **OS**: openSUSE Tumbleweed 20250505
- **Kernel**: 6.13.8-1-default
- **Audio Server**: PipeWire 1.4.2
- **Audio Hardware**:
  - NVIDIA AD106M High Definition Audio (HDMI)
  - AMD Rembrandt Radeon High Definition Audio
  - AMD ACP/ACP3X/ACP6x Audio Coprocessor
  - AMD Family 17h/19h/1ah HD Audio

## Current Audio Settings
- Default sink: alsa_output.pci-0000_01_00.1.hdmi-stereo
- Default source: alsa_input.pci-0000_06_00.6.analog-stereo
- Sample rate: 48000Hz
- Format: s32le 2ch
- Current buffer size: 1024 frames
- Current period size: 32768 frames

## Diagnostic Tests Performed
1. **Checked PipeWire configuration**:
   - Default clock quantum: 1024
   - Minimum quantum: 1024
   - PulseAudio minimum request: 512/48000

2. **Tested without PulseAudio compatibility layer**:
   - Stopped pipewire-pulse.socket
   - Result: No sound in games, Firefox had sound but no volume control

## Potential Solutions (Not Yet Implemented)

### 1. Create Custom PipeWire Configuration
```bash
mkdir -p ~/.config/pipewire
cp /usr/share/pipewire/pipewire.conf ~/.config/pipewire/
cp /usr/share/pipewire/pipewire-pulse.conf ~/.config/pipewire/
```

Edit `~/.config/pipewire/pipewire.conf`:
```
default.clock.rate = 48000
default.clock.quantum = 256
default.clock.min-quantum = 256
default.clock.max-quantum = 1024
```

Edit `~/.config/pipewire/pipewire-pulse.conf`:
```
pulse.min.req = 256/48000
pulse.default.req = 256/48000
pulse.min.frag = 256/48000
pulse.default.frag = 512/48000
```

### 2. Adjust Real-time Priorities
Create `~/.config/pipewire/pipewire.conf.d/99-custom.conf`:
```
context.properties = {
  default.clock.rate = 48000
  default.clock.quantum = 256
  default.clock.min-quantum = 256
  default.clock.max-quantum = 1024
  core.daemon = true
  core.rt.prio = 88
  core.rtkit.prio = 88
}
```

### 3. Disable Audio Effects
```bash
mkdir -p ~/.config/wireplumber/main.lua.d/
echo "alsa_monitor.properties[\"alsa.jack-detection\"] = false" > ~/.config/wireplumber/main.lua.d/51-disable-jack-detection.lua
```

### 4. HDMI Audio Specific Fixes
```bash
echo "options snd-hda-intel enable_msi=1" | sudo tee -a /etc/modprobe.d/snd-hda-intel.conf
echo "options snd_hda_intel power_save=0" | sudo tee -a /etc/modprobe.d/audio_disable_powersave.conf
```

### 5. CPU Performance Settings
```bash
sudo cpupower frequency-set -g performance
```

## Next Steps
1. Implement the custom PipeWire configuration with lower buffer sizes
2. Test with different audio outputs to isolate the issue
3. Check for IRQ conflicts between audio and GPU
4. Consider updating PipeWire to the latest version if available

After making any changes, restart PipeWire services:
```bash
systemctl --user restart pipewire pipewire-pulse wireplumber
```
