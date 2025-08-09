# Corne Keyboard ZMK Config with Dongle

This repository contains the ZMK firmware configuration for a Corne split keyboard with wireless dongle and screen support.

## Hardware Setup

- **Keyboard**: Corne 42-key split keyboard
- **Controllers**: nice!nano v2 for keyboard halves
- **Dongle**: Seeeduino XIAO BLE with ST7789V display
- **Features**: 
  - Wireless dongle with color LCD screen
  - Mouse support
  - Multiple layers with combos
  - Battery monitoring
  - Screen brightness controls

## Architecture

This setup uses a **dongle-based architecture** where:
- The **dongle** acts as the central device (connects to your computer)
- Both **keyboard halves** act as peripherals (connect only to the dongle)
- The **screen** shows real-time keyboard status

### Benefits
- Better battery life on both keyboard halves
- More reliable USB connection via dongle
- Real-time status display (layer, battery, WPM, etc.)
- Easy switching between devices

## Firmware Components

The build generates these firmware files:
- `corne-dongle-screen-seeeduino_xiao_ble-zmk.uf2` - Main dongle with screen
- `corne-left-peripheral-nice_nano_v2-zmk.uf2` - Left keyboard half
- `corne-right-peripheral-nice_nano_v2-zmk.uf2` - Right keyboard half
- `settings-reset-nice-nano-nice_nano_v2-zmk.uf2` - Settings reset for keyboard halves
- `settings-reset-xiao-seeeduino_xiao_ble-zmk.uf2` - Settings reset for dongle

## Flashing Instructions

⚠️ **Important**: Flash in this exact order for proper pairing!

1. **Flash settings reset** on all devices:
   - Dongle: `settings-reset-xiao-seeeduino_xiao_ble-zmk.uf2`
   - Left half: `settings-reset-nice-nano-nice_nano_v2-zmk.uf2`
   - Right half: `settings-reset-nice-nano-nice_nano_v2-zmk.uf2`
2. **Flash the dongle** with `corne-dongle-screen-seeeduino_xiao_ble-zmk.uf2`
3. **Disconnect the dongle** from computer
4. **Flash left half** with `corne-left-peripheral-nice_nano_v2-zmk.uf2`  
5. **Flash right half** with `corne-right-peripheral-nice_nano_v2-zmk.uf2`
6. **Reconnect the dongle** to computer
7. **Power on left half first**, wait for connection indicator on screen
8. **Power on right half**, wait for connection indicator on screen

## Screen Features

The dongle screen displays:
- **Output Status**: USB (white/red) or BLE (green/blue/white) connection status
- **Layer Indicator**: Currently active layer number
- **Modifier Status**: Active modifier keys (Shift, Ctrl, Alt, GUI)
- **WPM Counter**: Real-time typing speed
- **Battery Levels**: Both keyboard halves' battery status

### Screen Controls
- **Brightness Up**: F24 key (mapped to combined layer)
- **Brightness Down**: F23 key (mapped to combined layer)
- **Auto-dim**: Screen dims after 5 minutes of inactivity
- **Orientation**: Horizontal layout (configurable in `corne_dongle.conf`)

## Layer Layout

## Layer 0 (Default)
![image](https://github.com/user-attachments/assets/014ad379-d107-46c7-b51f-ebe1a94fbf79)

## Layer 1 (Lower)
![image](https://github.com/user-attachments/assets/3856c896-3914-4c73-8671-f698b8fda819)

## Layer 2 (Raised) 
![image](https://github.com/user-attachments/assets/dbb92cd3-714e-415d-980b-70e9ad9ce517)

## Layer 3 (Combined - includes screen controls)
![image](https://github.com/user-attachments/assets/bd81d6c9-33ba-4b7b-8fc7-dc7042aa8b9e)

*Note: F23/F24 keys added for screen brightness control*

## Troubleshooting

### Battery Indicators Swapped
If the screen shows swapped battery indicators:
1. Flash `settings-reset` to all devices
2. Follow the flashing order above exactly
3. Ensure left half connects before right half

### Screen Not Working
- Verify the `zmk-dongle-screen` module is properly included in `west.yml`
- Check that ZMK revision is `main` or at least commit `147c340c6`
- Ensure dongle was flashed with `dongle_screen` shield

### Connection Issues
- Reset all devices with `settings-reset` firmware
- Follow the exact pairing sequence above
- Check that cmake args are properly set for peripheral mode

## Configuration Files

Key configuration files:
- `config/west.yml` - Module dependencies
- `build.yaml` - Build targets and peripheral configuration  
- `boards/shields/corne/` - Dongle shield definition
- `config/corne_dongle.conf` - Screen and dongle settings
- `config/corne.keymap` - Key mappings including screen controls

## Customization

### Screen Settings
Edit `config/corne_dongle.conf` to customize:
- Screen orientation and flip
- Brightness levels and auto-dim timeout
- Widget enable/disable
- Brightness control key codes

### Layout Changes
Modify `config/corne.keymap` to:
- Add/remove layers
- Change key mappings
- Adjust combo keys
- Modify mouse settings

## Dependencies

This configuration uses:
- [ZMK Firmware](https://github.com/zmkfirmware/zmk) (main branch)
- [ZMK Dongle Screen Module](https://github.com/janpfischer/zmk-dongle-screen) by janpfischer

## Building Locally

```bash
west build -p -s /path/to/zmk/app -d /path/to/build -b "seeeduino_xiao_ble" -- \
  -DZMK_CONFIG=/path/to/this/config \
  -DSHIELD="corne_dongle dongle_screen" \
  -DZMK_EXTRA_MODULES=/path/to/zmk-dongle-screen/
```