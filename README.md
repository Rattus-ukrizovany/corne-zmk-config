# 🌱 Corne ZMK Config with Dongle Support

This repository contains a ZMK configuration for the Corne keyboard—supporting both split halves and a central dongle setup.

---

## 🚦 Build Targets

| Target          | Device/Display            | Description                |
|-----------------|--------------------------|----------------------------|
| **corne_left**  | Nice Nano v2 + Nice View | Left half                  |
| **corne_right** | Nice Nano v2 + Nice View | Right half                 |
| **corne_dongle**| Nice Nano v2 + SSH1106   | Central dongle (USB HID)   |
| **settings_reset** | -                      | Clears paired devices      |

---

## 🖥️ Dongle Configuration

The central dongle (`corne_dongle`) acts as the hub for both halves.

- **Central role**: Keyboard halves connect wirelessly via Bluetooth
- **USB HID output**: Keyboard input via USB to PC
- **SSH1106 OLED display**: 128x64 px (1.3" compatible), I2C:
  - SDA → P0.17 (pin 17)
  - SCL → P0.20 (pin 20)
- **Advanced widgets**: Battery, layers, Bongo Cat, modifiers, HID indicators
- **Bluetooth**: Wireless connection for both halves
- **Identical keymap**: Same layout as halves

### Display Features

Powered by `zmk-dongle-display`:
- 🔋 Battery status (dongle + halves)
- 🔄 Layer indication
- ⌨️ Modifier keys (Shift, Ctrl, Alt…)
- 🐾 Bongo Cat typing animation
- 🔢 HID indicators (Caps/Num/Scroll Lock)

---

## 🔌 Hardware Requirements

### Dongle

- Nice Nano v2 controller
- SSH1106 OLED (128x64, 1.3”)
- Connections:
  - SDA → Pin 17 (P0.17)
  - SCL → Pin 20 (P0.20)
  - VCC → 3.3V
  - GND → GND

### Keyboard Halves

- Two Nice Nano v2 controllers
- Nice View displays + adapters
- Standard Corne handwired build

---

## 📝 Usage

1. **Build firmware** (GitHub Actions workflow)
2. **Flash dongle** with `corne_dongle` firmware
3. **Flash halves** with `corne_left` and `corne_right` firmware
4. **Connect dongle** to your computer via USB
5. **Power on halves**—they’ll auto-connect to dongle
6. **Enjoy typing!** Input transmits through dongle

---

## ⌨️ Keymap

- QWERTY base with homerow mods
- Lower: Numbers + symbols
- Raise: F-keys + media
- Extra: Special function layers
- **Identical keymap for all devices!**

### 🖼️ Keymap Drawer

Latest PNG here:
![Keymap Drawer](keymap/corne.keymap.png)

---

## 📥 Download Latest Firmware & Keymap

After each build, find the latest files here:

- 🟦 [Corne Left Firmware](firmware/corne_left%20nice_view_adapter%20nice_nano_v2-zmk.uf2)
- 🟩 [Corne Right Firmware](firmware/corne_right%20nice_view_adapter%20nice_nano_v2-zmk.uf2)
- 🟨 [Dongle Firmware](firmware/corne_dongle%20dongle_display-nice_nano_v2-zmk.uf2)
- 🧹 [Settings Reset Firmware](firmware/settings_reset-nice_nano_v2-zmk.uf2)
- 🖼️ [Keymap PNG](keymap/corne.keymap.png)

_Just click to download!_

---

## ⚙️ Configuration Files

| File/Folder                                  | Purpose/Contents                       |
|-----------------------------------------------|----------------------------------------|
| `build.yaml`                                 | Defines build targets (GitHub Actions) |
| `config/corne.conf`                          | Split halves config                    |
| `config/corne.keymap`                        | Keymap for split halves                |
| `config/corne_dongle.conf`                   | Dongle config                          |
| `config/corne_dongle.keymap`                 | Dongle keymap (identical to halves)    |
| `config/boards/shields/corne_dongle/`        | Dongle hardware shield definition      |

---

## 🛠️ Building & Artifacts

- Firmware builds automatically on push (GitHub Actions)
- Forks: workflow needs approval
- Download artifacts after each build:
  - `corne_left-nice_nano_v2-zmk.uf2`
  - `corne_right-nice_nano_v2-zmk.uf2`
  - `corne_dongle-nice_nano_v2-zmk.uf2`
  - `settings_reset-nice_nano_v2-zmk.uf2`

---

## 🖼️ Keymap PNG Generation

A workflow auto-generates PNG whenever `config/corne.keymap` changes—or trigger manually from Actions.

### Local Generation

1. **Install dependencies:**
   ```bash
   pip install keymap-drawer
   sudo apt-get install inkscape      # Ubuntu/Debian
   # OR: brew install inkscape        # macOS
   ```
2. **Create config for background color:**
   ```bash
   cat > keymap-config.yaml << EOF
   draw_config:
     svg_extra_style: |
       svg { background-color: #b8a78b; }
   EOF
   ```
3. **Generate PNG:**
   ```bash
   python -m keymap_drawer -c keymap-config.yaml parse -z config/corne.keymap | \
   python -m keymap_drawer -c keymap-config.yaml draw -z corne -o config/corne.keymap.svg -
   inkscape --export-type=png --export-dpi=300 config/corne.keymap.svg -o config/corne.keymap.png
   ```
---

## ⚙️ Technical Implementation & Major Differences

This configuration implements a **central dongle architecture** that differs from standard split keyboards in several key ways:

### 🔗 Architecture Overview

```
[Left Half] ──┐
              │ Bluetooth
              ├─→ [Central Dongle] ──USB──→ [Computer]
              │     ↳ SSH1106 Display
[Right Half] ──┘
```

### 🔧 Key Differences from Standard Corne

| Aspect | Standard Corne | This Dongle Setup |
|--------|----------------|-------------------|
| **Connection** | Direct USB from one half | Wireless to central dongle |
| **Display** | SSD1306 (32px height) on halves | SSH1106 (64px height) on dongle |
| **Matrix Scanning** | Physical GPIO matrix per half | Composite kscan (wireless input) |
| **Power** | USB powers one half, battery other | Both halves on battery, dongle USB-powered |
| **HID Output** | From connected half | From central dongle only |
| **Single-Half Mode** | ❌ Requires both halves | ✅ Works with either half alone |

### 🛠️ Wiring Compliance

This configuration **follows foostan Corne wiring standards**:
- ✅ Matrix layout: 4 rows × 12 columns (standard Corne)
- ✅ Transform mapping: Compatible with foostan/corne layouts
- ✅ Physical layout: Standard 6-column Corne physical arrangement
- ✅ Pin assignments: Standard row/col GPIO usage for halves

### 📡 Bluetooth Configuration

- **Central Device**: Dongle scans for and connects to keyboard halves
- **Max Connections**: 3 (dongle + 2 halves)
- **High TX Power**: `CONFIG_BT_CTLR_TX_PWR_PLUS_8=y` for better range
- **Battery Monitoring**: Central fetches battery levels from both halves

### 🖥️ Display Differences

| Feature | Standard (SSD1306) | Dongle (SSH1106) |
|---------|-------------------|------------------|
| **Resolution** | 128×32 | 128×64 |
| **I2C Address** | 0x3C | 0x3C |
| **Widgets** | Basic status | Enhanced: Bongo Cat, modifiers, HID indicators |
| **Battery Info** | Local only | Dongle + both halves |

### 🔌 Hardware Pin Usage

**Dongle (Nice Nano v2)**:
- Pin 17 (P0.17): I2C SDA → SSH1106 Display (128x64)
- Pin 20 (P0.20): I2C SCL → SSH1106 Display (128x64)  
- No physical matrix pins (receives wireless input)

**Keyboard Halves**: Standard Corne wiring (unchanged)

### ⚖️ Trade-offs

**Advantages**:
- ✅ Works with single half (great for troubleshooting)
- ✅ Larger display with more information
- ✅ USB connection always available (no half switching)
- ✅ Better power management (halves can sleep independently)

**Considerations**:
- ⚠️ Additional device required (dongle)
- ⚠️ Slightly more complex setup
- ⚠️ Bluetooth latency (minimal for typing)

---

## 🔧 Troubleshooting

### Common Issues

**🔸 Dongle not connecting to halves**
- Flash `settings_reset` firmware to all devices to clear pairing
- Ensure all devices are powered on and in pairing mode
- Check that dongle shows as "Corne Dongle" in Bluetooth settings

**🔸 Display not working**
- Verify SSH1106 wiring: SDA→Pin 17, SCL→Pin 20, VCC→3.3V, GND→GND
- Check I2C address (should be 0x3C)
- Ensure display is SSH1106 compatible (not SSD1306)

**🔸 Single half not working**
- This is normal behavior - dongle requires at least one half to be connected
- If neither half works, check Bluetooth pairing and battery levels
- Use `settings_reset` firmware if connection issues persist

**🔸 Keys not registering**
- Check battery levels on halves (displayed on dongle screen)
- Verify halves are properly paired to dongle (not to computer directly)
- Ensure keymap is flashed to all devices after changes

### Testing Single-Half Operation

1. **Power on dongle** (connects to computer via USB)
2. **Power on right half only** → Should work normally
3. **Power off right half, power on left half** → Should switch and work
4. **Power on both halves** → Full functionality

### Hardware Validation

- **Dongle LED**: Should show activity when halves connect
- **Display**: Should show battery, layer, and modifier info
- **USB Connection**: Computer should detect "Corne Dongle" as HID device

---

## 🙌 Community & Related Projects

- **YADS Dongle Maker**: This repo ([janpfischer/zmk-dongle-screen](https://github.com/janpfischer/zmk-dongle-screen)) is "Yet Another Dongle Screen" (YADS) by @janpfischer—an advanced, modular ZMK dongle display project for the Seeeduino XIAO BLE and Nice!Nano v2.
- **ZMK Community**: Find support, discussion, and resources at the [ZMK Community](https://zmk.dev/community/).
- **@englmaxi/zmk-dongle-display**: The [zmk-dongle-display](https://github.com/englmaxi/zmk-dongle-display) project provides additional widgets and OLED support for ZMK dongle builds.
  
---


