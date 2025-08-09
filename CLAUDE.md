# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a ZMK (Zephyr Mechanical Keyboard) firmware configuration repository for a wireless Corne keyboard setup. The project uses the Zephyr RTOS and ZMK firmware framework to create custom keyboard firmware with multiple layers, combos, and Bluetooth connectivity.

## Build Commands

### Prerequisites
- Install ZMK development environment with west tool
- Clone ZMK repository separately and use this config as a module

### Building Firmware
Build for left and right halves separately:

```bash
# Build left half
west build -d build/left -b nice_nano_v2 -- -DSHIELD=corne_left -DZMK_CONFIG="$(pwd)"

# Build right half  
west build -d build/right -b nice_nano_v2 -- -DSHIELD=corne_right -DZMK_CONFIG="$(pwd)"

# Build dongle (central)
west build -d build/dongle -b seeeduino_xiao_ble -- -DSHIELD=corne_dongle -DZMK_CONFIG="$(pwd)"
```

Build using GitHub Actions (automatic):
- The `build.yaml` file configures GitHub Actions to build firmware automatically
- Builds left half, right half, and dongle when changes are pushed to the repository
- Artifacts are available in GitHub Actions runs

### Alternative Build Method
If working within the ZMK source tree:
```bash
west build -b nice_nano_v2 -- -DSHIELD=corne_left
west build -b nice_nano_v2 -- -DSHIELD=corne_right
```

## Architecture and Key Components

### Configuration Structure
- `config/corne.keymap` - Main keymap configuration with 4 layers
- `config/corne_dongle.conf` - Dongle-specific configuration settings
- `config/west.yml` - West manifest file specifying ZMK as upstream dependency
- `build.yaml` - GitHub Actions build matrix configuration
- `zephyr/module.yml` - Zephyr module configuration
- `boards/shields/corne/` - Custom Corne dongle shield definitions
  - `Kconfig.shield` - Shield selection configuration
  - `Kconfig.defconfig` - Default configuration for dongle
  - `corne_dongle.overlay` - Device tree overlay with mock kscan

### Keymap Layers (config/corne.keymap:22-88)
1. **Layer 0 (Default)**: Standard QWERTY layout with modifier keys
2. **Layer 1 (Lower)**: Numbers, brackets, and punctuation symbols  
3. **Layer 2 (Raise)**: Mouse movement, media keys, navigation arrows
4. **Layer 3 (Combined)**: Bluetooth controls, activated when both Layer 1+2 are active

### Key Features
- **Combos**: Tab combo on positions 1+2 (config/corne.keymap:16-20)
- **Conditional Layers**: Layer 3 activates when both layers 1 and 2 are pressed (config/corne.keymap:80-87)
- **Bluetooth Support**: BT clear, select, and disconnect functions on Layer 3
- **Mouse/Pointing Device Support**: Scroll and mouse click/movement on Layer 2

### Hardware Configuration
- **MCU**: nice!nano v2 (nRF52840-based) for keyboard halves
- **Dongle MCU**: Seeeduino XIAO BLE (nRF52840-based) for central/dongle
- **Keyboard**: Corne (CRKBD) split keyboard
- **Connectivity**: Bluetooth wireless with ZMK support

### Dongle Setup
The dongle acts as a central device that:
- Connects to both keyboard halves via Bluetooth
- Presents a single USB keyboard interface to the computer
- Uses the same keymap as the split keyboard
- Provides better power management when keyboard halves are not in use

## Development Workflow

1. Modify keymap in `config/corne.keymap`
2. Test locally with west build commands
3. Push changes to trigger GitHub Actions build
4. Download firmware artifacts from GitHub Actions
5. Flash .uf2 files to respective keyboard halves

## Troubleshooting Split Keyboard and Dongle Issues

### Settings Reset (Factory Reset)
If you're experiencing pairing issues between keyboard halves or with the dongle, use the settings reset firmware:

1. **Flash settings reset firmware**:
   - Download `left_settings_reset.uf2`, `right_settings_reset.uf2`, and `dongle_settings_reset.uf2` from GitHub Actions
   - Flash each device with its respective settings reset firmware
   - This erases all Bluetooth pairings and settings

2. **Reset pairing process**:
   - Flash normal firmware back to each device (`corne_left.uf2`, `corne_right.uf2`, `corne_dongle.uf2`)
   - Reset all devices simultaneously (press reset buttons at the same time)
   - Allow 10-15 seconds for automatic pairing

3. **Dongle setup order**:
   - First flash and reset the keyboard halves together
   - Then power on the dongle to connect as central

### Common Issues
- **Only one side working**: Reset both halves simultaneously after flashing normal firmware
- **Wrong key mappings**: Usually indicates the halves are swapped - check which side is responding
- **No connection**: Use settings reset firmware and follow the complete reset process

## Key File Locations
- Main keymap: `config/corne.keymap`
- Build configuration: `build.yaml` 
- ZMK dependency: `config/west.yml`
- Module definition: `zephyr/module.yml`