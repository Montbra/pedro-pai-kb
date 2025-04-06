# Signal with Crisp Fonts on Wayland

## Problem
After switching to Wayland, Signal Desktop (Flatpak) had blurry fonts when launched from the application menu, but had crisp fonts when launched from the command line with specific Wayland environment variables.

## Solution
Created a custom launcher script that properly sets up the Wayland environment before launching Signal.

## Implementation

### 1. Create a Wayland-specific launcher script

Created a script at `/home/henrique/.local/bin/signal-wayland-launcher.sh`:

```bash
#!/bin/bash

# Ensure Wayland environment variables are set
export XDG_SESSION_TYPE=wayland
export GDK_BACKEND=wayland
export CLUTTER_BACKEND=wayland
export QT_QPA_PLATFORM=wayland
export ELECTRON_OZONE_PLATFORM_HINT=wayland
export OZONE_PLATFORM=wayland

# Small delay to ensure environment is ready
sleep 0.5

# Launch Signal with Wayland settings
if [[ "$1" == "@@u" ]]; then
  exec /usr/bin/flatpak run --branch=stable --arch=x86_64 --command=signal-desktop --env=OZONE_PLATFORM=wayland --env=ELECTRON_OZONE_PLATFORM_HINT=wayland --file-forwarding org.signal.Signal "$@"
else
  exec /usr/bin/flatpak run --branch=stable --arch=x86_64 --command=signal-desktop --env=OZONE_PLATFORM=wayland --env=ELECTRON_OZONE_PLATFORM_HINT=wayland org.signal.Signal "$@"
fi
```

Made the script executable:
```bash
chmod +x /home/henrique/.local/bin/signal-wayland-launcher.sh
```

### 2. Create a custom desktop entry

Created a new desktop entry at `/home/henrique/.local/share/applications/signal-wayland-crisp.desktop`:

```ini
[Desktop Entry]
Name=Signal (Crisp Fonts)
Exec=/home/henrique/.local/bin/signal-wayland-launcher.sh @@u %U @@
Terminal=false
Type=Application
Icon=org.signal.Signal
StartupWMClass=Signal
Comment=Private messaging with crisp Wayland fonts
MimeType=x-scheme-handler/sgnl;x-scheme-handler/signalcaptcha;
Categories=Network;InstantMessaging;Chat;
X-Desktop-File-Install-Version=0.28
```

Updated the desktop database:
```bash
update-desktop-database ~/.local/share/applications
```

### 3. Reset any existing flatpak overrides

```bash
flatpak override --user --reset org.signal.Signal
```

## Why This Works

1. The script explicitly sets all necessary Wayland environment variables before launching Signal
2. The `--env` flags in the flatpak command ensure these variables are passed to the flatpak container
3. The script preserves file forwarding functionality for handling links and attachments
4. The small delay ensures the environment is fully set up before launching Signal

## Troubleshooting

If Signal still launches with blurry fonts:
1. Try increasing the sleep time in the script
2. Check if your system is actually running Wayland with `echo $XDG_SESSION_TYPE`
3. Verify that other Electron apps work properly with Wayland

If Signal crashes or doesn't start:
1. Try removing some of the environment variables
2. Check the logs with `flatpak logs org.signal.Signal`
3. Try running with `--verbose` added to the flatpak command
