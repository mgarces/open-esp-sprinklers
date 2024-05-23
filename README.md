# Open ESP Sprinklers
This small project aims to build an automated watering system, using simple accessible hardware and combining it with [HomeAssistant](https://www.home-assistant.io/).

We also take advantage of the amazing [ESPHome](https://esphome.io/) project and it's clean integration with HomeAssistant.

# Hardware
For now, this as only been tested using the following
- ESP32 (NodeMCU ESP32) - [example](https://mauser.pt/catalog/product_info.php?products_id=096-7620)
- 8 Relay Module - [example](https://mauser.pt/catalog/product_info.php?products_id=096-8203)

I've set out to build this, after sucessufully automating my own garden sprinklers using 3 Shelly 1; they are great, and work perfectly, but it's way messier to install 
and also 3 different Wi-Fi devices in your network to manage. With this solution, you only have 1 device in your network. 

# Hardware diagram
to do

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
