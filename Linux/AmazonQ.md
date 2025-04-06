# Amazon Q Start Minimized Configuration

## Problem
Amazon Q was starting maximized on every boot of Tumbleweed.

## Solution
Modified the Amazon Q desktop entry file to include the `--start-minimized` flag.

## Steps Taken

1. Found the Amazon Q desktop file:
```bash
find ~/.config -name "*amazon*" -o -name "*q.*" 2>/dev/null
```
This showed that there was a symlink at `/home/henrique/.config/autostart/amazon-q.desktop`

2. Found the actual desktop file:
```bash
find /usr/share/applications /home/henrique/.local/share/applications -name "*amazon*" -o -name "*q.*" 2>/dev/null
```
This revealed the desktop file at `/home/henrique/.local/share/applications/amazon-q.desktop`

3. Checked the autostart directory:
```bash
ls -la /home/henrique/.config/autostart/
```
Confirmed that `amazon-q.desktop` in the autostart directory is a symlink to `/home/henrique/.local/share/applications/amazon-q.desktop`

4. Modified the desktop file to add the `--start-minimized` flag:

Original content:
```
[Desktop Entry]
Categories=Development;
Exec=/home/henrique/AppImages/amazon-q.appimage
Icon=/home/henrique/.local/share/amazon-q/q-desktop.png
Name=Amazon Q
Terminal=false
Type=Application
```

Updated content:
```
[Desktop Entry]
Categories=Development;
Exec=/home/henrique/AppImages/amazon-q.appimage --start-minimized
Icon=/home/henrique/.local/share/amazon-q/q-desktop.png
Name=Amazon Q
Terminal=false
Type=Application
```

## Testing
To test without rebooting:
1. Close Amazon Q if it's running
2. Launch it again from the application menu
3. It should now start minimized

## If This Doesn't Work
Alternative approaches to try:
1. Check if Amazon Q supports other command line flags for window state
2. Look into using window manager rules to force Amazon Q to start minimized
3. Consider using a script wrapper that launches Amazon Q and then minimizes it using window manager commands

## Using KDE Window Rules (Alternative Solution)

Since the `--start-minimized` flag didn't work, using KDE's window rules is a more reliable approach:

1. Right-click on the title bar of the Amazon Q window and select "More Actions" > "Configure Special Window Settings"

2. In the window that appears:
   - Set a description like "Amazon Q Start Minimized"
   - Under the "Window matching" section, make sure the correct window properties are selected
   - You can click "Detect Window Properties" to automatically fill in the details

3. Go to the "Size & Position" tab and:
   - Find "Minimized" in the list
   - Set it to "Force" and "Yes"
   - Make sure "Apply Initially" is checked

4. Click "OK" to save the rule

5. Test by closing Amazon Q and launching it again from the application menu

This approach uses KDE's built-in window management capabilities and should work reliably for both manual launches and autostart.

## Using a Wrapper Script with wmctrl (Successful Solution)

Since both the `--start-minimized` flag and KDE Window Rules failed to work reliably on boot, a wrapper script using `wmctrl` was implemented.

**Steps:**

1.  **Install `wmctrl`:** Ensure `wmctrl` is installed (e.g., `sudo zypper install wmctrl` on openSUSE).

2.  **Create the Wrapper Script:**
    Create a file named `/home/henrique/.local/bin/start-amazon-q-minimized.sh` with the following content:
    ```bash
    #!/bin/bash

    # Launch Amazon Q AppImage in the background
    /home/henrique/AppImages/amazon-q.appimage &

    # Wait for the window to appear (adjust sleep time if needed)
    sleep 5

    # Find the window ID for Amazon Q and minimize it
    # Assumes the window title contains "Amazon Q"
    WINDOW_ID=$(wmctrl -l | grep "Amazon Q" | awk '{print $1}')

    if [ -n "$WINDOW_ID" ]; then
      wmctrl -i -r "$WINDOW_ID" -b add,hidden
    else
      echo "Amazon Q window not found after 5 seconds." >&2
    fi

    exit 0
    ```

3.  **Make the Script Executable:**
    ```bash
    chmod +x /home/henrique/.local/bin/start-amazon-q-minimized.sh
    ```

4.  **Update the `.desktop` File:**
    Modify the `Exec` line in `/home/henrique/.local/share/applications/amazon-q.desktop` to point to the wrapper script:
    ```ini
    [Desktop Entry]
    Categories=Development;
    Exec=/home/henrique/.local/bin/start-amazon-q-minimized.sh
    Icon=/home/henrique/.local/share/amazon-q/q-desktop.png
    Name=Amazon Q
    Terminal=false
    Type=Application
    ```

**Result:** This approach successfully launches Amazon Q minimized on system boot and manual launch by running the script, which waits briefly and then uses `wmctrl` to minimize the application window.
