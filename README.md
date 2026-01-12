Forked from https://github.com/agillis/esphome-modular-lvgl-buttons

## Dev setup
### SDL setup 
[More details here](https://community.home-assistant.io/t/how-to-virtual-esphome-device-and-development-using-windows-work-in-progress/802669/6)  

Install 
- vscode & WSL extension
- SDL 2
```Shell
sudo apt-get update
sudo apt install libsdl2-dev libsodium-dev build-essential git
```

- Install python and venv
```Shell
sudo apt-get update
sudo apt-get install libpython3-dev
sudo apt-get install python3-venv
python3 -m venv vesphome
```

- Start venv
```Shell
source vesphome/bin/activate
```
- Install esphome
```Shell
cd vesphome
pip3 install wheel
pip3 install esphome
```

- Network setup for SDL to connect to HA.  Replace `x.x.x.x` with WSL ip
```PowerShell
netsh interface portproxy add v4tov4 listenport=6053 listenaddress=0.0.0.0 connectport=6053 connectaddress=x.x.x.x
New-NetFirewallRule -DisplayName "WSL Port 6053" -Direction Inbound -LocalPort 6053 -Protocol TCP -Action Allow
```

Compile
```Shell
esphome compile waveshare-esp32-p4-wifi6-touch-lcd-7b.yaml 
```

Run
```Shell
esphome run waveshare-esp32-p4-wifi6-touch-lcd-7b.yaml --device x.x.x.x
```

Clean
```Shell
esphome clean waveshare-esp32-p4-wifi6-touch-lcd-7b.yaml 
```
### WSL setup
Push config from WSL\
Download - https://github.com/dorssel/usbipd-win/releases

List USB device ID's (PS)
```PowerShell
usbipd list
```

Bind esp32 device by ID (PS)
```PowerShell
usbipd bind --busid <busid>
```
Attached to WSL (PS)
```PowerShell
usbipd attach --wsl --busid <busid>
```

List device (WSL)
```Shell
lsusb
```

Detach (PS)
```PowerShell
usbipd detach --busid <busid>
```

Push via USB (WSL)
```Shell
esphome run waveshare-esp32-p4-wifi6-touch-lcd-7b_display_modular.yaml --device /dev/ttyACM0
```

Update esphome
```Shell
pip3 install esphome --upgrade
```

## Waveshare esp32-p4-wifi6-touch-lcd-7b Reference docs
[Wiki](https://www.waveshare.com/wiki/ESP32-P4-WIFI6-Touch-LCD-7B?srsltid=AfmBOoo9Y7EjqQdv_sgnoF6c126Rnp5BGZYRx9xw3hhQHx_hje2eVhM6)  
[Schematic](https://files.waveshare.com/wiki/ESP32-P4-WIFI6-Touch-LCD-7B/ESP32-P4-WIFI6-Touch-LCD-7B.pdf)  
[Datasheet](https://files.waveshare.com/wiki/common/Esp32-p4_datasheet_en.pdf)  
[Github](https://github.com/waveshareteam/ESP32-P4-WIFI6-Touch-LCD-7B)  

## TODO
- General
  - [ ] Code cleanup
  - [ ] Fix backlight timing
- Theme/styles
- Media player widget
  - [ ] Album Art
  - [x] Power on
  - [ ] Previous
  - [ ] Skip
  - [ ] Play/pause
- Add battery charge widget
  - Battery voltage: GPIO20
    - ```YAML
      attenuation: 12dB
      samples: 10
      filters:
        - multiply: 3.0
      ```
  - Max charge ~4.1v
  - Min charge ~3.25
    - Drop off from 3.25 @ 10:06:32am
    - Screen died 2.54 @ 10:23am
- Weather
  - Update HA weather template to fix hourly icons when partyly cloudy with sun/moon
  - daily/hourly_forecast
    - Update styles
  - current_conditions/weather_status_widget
    - Make more modular
    - When single widget is clicked, display graph with historical values
  - 

