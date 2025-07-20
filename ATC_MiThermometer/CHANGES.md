# TelinkMiFlasher.html Enhancement Changes

## Version 11.4 - MAC Address and Device Name Prediction Enhancements

### New Features

#### 1. MAC Address Display
- **Enhanced Connection Logging**: Shows MAC addresses during device connection
  - Format: "Connecting to: LYWSD03MMC (MAC: A4:C1:38:C9:26:47)"
  - Works with devices that support MAC address reading via Web Bluetooth API

#### 2. Enhanced Advertisement MAC Logging
- **Improved MAC Address Capture**: Stores MAC addresses from advertisement data for later use
- **Multiple Format Support**: Handles MAC addresses from various advertisement formats:
  - Mi protocol advertisements
  - PVVX custom firmware advertisements  
  - ATC1441 firmware advertisements
  - CGG1 "Goose" advertisements
  - Qingping/ClearGrass advertisements

#### 3. Device Name Change Predictions
- **Firmware Type Tracking**: Added tracking for 4 firmware types:
  - `custom`: Custom ATC firmware
  - `zigbee`: Zigbee protocol firmware
  - `zb`: Zigbee trial firmware
  - `original`: Stock firmware

- **ATC Name Generation**: Automatically generates ATC_XXXXXX names from MAC addresses
  - Algorithm: Takes last 6 characters of MAC address
  - Example: MAC `A4:C1:38:C9:26:47` → ATC name `ATC_C92647`

- **Prediction Messages**: Shows predictions when flashing starts:
  - **Custom**: "Device will be renamed to: ATC_C92647 after flashing custom firmware"
  - **Zigbee**: "Device will be converted to Zigbee protocol after flashing"
  - **ZB**: "Device will be converted to Zigbee protocol (trial firmware) after flashing"
  - **Original**: "Device will keep original LYWSD03MMC name after flashing stock firmware"

### Technical Implementation

#### New Helper Functions
```javascript
// Generate ATC name from MAC address
function generateATCName(macAddress) {
    if (!macAddress) return null;
    var cleanMac = macAddress.replace(/:/g, '').toUpperCase();
    var lastThreeBytes = cleanMac.slice(-6);
    return "ATC_" + lastThreeBytes;
}

// Get/set device MAC address
function getDeviceMAC() { return deviceMAC; }
function setDeviceMAC(mac) { deviceMAC = mac; }

// Set current firmware type
function setCurrentFirmwareType(type) { currentFirmwareType = type; }
```

#### Enhanced Functions
- **Firmware Loading Functions**: Now set firmware type when loading:
  - `FirmwareUpgrade()` → sets `'custom'`
  - `FirmwareZigbee()` → sets `'zigbee'`
  - `FirmwareZB()` → sets `'zb'`
  - `BackToOriginal()` → sets `'original'`

- **updateBegin()**: Enhanced to show predictions when flashing starts
- **resetVariables()**: Enhanced to reset MAC address and firmware type tracking

### Backward Compatibility
- All existing functionality remains unchanged
- MAC address display is optional and gracefully handles missing MAC addresses
- Predictions only appear when flashing starts, not during connection
- Graceful handling of null/empty MAC addresses

### Browser Requirements
- Requires Web Bluetooth API support
- MAC address reading requires experimental web platform features in some browsers
- Chrome/Edge/Opera: Enable `#enable-experimental-web-platform-features` flag

### Example Output

#### Connection Logging
```
10:30:18: Connecting to: LYWSD03MMC (MAC: A4:C1:38:C9:26:47)
```

#### Advertisement Data
```
10:30:15: MAC: A4C138C92647, Bat: 85%, Vbat: 3000 mV, Temp: 22.5°C, Humi: 45.2%
```

#### Flashing Predictions
```
10:30:20: Device will be renamed to: ATC_C92647 after flashing custom firmware
10:30:20: Device will be converted to Zigbee protocol after flashing
10:30:20: Device will be converted to Zigbee protocol (trial firmware) after flashing
10:30:20: Device will keep original LYWSD03MMC name after flashing stock firmware
```

### Files Modified
- `TelinkMiFlasher.html`: Main script with enhanced logging functionality
- `CHANGES.md`: This documentation file
- `PR_DESCRIPTION.md`: Pull request description
- `EXAMPLE_OUTPUT.md`: Example output documentation