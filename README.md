# SLHA00045 - ESPHome Electric Heater Pilot Module

[![ESPHome](https://img.shields.io/badge/ESPHome-2024.12+-green.svg)](https://esphome.io/)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

A smart module that enables Home Assistant and other ESPHome-compatible home automation systems to control electric heaters via the **fil pilote** (pilot wire) system, commonly used in France and other European countries.

## 📋 Overview

This module transforms any fixed electric heater (compatible with the pilot wire system) into a smart, connected device. It mounts directly onto your heater's junction box, replacing the existing cover, and provides:

- **Smart Thermostat Control**: Full heating control through your home automation system
- **Temperature & Humidity Monitoring**: Built-in AHT20 sensor for accurate environmental readings
- **Pilot Wire Control**: Direct control of the heater's pilot wire for different heating modes
- **Safety Features**: Automatic frost protection mode on startup

## ✨ Features

- **ESP32-C6** microcontroller with Wi-Fi connectivity
- **AHT20** temperature and humidity sensor (updates every 10 seconds)
- **Pilot Wire Control**: 
  - Comfort mode (heating ON)
  - Frost protection mode (hors-gel)
- **Visual Feedback**: Green LED indicator for heating status
- **OTA Updates**: Over-the-air firmware updates
- **Captive Portal**: Easy Wi-Fi setup via fallback hotspot
- **Home Assistant Integration**: Native ESPHome API support

## 🔧 Hardware Specifications

- **Microcontroller**: ESP32-C6 (ESP32-C6-DevKitC-1)
- **Sensor**: AHT20 (Temperature & Humidity)
- **GPIO Configuration**:
  - GPIO1: Green LED
  - GPIO6: I2C SDA (AHT20)
  - GPIO7: I2C SCL (AHT20)
  - GPIO10: ESPHome status LED
  - GPIO22: Pilot Wire Negative (N)
  - GPIO23: Pilot Wire Positive (P)

## 📦 Installation

### Prerequisites

- ESPHome installed (via Home Assistant add-on or standalone)
- Home Assistant or compatible home automation system
- Basic electrical knowledge (⚠️ **High voltage warning**)

### Step 1: Flash the Firmware

#### Option A: Using ESP Web Tools (Recommended)

1. Visit the [GitHub Pages deployment](https://sitronlabs.github.io/ESPHome-Electric-Heater-Pilot/) (if available)
2. Connect your module via USB
3. Click "Connect" and select your device
4. Follow the on-screen instructions

#### Option B: Using ESPHome Dashboard

1. Open ESPHome Dashboard
2. Click "New Device" → "Continue"
3. Enter a name (e.g., `slha00045`)
4. Select "ESP32-C6-DevKitC-1" as the board
5. Copy the configuration from `slha00045.yaml`
6. Compile and flash

#### Option C: Using esphome CLI

```bash
esphome compile slha00045.yaml
esphome upload slha00045.yaml
```

### Step 2: Configure Wi-Fi

On first boot, the device will create a fallback hotspot:
- **SSID**: `SLHA00045 Fallback Hotspot`
- **Password**: (configured in secrets)

Connect to this hotspot and use the captive portal to configure your Wi-Fi credentials.

Alternatively, use the ESPHome Improv feature via serial or Bluetooth LE.

### Step 3: Add to Home Assistant

1. In Home Assistant, go to **Settings** → **Devices & Services**
2. Click **Add Integration**
3. Search for **ESPHome**
4. The device should auto-discover, or enter its IP address
5. Enter the encryption key (if configured)

## ⚙️ Configuration

### Basic Configuration

The main configuration file is `slha00045.yaml`. Key sections:

- **Wi-Fi**: Configure your network credentials
- **API**: Home Assistant integration settings
- **OTA**: Over-the-air update settings
- **Sensors**: AHT20 temperature and humidity
- **Climate**: Thermostat control logic

### Customization

#### Adjusting Temperature Update Interval

```yaml
sensor:
  - platform: aht10
    variant: AHT20
    update_interval: 10s  # Change this value
```

#### Modifying Thermostat Behavior

```yaml
climate:
  - platform: thermostat
    heat_deadband: 0.1      # Temperature difference before switching
    heat_overrun: 0.1       # Temperature overshoot tolerance
```

#### Changing LED Behavior

The green LED (GPIO1) can be customized in the `heat_action` and `idle_action` sections.

### Secrets File

Create a `secrets.yaml` file (not tracked in git) with:

```yaml
wifi_ssid: "YourWiFiSSID"
wifi_password: "YourWiFiPassword"
api_encryption_key: "YourEncryptionKey"
ota_password: "YourOTAPassword"
ap_password: "YourHotspotPassword"
```

## 🏠 Usage in Home Assistant

Once integrated, the module appears as:

### Climate Entity
- **Entity**: `climate.thermostat`
- **Control**: Set target temperature
- **Modes**: 
  - **Heat**: Comfort mode (full heating)
  - **Off**: Frost protection mode

### Sensors
- **Temperature**: `sensor.temperature` (in °C)
- **Humidity**: `sensor.humidity` (in %)

### Example Automation

```yaml
automation:
  - alias: "Heater Auto Control"
    trigger:
      - platform: numeric_state
        entity_id: sensor.temperature
        below: 19
    action:
      - service: climate.set_temperature
        target:
          entity_id: climate.thermostat
        data:
          temperature: 20
```

## 🔌 Physical Installation

⚠️ **CRITICAL SAFETY WARNING**: This module works with **230V AC mains voltage**. Installation must only be performed by qualified personnel with proper electrical knowledge.

### Installation Steps

1. **Power Off**: Turn off the circuit breaker for the heater
2. **Verify**: Use a voltage tester to confirm no power is present
3. **Remove Cover**: Unscrew the existing junction box cover
4. **Mount Module**: 
   - Position the module over the junction box
   - Use the existing screws (same as the original cover)
   - Ensure proper wire routing
5. **Connect Wires**:
   - Connect phase (L) and neutral (N) from the heater
   - Connect the pilot wire to the module's pilot wire terminals
6. **Power On**: Restore power and verify operation

### Pilot Wire Modes

The module controls the heater through the pilot wire:

- **Comfort Mode**: Both positive and negative half-cycles cut → Full heating
- **Frost Protection**: Only negative half-cycle → Minimal heating (hors-gel)

## 🔄 Firmware Updates

### Automatic Updates (via Home Assistant)

If configured with `http_request` OTA, updates can be pulled automatically from GitHub releases.

### Manual OTA Update

```bash
esphome upload slha00045.yaml
```

Or use the ESPHome Dashboard's "Install" button.

## 🐛 Troubleshooting

### Device Not Connecting to Wi-Fi

- Check credentials in `secrets.yaml`
- Use the fallback hotspot to reconfigure
- Verify Wi-Fi signal strength at installation location

### Temperature Readings Incorrect

- Ensure AHT20 sensor is properly connected
- Check I2C bus connections (SDA/SCL)
- Verify sensor is not obstructed

### Heater Not Responding

- Verify pilot wire connections
- Check that the heater is compatible with pilot wire control
- Test with a multimeter to confirm pilot wire signals

### OTA Updates Failing

- Ensure device is on the same network
- Check OTA password in secrets
- Verify sufficient free space on device

## 📚 Resources

- [Product Page](https://www.sitronlabs.com/store/slha00045-esphome-electric-heater-pilot-module-12)
- [ESPHome Documentation](https://esphome.io/)
- [Schematic (PDF)](https://www.sitronlabs.com/store/slha00045-esphome-electric-heater-pilot-module-12) (available on product page)

## ⚠️ Safety & Disclaimer

**IMPORTANT**: This module requires working with potentially lethal voltages (230V AC). Installation must be performed exclusively by persons with the necessary electrical qualifications. 

- Always disconnect power before installation
- Verify absence of voltage with appropriate test equipment
- When in doubt, consult a qualified electrician
- The user assumes full responsibility for installation
- This module has no certification

**Use at your own risk.**

## 🤝 Support

- **Issues**: [GitHub Issues](https://github.com/sitronlabs/ESPHome-Electric-Heater-Pilot/issues)
- **Discord**: [Sitron Labs Discord](https://discord.gg/sitronlabs)
- **Website**: [Sitron Labs](https://www.sitronlabs.com)

## 📄 License

This firmware is provided as-is. See LICENSE file for details.

---

**Manufactured by**: [Sitron Labs](https://www.sitronlabs.com)  
**Product Code**: SLHA00045
