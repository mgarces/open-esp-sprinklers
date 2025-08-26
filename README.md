# ESP32-C3 Sprinkler Controller

An intelligent, automated sprinkler system controller built with ESPHome for Home Assistant integration. Features persistent configuration storage, flexible scheduling, and both manual and automatic operation modes.

## 🌟 Features

### Core Functionality
- **3-Zone Control**: Independent control of up to 3 sprinkler zones
- **Persistent Storage**: All settings survive power outages and reboots
- **Flexible Scheduling**: Daily, weekday, weekend, or custom day patterns
- **Home Assistant Integration**: Full integration with HA dashboard and automations
- **Manual Override**: Physical button and web interface control
- **Safety Features**: Automatic shutdowns and zone interlocking

### Smart Scheduling
- **28 Runtime Options**: From 10 seconds to 60 minutes per zone
- **Multiple Schedule Patterns**: Daily, weekdays, weekends, specific days, or custom patterns
- **Time Precision**: Hour and minute control for watering times
- **Schedule Enable/Disable**: Easy on/off toggle without losing settings

### User Interface
- **Web Dashboard**: Built-in web server for local control
- **Home Assistant Cards**: Native HA integration with custom entities
- **Physical Button**: On-device control with short/long press actions
- **Real-time Status**: Live countdown timers and zone status

## 🛠️ Hardware Requirements

### Main Components
- **ESP32-C3 Development Board** (ESP32-C3-DevKitM-1)
- **3-Channel Relay Module** (5V, with optocouplers)
- **Push Button** (for manual control)
- **Power Supply** (5V for relays, 3.3V for ESP32-C3)

### Connections
```
ESP32-C3 Pin | Component
-------------|----------
GPIO 2       | Relay 1 (Zone 1)
GPIO 3       | Relay 2 (Zone 2) 
GPIO 4       | Relay 3 (Zone 3)
GPIO 5       | Push Button (with pullup)
3.3V         | ESP32-C3 Power
5V           | Relay Module VCC
GND          | Common Ground
```

### Wiring Diagram
```
[ESP32-C3] -----> [3-Channel Relay] -----> [Solenoid Valves]
    |
    └-----> [Push Button] -----> [GND]
```

## 📋 Installation

