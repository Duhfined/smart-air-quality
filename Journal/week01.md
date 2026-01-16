## Goals
- Get reliable DHT readings

# Issues
- SSD1306 sometimes will turn off due to the header pins not being soldered on properly.
- SSD1306 will not reboot automatically; has to be reset from the ESP32.

# Notes
- OLED displays a bunch of random text for a second after opening up.

# Next Steps
- Find a workaround to let the SSD1306 automatically boot up after a brownout or have a proper connection.
- Add an uptime monitor on the oled.
