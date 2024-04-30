# iot-based-project-weather-system
# code for project
#include <DHT.h>
#include <Wire.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_BMP280.h>
#include <ESP8266WiFi.h>

// Replace with your network credentials
const char *ssid = "your-SSID";
const char *password = "your-PASSWORD";

// Replace with your server details
const char *server = "your-server.com";
const int port = 80;

// Replace with your sensor details
#define DHTPIN 2  // D2 pin for DHT sensor
#define DHTTYPE DHT22
DHT dht(DHTPIN, DHTTYPE);

Adafruit_BMP280 bmp;

void setup() {
  Serial.begin(115200);
  dht.begin();
  bmp.begin(0x76);  // Address 0x76 for BMP280
  connectToWiFi();
}

void loop() {
  float temperature = dht.readTemperature();
  float humidity = dht.readHumidity();
  float pressure = bmp.readPressure() / 100.0F;

  sendToServer(temperature, humidity, pressure);

  delay(60000);  // Send data every minute
}

void connectToWiFi() {
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi");
}

void sendToServer(float temperature, float humidity, float pressure) {
  WiFiClient client;

  if (client.connect(server, port)) {
    Serial.println("Connected to server");

   Format your data as needed
    String data = "temperature=" + String(temperature) +
                  "&humidity=" + String(humidity) +
                  "&pressure=" + String(pressure);

   Send HTTP POST request
    client.println("POST /path/to/your/api HTTP/1.1");
    client.println("Host: " + String(server));
    client.println("Content-Type: application");
    client.println("Content-Length: " + String(data.length()));
    client.println();
    client.print(data);

    Serial.println("Data sent to server");
  } else {
    Serial.println("Unable to connect to server");
  }

  client.stop();
}
```
