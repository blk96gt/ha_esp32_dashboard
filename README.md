Forked from https://github.com/agillis/esphome-modular-lvgl-buttons

# Notes
## LVGL Structure
Main LVGL
```Yaml
lvgl:
  buffer_size: 100%
  pages:
    - id: main_page
      layout:
        type: grid
        multiple_widgets_per_cell: true
        grid_rows: [ fr(10), fr(18), fr(18), fr(18), fr(18), fr(18) ] # fr(90) ]
        grid_columns: [ fr(1), fr(1), fr(1), fr(1), fr(1), fr(1), fr(1) ]
      widgets:
        #######  HEADER #######
        - container:
            layout:
              type: grid
              grid_rows: [ fr(1) ]
              grid_columns: [ fr(1), fr(1), fr(1), fr(1), fr(1), fr(1), fr(1) ]
            widgets:
              - label:
                  text: "Monday, January 1"
                  text_font: nunito_18
                  grid_cell_row_pos: 0
                  grid_cell_column_pos: 0
                  grid_cell_column_span: 3
                  grid_cell_x_align: start
                  grid_cell_y_align: center
                  text_color: white
                  pad_left: 8
                  align: LEFT_MID
              - label:
                  ...
              - container:
                  bg_color: header_bg
                  grid_cell_row_pos: 0
                  grid_cell_column_pos: 4
                  grid_cell_column_span: 1
                  grid_cell_x_align: stretch
                  grid_cell_y_align: center # stretch
                  pad_all: 8
                  widgets:
                    - button:
                        widgets:
                          - label:
                              text_font: $icon_font_3
                              align: left_mid
                              id: icon_id
                              text: "\U000F1182"
                              text_color: dodgerblue
                          - label:
                              text_font: nunito_12
                              align: right_mid
                              id: label_text
                              text: "Text"
                        on_short_click:
                          - homeassistant.action:
                              action: input_button.press
                              data:
                                entity_id: "input_button.button1"
              - container:
                  postions: ....
                  widgets:
                    - button:
                        ...
                        widgets:
                          - label:
                              ...
                          - label:
                              ...
                        on_short_click:
                          - homeassistant.action:
                              action: input_button.press
                              data:
                                entity_id: "input_button.button1"
```
## Weather widgets
Add under `packages: ` section
```Yaml
  hourly_1: !include
    file: esphome-modular-lvgl-buttons/widgets/hourly_forecast.yaml
    vars:
      uid: hourly_widget
      row: 1
      column: 2
      row_span: 2
      column_span: 4
```

Structure of current `hourly_forecast` `daily_forecast` and `current_conditions` widgets
```Yaml
lvgl:
  pages:
    - id: !extend main_page
      widgets:
        - container:
            id: ${uid}
            grid_cell_row_pos: ${row}
            grid_cell_column_pos: ${column}
            grid_cell_row_span: ${row_span | default(1)}
            grid_cell_column_span: ${column_span | default(1)}
            grid_cell_x_align: stretch
            grid_cell_y_align: stretch
            bg_color: slate_blue_gray
            bg_opa: COVER
            border_width: 2
            border_color: very_dark_gray
            radius: 10
            layout:
              type: grid
              grid_rows: [ fr(1), content, fr(1), fr(1) ]
              grid_columns: [ fr(1), fr(1), fr(1), fr(1), fr(1) ]
            widgets:
              ########  Day 0  ########
              - label:
                  id: forecast_day_0
                  <<: *forcast_label_base
                  grid_cell_row_pos: 0
                  grid_cell_column_pos: 0
                  grid_cell_column_span: 1
                  text: ???
              - image:
                  id: forecast_condition_icon_0
                  <<: *forcast_image_base
                  grid_cell_row_pos: 1
                  grid_cell_column_pos: 0
                  grid_cell_column_span: 1
                  src: unknown_100
              - label:
                  id: forecast_temperature_hi_0
                  <<: *forcast_label_base
                  grid_cell_row_pos: 2
                  grid_cell_column_pos: 0
                  grid_cell_column_span: 1
                  text: "---°"
              - label:
                  id: forecast_temperature_lo_0
                  <<: *forcast_label_base_lo
                  grid_cell_row_pos: 3
                  grid_cell_column_pos: 0
                  grid_cell_column_span: 1
                  text: "---°"
              
              ########  Day 1  ########
              - label:
                  ...
              - image:
                  ...
              - label:
                  ...
              - label:
                  ...

              ########  Day 2  ########
              - label:
                  ...
```


## Battery
Battery voltage: GPIO20
```YAML
  attenuation: 12dB
  samples: 10
  filters:
    - multiply: 3.0
```
- Max charge ~4.1v
- Min charge ~3.25
  - Drop off from 3.25 @ 10:06:32am
  - Screen died 2.54 @ 10:23am


# Dev setup
## SDL setup 
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
## WSL setup
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

Push via USB (WSL)
```Shell
esphome run waveshare-esp32-p4-wifi6-touch-lcd-7b_display_modular.yaml --device /dev/ttyACM0
```

Disconnect USB or run with detach switch when finished (PS) 
```PowerShell
usbipd detach --busid <busid>
```

Update esphome
```Shell
pip3 install esphome --upgrade
```

## Waveshare esp32-p4-wifi6-touch-lcd-7b Reference docs
[Waveshare esp32-p4-wifi6-touch-lcd-7b Wiki](https://www.waveshare.com/wiki/ESP32-P4-WIFI6-Touch-LCD-7B?srsltid=AfmBOoo9Y7EjqQdv_sgnoF6c126Rnp5BGZYRx9xw3hhQHx_hje2eVhM6)  
[Schematic](https://files.waveshare.com/wiki/ESP32-P4-WIFI6-Touch-LCD-7B/ESP32-P4-WIFI6-Touch-LCD-7B.pdf)  
[Datasheet](https://files.waveshare.com/wiki/common/Esp32-p4_datasheet_en.pdf)  
[Github esp32-p4-wifi6-touch-lcd-7b](https://github.com/waveshareteam/ESP32-P4-WIFI6-Touch-LCD-7B)  

# TODO
- General
  - Code cleanup
  - [ ] Fix backlight timing
- Theme/styles
- Media player widget
  - [ ] Album Art
  - [x] Power on
  - [ ] Previous
  - [ ] Skip
  - [ ] Play/pause
- Add battery charge widget

- Weather
  - [ ] Update HA weather template to fix hourly icons when partyly cloudy with sun/moon
  - daily/hourly_forecast
    - Update styles
  - current_conditions/weather_status_widget
    - [ ] Improve style customization
    - [ ] When single widget is clicked, display graph with historical values
  - 

