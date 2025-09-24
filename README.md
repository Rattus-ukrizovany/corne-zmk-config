# Corne ZMK Configuration with Dongle Support

This repository contains a ZMK configuration for the Corne keyboard with support for both split halves and a central dongle setup.

## Build Targets

This configuration supports the following build targets:

1. **Left Half** (`corne_left`) - Nice Nano v2 with Nice View display
2. **Right Half** (`corne_right`) - Nice Nano v2 with Nice View display  
3. **Central Dongle** (`corne_dongle`) - Nice Nano v2 with SSH1106 OLED display
4. **Settings Reset** (`settings_reset`) - For clearing paired devices

## Dongle Configuration

The central dongle configuration (`corne_dongle`) provides:

- **Central role**: Acts as the central device that both keyboard halves connect to
- **USB HID output**: Provides keyboard input to the connected computer via USB
- **SSH1106 OLED display**: 128x64 pixel display (1.3" compatible) connected via I2C
  - SDA on pin P0.17 (pin 17)
  - SCL on pin P0.20 (pin 20)
- **Advanced display widgets**: Battery status, layer indicators, Bongo Cat animation, modifiers, and HID indicators
- **Bluetooth connectivity**: Connects to both keyboard halves wirelessly
- **Identical keymap**: Same keymap as the split halves for consistent behavior

### Display Features
The dongle integrates with the `zmk-dongle-display` module to provide:
- **Battery Status**: Shows battery levels for both dongle and connected halves
- **Layer Status**: Visual indication of active keyboard layer
- **Modifier Status**: Shows active modifier keys (Shift, Ctrl, Alt, etc.)
- **Bongo Cat Animation**: Animated typing indicator
- **HID Indicators**: Caps Lock, Num Lock, and Scroll Lock status

## Hardware Requirements

### For the Dongle:
- Nice Nano v2 controller
- SSH1106 OLED display (128x64) - 1.3 inch compatible
- Connections:
  - Display SDA → Pin 17 (P0.17)  
  - Display SCL → Pin 20 (P0.20)
  - Display VCC → 3.3V
  - Display GND → GND

### For the Keyboard Halves:
- Nice Nano v2 controllers (left and right)
- Nice View displays with adapters
- Standard Corne handwired construction

## Usage

1. **Build the firmware** using the GitHub Actions workflow
2. **Flash the dongle** with the `corne_dongle` firmware
3. **Flash the halves** with `corne_left` and `corne_right` firmware
4. **Connect the dongle** to your computer via USB
5. **Power on the keyboard halves** - they should automatically connect to the dongle
6. **Use the keyboard** - all input will be transmitted through the dongle to the computer

## Keymap

The keymap includes:
- Base layer with QWERTY layout and homerow mods
- Lower layer with numbers and symbols
- Raise layer with function keys and media controls
- Additional layers for special functions

The keymap is identical across all build targets to ensure consistent behavior.

### Keymap Drawer

![Keymap Drawer](assets/corne.keymap.png)

The above keymap drawer represents the current configuration and preserves the original color scheme as referenced.

## Configuration Files

- `build.yaml` - Defines build targets for GitHub Actions
- `config/corne.conf` - Configuration for split halves
- `config/corne.keymap` - Keymap for split halves
- `config/corne_dongle.conf` - Configuration for central dongle
- `config/corne_dongle.keymap` - Keymap for central dongle (identical to split)
- `config/boards/shields/corne_dongle/` - Shield definition for dongle hardware

## Building

Firmware builds automatically via GitHub Actions when changes are pushed. The workflow requires approval for forked repositories.

Artifacts will be available for download after successful builds:
- `corne_left-nice_nano_v2-zmk.uf2`
- `corne_right-nice_nano_v2-zmk.uf2`  
- `corne_dongle-nice_nano_v2-zmk.uf2`
- `settings_reset-nice_nano_v2-zmk.uf2`

## Keymap PNG Generation

A GitHub Actions workflow automatically generates a PNG visual of the keymap whenever `config/corne.keymap` is modified. The workflow can also be triggered manually from the Actions tab.

### Using the Workflow

1. **Automatic Generation**: Push changes to `config/corne.keymap` to trigger the workflow
2. **Manual Generation**: Go to the Actions tab → "Generate Keymap PNG" → "Run workflow"
3. **Download**: After the workflow completes, download the `keymap-png` artifact containing `corne.keymap.png`

### Running Locally

To generate the keymap PNG on your local machine:

1. Install dependencies:
   ```bash
   pip install keymap-drawer
   sudo apt-get install inkscape  # For SVG to PNG conversion (Ubuntu/Debian)
   # OR: brew install inkscape    # For macOS
   ```

2. Create a config file for the background color:
   ```bash
   cat > keymap-config.yaml << EOF
   draw_config:
     svg_extra_style: |
       svg { background-color: #b8a78b; }
   EOF
   ```

3. Generate the PNG:
   ```bash
   python -m keymap_drawer -c keymap-config.yaml parse -z config/corne.keymap | \
   python -m keymap_drawer -c keymap-config.yaml draw -z corne -o config/corne.keymap.svg -
   inkscape --export-type=png --export-dpi=300 config/corne.keymap.svg -o config/corne.keymap.png
   ```

