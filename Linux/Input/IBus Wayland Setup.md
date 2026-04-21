# IBus Wayland Setup Guide

This document provides instructions for properly configuring IBus to work with Wayland sessions in KDE.

## Problem
IBus doesn't start automatically on boot in Wayland sessions, and environment variables may interfere with proper operation.

## Solution

### Step 1: Unset Input Method Environment Variables
Create a script to unset QT_IM_MODULE and GTK_IM_MODULE environment variables at login:

```bash
# File: /home/henrique/.config/plasma-workspace/env/unset_im_modules.sh
#!/bin/sh
# Unset input method environment variables for proper IBus Wayland operation
unset QT_IM_MODULE
unset GTK_IM_MODULE
```

Make the script executable:
```bash
chmod +x /home/henrique/.config/plasma-workspace/env/unset_im_modules.sh
```

### Step 2: Create Autostart Entry for IBus Wayland
Create an autostart desktop entry for IBus Wayland:

```ini
# File: /home/henrique/.config/autostart/ibus-wayland.desktop
[Desktop Entry]
Name=IBus Wayland
Exec=/usr/libexec/ibus/ibus-ui-gtk3 --enable-wayland-im --exec-daemon --daemon-args "--xim --panel disable"
Type=Application
X-KDE-Wayland-VirtualKeyboard=true
Icon=ibus
```

### Step 3: Configure IBus in KDE System Settings
1. Open KDE System Settings (run `systemsettings5` in terminal)
2. Go to "Input Devices" → "Virtual Keyboard"
3. Select "IBus Wayland" icon
4. Click "Apply" button

### Step 4: Apply Changes
Log out and log back in to apply these changes.

## Additional Configuration
- Run `ibus-setup` to configure your preferred input methods
- Verify IBus is running with `ibus-daemon -drx`

## Troubleshooting
If IBus still doesn't work properly:
1. Check if the service is running: `systemctl --user status ibus.service`
2. Verify environment variables are unset: `echo $QT_IM_MODULE $GTK_IM_MODULE`
3. Manually start IBus: `ibus-daemon -drx`

## References
- For other desktop environments, copy the 'Exec=' line from org.freedesktop.IBus.Panel.Wayland.Gtk3.desktop to your session configuration file
- Refer to your desktop environment's documentation about "Wayland input method" configuration
