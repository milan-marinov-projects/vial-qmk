# Building Vial Firmware for Splitkb Halcyon Kyria with Multiple Layers

Here's a comprehensive guide on how to successfully build and flash Vial firmware for the Splitkb Halcyon Kyria keyboard with multiple layers.

## Background

The Halcyon Kyria keyboard comes with Vial support preinstalled, but when using the splitkb-recommended QMK userspace firmware, Vial compatibility can be lost. This guide explains how to restore Vial support while increasing the number of available layers.

## Prerequisites

- Linux environment (tested on Ubuntu 24.10)
- Python with pyenv for virtual environments
- Git
- Basic knowledge of terminal commands

## Step 1: Fork and Clone the Vial-QMK Repository

1. Fork the Vial-QMK repository on GitHub: https://github.com/vial-kb/vial-qmk
2. Clone your forked repository:
   ```bash
   git clone https://github.com/YOUR_USERNAME/vial-qmk.git
   cd vial-qmk
   ```

## Step 2: Set Up Python Environment

1. Create a new Python virtual environment using pyenv:
   ```bash
   pyenv virtualenv vial-qmk
   pyenv local vial-qmk
   ```

2. Install the required dependencies:
   ```bash
   pip install -r requirements.txt
   ```

3. Install the QMK CLI:
   ```bash
   pip install qmk
   ```

## Step 3: Set Up QMK Environment

1. Initialize the QMK environment:
   ```bash
   qmk setup
   ```

2. When prompted to clone submodules, select 'y' (yes)

## Step 4: Create a Custom Vial Keymap

1. Create a directory for your custom Vial keymap:
   ```bash
   mkdir -p /path/to/vial-qmk/keyboards/splitkb/halcyon/kyria/keymaps/vial
   ```

2. Copy your existing vial_hlc keymap files from QMK userspace:
   ```bash
   cp /path/to/qmk_userspace/keyboards/splitkb/halcyon/kyria/keymaps/vial_hlc/* /path/to/vial-qmk/keyboards/splitkb/halcyon/kyria/keymaps/vial/
   ```

## Step 5: Modify the Layer Count

1. Edit the config.h file in your custom keymap:
   ```bash
   nano /path/to/vial-qmk/keyboards/splitkb/halcyon/kyria/keymaps/vial/config.h
   ```

2. Set the dynamic keymap layer count:
   ```c
   // Modify this line - 12 layers works well, maximum 18 is possible
   #define DYNAMIC_KEYMAP_LAYER_COUNT 16
   ```

   **Note**: The maximum number of layers is limited by the EEPROM size. If you encounter a compilation error like:
   ```
   static assertion failed: "Dynamic keymaps are configured to use more EEPROM than is available."
   ```
   Try reducing the layer count.

## Step 6: Compile the Firmware

Compile the firmware with the Halcyon TFT display module enabled:

```bash
qmk compile -kb splitkb/halcyon/kyria/rev4 -km vial -e HLC_TFT_DISPLAY=1
```

**Important Note**: The Halcyon module parameter must be `1` (not `yes` or `true`).

## Step 7: Flash the Firmware

1. The compiled firmware will be located at:
   ```
   /path/to/vial-qmk/.build/splitkb_halcyon_kyria_rev4_vial.uf2
   ```

2. Put your keyboard in bootloader mode by double-tapping the reset button
3. The keyboard should appear as a USB drive
4. Copy the UF2 file to the USB drive
5. The keyboard will automatically reset after flashing

## Step 8: Verify Vial Compatibility

1. Open Vial (either the AppImage or web version at https://vial.rocks/)
2. Vial should now recognize your keyboard with your configured layers available

## Key Files

- **config.h**: Contains Vial configuration including UID, layer count, and security settings
- **rules.mk**: Enables Vial and other features
- **vial.json**: Defines the keyboard layout for Vial
- **keymap.json**: Defines the default keymap

## Troubleshooting

If Vial doesn't recognize your keyboard:

1. Try generating a new VIAL_KEYBOARD_UID in config.h
2. Add `#define VIAL_INSECURE yes` to bypass UID checks
3. Try different USB ports or cables
4. Reset the keyboard's EEPROM

## Important Notes

- The vial.json file is critical for Vial to recognize your keyboard properly
- The Halcyon modules require specific parameters (HLC_TFT_DISPLAY=1)
- The Vial-QMK repository is the best choice when you need Vial compatibility
- Layer count is limited by EEPROM size - 12 layers works reliably, 18 is also possible

This approach successfully enables Vial support for the Halcyon Kyria with multiple layers, allowing for extensive keyboard customization.
