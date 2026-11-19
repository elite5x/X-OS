# Changes to make WhiteSurLiquid theme more like macOS Liquid Glass

## Kvantum Theme (Qt application style)
- Increased transparency of window backgrounds, input fields, buttons, etc.
- Increased blur radius for menus and tooltips.
- Reduced window opacity reduction for inactive windows (set to 0, relying on color alpha).

## Plasma Desktop Theme (Panels, widgets, etc.)
- Increased transparency of view backgrounds (e.g., in list views, tooltips, window backgrounds) by adding alpha channel.

## Aurorae Window Decoration
- Made the window title bar and background more translucent by adding alpha channel to fill colors.

## Notes
- The blur effect for windows requires the KWin Blur effect to be enabled in system settings.
- Further adjustments may be needed for specific widgets or window states.
