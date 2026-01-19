# Wiring

This file explains the wiring and purpose of each connection and component.
Includes schematics and theory.

## ESP32-S3
- Gnd pin is connected to all gnd components (Through Ground Rail on breadboard)
- 5V pin is connected to 5V components.
    - DHT11
    - VCC (SSD1306)
- GPIO 16
    - Connected to DHT11 Signal Pin to read temperature and humidity signals
- GPIO 17
    - Connected to SSD1306's SCL
    - Responsible for I2C communication
- GPIO 18
    - Connected to SSD1306's SDA
    - Responsible for I2C communication
