## Goals
- Get reliable DHT readings

# Issues
- SSD1306 sometimes will turn off due to the header pins not being soldered on properly.
- SSD1306 will not reboot automatically; has to be reset from the ESP32.


# Notes
- OLED displays a bunch of random text for a second after opening up.

# Next Steps
- Find a workaround to let the SSD1306 automatically boot up after a brownout or have a proper connection.
- Add an uptime monitor on the OLED.
- A way to store and collect Data
- Internet coding

# Code (At the end of each week)
```
#include <DHT.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <Wire.h>
#include <WiFi.h>
#include <WebServer.h>

#define DHTPin 16
#define DHTTYPE DHT11

#define SDA_PIN 18
#define SCL_PIN 17

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET -1

Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);
DHT dht(DHTPin, DHTTYPE);

/* ===== Timing ===== */
unsigned long lastRead = 0;
const unsigned long interval = 2000; // DHT11 safe timer value

/* ===== Sensor data ===== */
float temp = NAN;
float humidity = NAN;

/* ===== Uptime ===== */
unsigned long totalSeconds;
unsigned long days, hours, minutes, seconds;

/* ===== WiFi ===== */
const char* ssid = "Cik_Mesh_1200_5140";
const char* password = "38144EA95140";
WebServer server(80); //sets up web server on port 80

/* ===== Function declarations ===== */
void readDHT();
void updateUptime();
void updateDisplay();

void setup() {
  Serial.begin(115200);

  Wire.begin(SDA_PIN, SCL_PIN);
  Wire.setClock(400000);

  WiFi.begin(ssid, password);
  dht.begin();

  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println(F("SSD1306 allocation failed"));
    while (true);
  }

  display.setTextWrap(false);
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.clearDisplay();
  display.display();

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("\nConnected!");
  Serial.print("IP Address: ");
  Serial.println(WiFi.localIP());
}

/* ===== Read DHT ===== */
void readDHT() {
  humidity = dht.readHumidity();
  temp = dht.readTemperature();

  if (isnan(temp) || isnan(humidity)) {
    Serial.println("Failed to read from DHT sensor!");
    return;
  }

  Serial.print("Temperature: ");
  Serial.print(temp);
  Serial.print(" C  Humidity: ");
  Serial.print(humidity);
  Serial.println(" %");
}

/* ===== Uptime ===== */
void updateUptime() {
  totalSeconds = millis() / 1000;

  days = totalSeconds / 86400;
  hours = (totalSeconds % 86400) / 3600;
  minutes = (totalSeconds % 3600) / 60;
  seconds = totalSeconds % 60;
}

/* ===== OLED ===== */
void updateDisplay() {
  display.clearDisplay();
  display.setCursor(0, 0);

  display.print("Temperature: ");
  display.print(temp);
  display.println(" C");

  display.print("Humidity: ");
  display.print(humidity);
  display.println(" %");

  display.print("Uptime: ");
  display.print(days);
  display.print(":");
  display.print(hours);
  display.print(":");
  display.print(minutes);
  display.print(":");
  display.print(seconds);

  display.display();
}

/* ===== Main loop ===== */
void loop() {
  if (millis() - lastRead >= interval) {
    lastRead = millis();

    readDHT();
    updateUptime();
    updateDisplay();
  }
} ```

