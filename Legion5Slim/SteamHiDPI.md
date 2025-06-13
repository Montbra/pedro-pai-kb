# Steam HiDPI Configuration for 4K TV

## Problem
Steam's UI was too small on a 4K TV viewed from 2 meters away, and font rendering was blurry when using application-specific scaling methods.

## Attempted Solutions
We initially tried multiple approaches to improve Steam's scaling and font rendering on a 4K TV:

1. **Environment Variables for Scaling**
   ```bash
   export GDK_SCALE=2
   export GDK_DPI_SCALE=0.5
   export QT_AUTO_SCREEN_SCALE_FACTOR=1
   export QT_SCALE_FACTOR=1.5
   ```
   Result: No noticeable improvement in font rendering.

2. **Steam's Own Scaling Parameter**
   ```bash
   export STEAM_FORCE_DESKTOPUI_SCALING=1.2
   export STEAM_DISPLAY_SCALE_FACTOR=175
   ```
   Result: UI elements scaled but fonts remained blurry. Higher values made UI too large.

3. **Font Rendering Improvements**
   ```bash
   export FREETYPE_PROPERTIES="truetype:interpreter-version=35"
   export STEAM_USE_SYSTEM_FONTS=1
   ```
   Result: No significant improvement in font clarity.

4. **Gamescope Compositor**
   ```bash
   gamescope -w 2560 -h 1440 -f -- /usr/bin/steam -tenfoot
   ```
   Result: Failed to properly launch Steam.

5. **Command Line Parameters**
   ```bash
   /usr/bin/steam -forcedesktopscaling 175
   ```
   Result: Similar to environment variables - scaling worked but fonts remained blurry.

## Successful Solution: KDE Plasma Display Scaling

After trying all the application-specific approaches above, we discovered that the best solution was to use KDE Plasma's built-in display scaling:

1. Go to System Settings > Display and Monitor
2. Select the display you want to configure
3. Adjust the "Scale" slider to an appropriate value (typically 150% or 200% for 4K displays)
4. Apply the changes
5. Log out and log back in for the changes to take full effect

**Important Note:** This solution works best under X11 (not Wayland) as X11 handles the scaling more consistently across all applications.

## Advantages of Using KDE's Display Scaling

1. System-wide solution that benefits all applications, not just Steam
2. Better font rendering quality compared to application-specific scaling
3. More consistent UI scaling across different elements
4. Persistent across reboots and sessions
5. Officially supported and tested by the KDE team

## Conclusion

While we initially tried to solve the problem with application-specific solutions, the best approach was to use the desktop environment's built-in scaling capabilities. This is a good reminder that system-level solutions often provide better results than trying to configure individual applications, especially for issues like HiDPI scaling.

For Steam specifically, the KDE Plasma display scaling provides both appropriate sizing for a 4K TV viewed from 2 meters away and maintains good font rendering quality.
