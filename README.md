# Open ESP Sprinklers
This is a simple, powerful, and reliable 8-zone sprinkler controller powered by an ESP32 and the [ESPHome](https://esphome.io/) framework. It allows you to automate your garden or lawn watering schedule with precision and integrate it seamlessly with [Home Assistant](https://www.home-assistant.io/).

The system is designed to be "set and forget." Once configured and installed, it will run its schedule autonomously without needing a constant connection to Home Assistant.

## Features

  * **Independent Zones:** Controls up to 8 separate sprinkler valves (using this relay, you can extend this off course).
  * **Automated Watering Schedule:** A fully customizable watering sequence that runs automatically at a set time.
  * **Safety First (Mutual Exclusion):** The logic ensures that only **one zone can be active at a time**, preventing low water pressure and protecting your pump or water line.
  * **Failsafe Shutdown:** Includes a secondary daily trigger that guarantees all valves are turned off, preventing water wa$te (ask me how I know) in case of a system error or reboot.
  * **Easy Integration:** Connects directly to Home Assistant for manual control, status monitoring, and easy configuration.
  * **Status LED:** An onboard LED provides a visual indicator when any zone is active.
  * **Simple Configuration:** Key settings like watering duration and schedule time are easily editable in a single file.

## How It Works

The ESP32 connects to your local Wi-Fi and synchronizes its internal clock with an internet time server. It then waits for the scheduled time to arrive.

1.  **Watering Cycle Starts:** At the scheduled time (e.g., 7:30 PM), the device begins the watering sequence.
2.  **Sequential Operation:** It turns on Zone 1, waits for a set duration, then turns on Zone 2 (which automatically turns off Zone 1), and so on for all defined zones.
3.  **Cycle Ends:** After the last zone has run, the system turns everything off.
4.  **Safety Check:** Later in the day (e.g., 11:30 PM), the failsafe trigger runs to ensure all valves are closed.

## Configuration

All configuration is done in the `sprinkler-module.yaml` file.

### 1\. Set the Watering Duration

To change how long each zone stays on, edit the `running_time` variable at the top of the file under `substitutions`:

```yaml
substitutions:
  running_time: "15min" # Change this value (e.g., "10min", "30min", "900s")
```

### 2\. Adjust the Schedule

To change the time when the watering cycle starts, find the appropriate value for minutes, hours and days in `substitutions`:

```yaml
substitutions:
...
  # define at what time you want the sprinklers to start
  start_time_minutes: "30"
  start_time_hour: "19"
  start_time_days_of_week: "*" # '*' means every day. You can use 'MON,WED,FRI' for specific days.
...
```

### 3\. Set Your Time Zone

To ensure the schedule runs on your local time, set the correct `timezone` string. The current configuration is set for Portugal. You can find a list of valid POSIX timezone strings [here](https://github.com/nayarsystems/posix_tz_db/blob/master/zones.csv).

```yaml
time:
  - platform: sntp
    id: "auto_timer_off"
    timezone: "WET0WEST,M3.5.0/1,M10.5.0" # <-- Change this to your local timezone
```

After making any changes, upload the new configuration to your ESP32 using the ESPHome dashboard.

# Hardware
For now, this as only been tested using the following
- ESP32 (NodeMCU ESP32) - [example](https://mauser.pt/catalog/product_info.php?products_id=096-7620)
- 8 Relay Module - [example](https://mauser.pt/catalog/product_info.php?products_id=096-8203)

I set out to build this after successfully automating my garden sprinklers using three Shelly 1 devices. They work perfectly, but installation is messy, and managing three different Wi-Fi devices on your network can be cumbersome. With this solution, you only need one device on your network.

# Hardware diagram
![esp32-relay](https://github.com/mgarces/open-esp-sprinklers/assets/126121/2a74c420-d607-4861-bc3f-4a23bc8566ce)

Safe GPIO to use:
`33`, `32` `27`, `26`, `25`, `23`, `22`, `21`, `19`, `17`, `16`, `13` and `4`

Refer to:
[ESP32 GPIO reference pinout](https://randomnerdtutorials.com/esp32-pinout-reference-gpios/)
# Valves Diagram 
to do

# Flashing your ESP32
to do 
```
esptool.py --chip esp32 -p /dev/tty.usbserial-0001 write_flash 0x0 sprinkler-module-factory.bin
```

# 3D Case
to do but feel free to contribute, since I'm still learning 3D CAD design (Fusion360, OpenCAD, etc)