### Prerequisites
- [ESPHome](https://esphome.io/) installed
- Home Assistant (optional but recommended)
- WiFi network credentials

### Setup Steps

1. **Clone this repository**
   ```bash
   git clone https://github.com/yourusername/esp32c3-sprinkler-controller.git
   cd esp32c3-sprinkler-controller
   ```

2. **Create secrets.yaml file**
   ```yaml
   # secrets.yaml
   wifi_ssid: "YourWiFiName"
   wifi_password: "YourWiFiPassword"
   ip_sprinklers: "192.168.1.100"  # Static IP for controller
   api_encryption_sprinklers: "your-32-character-api-key"
   ota_password_sprinklers: "your-ota-password"
   ```

3. **Compile and upload**
   ```bash
   esphome compile ESP32C3\ Sprinkler\ \(1\).yaml
   esphome upload ESP32C3\ Sprinkler\ \(1\).yaml
   ```

4. **Add to Home Assistant**
   - Go to Settings > Devices & Services
   - Click "Add Integration" 
   - Select "ESPHome"
   - Enter the device IP address

## 🎛️ Configuration

### Zone Runtime Settings
Each zone can be configured with one of 28 runtime options:
- **Short cycles**: 10s, 30s, 1min, 1.5min, 2min, 2.5min
- **Medium cycles**: 3min to 10min (30-second increments)
- **Long cycles**: 12min to 60min (various increments)

### Schedule Configuration
- **Hour**: 00-23 (24-hour format)
- **Minutes**: 00-59 (1-minute precision)
- **Days**: Multiple patterns available
  - Daily
  - Weekdays (Mon-Fri)
  - Weekends (Sat-Sun)
  - Specific days (Monday, Tuesday, etc.)
  - Custom patterns (Mon/Wed/Fri, Tue/Thu/Sat)
  - Disabled

### Safety Features
- **Zone Interlocking**: Only one zone active at a time
- **Daily Shutdowns**: Automatic stop at 23:30 and 00:00
- **Power-on State**: All zones default to OFF after restart
- **Persistent Settings**: Configuration survives power outages

## 🔧 Usage

### Manual Control

#### Physical Button
- **Short Press**: Cycle through zones (Zone 1 → Zone 2 → Zone 3 → Off)
- **Long Press**: Start/stop full automatic cycle

#### Home Assistant
- Individual zone switches for manual control
- "RUN ALL ZONES" button for automatic sequence
- "STOP ALL" button for emergency stop
- Configuration dropdowns for all settings

#### Web Interface
Access the built-in web server at `http://192.168.1.100` (or your configured IP) for:
- Manual zone control
- Configuration changes
- Real-time status monitoring
- Log viewing

### Automatic Operation

The system automatically runs the configured sequence when:
1. Schedule is enabled
2. Current time matches configured time
3. Current day matches configured pattern
4. No zones are currently running

### Monitoring

#### Available Sensors
- **Sprinkler Status**: Current operation mode and active zone
- **Timer Remaining**: Countdown timer in seconds
- **Sprinkler Configuration**: Summary of all settings

#### Status Messages
- `Idle`: No zones active
- `Zone X Manual`: Manual zone operation
- `Zone X Auto`: Automatic cycle in progress
- `Cycle Starting...`: Transition between zones

## 📱 Home Assistant Integration

### Entities Created
- **Switches**: Individual zone controls + schedule enable/disable
- **Buttons**: Run all zones, stop all zones
- **Selects**: Zone runtimes, schedule time, schedule days
- **Sensors**: Timer remaining, status, configuration summary

### Sample Lovelace Card
```yaml
type: entities
title: Sprinkler Controller
entities:
  - entity: switch.sprinkler_esp32c3_schedule_enabled
  - entity: button.sprinkler_esp32c3_run_all_zones
  - entity: button.sprinkler_esp32c3_stop_all
  - entity: sensor.sprinkler_esp32c3_sprinkler_status
  - entity: sensor.sprinkler_esp32c3_timer_remaining
  - type: divider
  - entity: select.sprinkler_esp32c3_schedule_hour
  - entity: select.sprinkler_esp32c3_schedule_minutes  
  - entity: select.sprinkler_esp32c3_schedule_days
  - type: divider
  - entity: select.sprinkler_esp32c3_zone_1_runtime
  - entity: select.sprinkler_esp32c3_zone_2_runtime
  - entity: select.sprinkler_esp32c3_zone_3_runtime
```

## 🔍 Troubleshooting

### Common Issues

#### Device Won't Connect to WiFi
- Verify credentials in `secrets.yaml`
- Check if AP mode is activated (`esp32-ap-recovery`)
- Ensure IP address doesn't conflict with other devices

#### Zones Don't Turn Off
- Check relay wiring (ensure inverted logic is correct)
- Verify power supply stability
- Review ESPHome logs for timeout messages

#### Schedule Not Triggering
- Confirm time synchronization: Check logs for "Time synchronized"
- Verify schedule is enabled: `Schedule Enabled` switch should be ON
- Check day pattern matches current day
- Ensure no zones are manually running

#### Settings Not Persisting
- Check flash write operations in logs
- Verify `preferences` component configuration
- Ensure stable power supply during writes

### Log Analysis
Enable verbose logging by adding to your YAML:
```yaml
logger:
  level: DEBUG
  logs:
    schedule: DEBUG
    config: DEBUG
    boot: DEBUG
```

### Recovery Mode
If the device becomes unresponsive:
1. Device creates AP: `esp32-ap-recovery`
2. Connect with password from `secrets.yaml`
3. Access web interface at `192.168.100.1`
4. Reconfigure WiFi settings

## 🔧 Customization

### Adding More Zones
To expand beyond 3 zones:
1. Add GPIO pin assignments
2. Create additional global variables for start times
3. Extend runtime index globals
4. Update scripts and sensors
5. Add relay switch configurations

### Custom Runtime Options
Modify the `runtime_values` vector in the lambda functions:
```cpp
std::vector<uint32_t> runtime_values = {
  10000,   // 10 seconds
  30000,   // 30 seconds  
  // Add your custom values in milliseconds
};
```

### Additional Schedule Patterns
Extend the day matching logic in the interval component to add custom patterns.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 🐛 Issues

Please report issues on the [GitHub Issues](https://github.com/yourusername/esp32c3-sprinkler-controller/issues) page.

## 📞 Support

- **Documentation**: Check this README and inline code comments
- **Community**: ESPHome Discord server
- **Issues**: GitHub Issues page for bug reports

## 🏷️ Version History

- **v1.0**: Initial release with 3-zone control, persistent storage, and scheduling
- See [CHANGELOG.md](CHANGELOG.md) for detailed version history

## 🙏 Acknowledgments

- [ESPHome](https://esphome.io/) team for the excellent framework
- Home Assistant community for integration support
- Contributors and testers

---

**⚠️ Safety Notice**: Always test the system thoroughly before leaving it unattended. Ensure proper electrical safety when working with relay modules and AC-powered solenoids.
