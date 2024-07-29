# FARMGUIDE
#define BLYNK_PRINT Serial
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <DHT.h>

#define DHTPIN 14 // D5 pin
#define DHTTYPE DHT11 // DHT 11

#define SOIL_MOISTURE_PIN A0
#define RAIN_SENSOR_PIN 12 // D6 pin
#define RELAY_PIN 13 // D7 pin

DHT dht(DHTPIN, DHTTYPE);

// Blynk Auth Token from Blynk app
char auth[] = "YourAuthToken";

// Your WiFi credentials
char ssid[] = "YourNetworkName";
char pass[] = "YourPassword";

void setup()
{
  Serial.begin(9600);
  Blynk.begin(auth, ssid, pass);

  dht.begin();
  pinMode(SOIL_MOISTURE_PIN, INPUT);
  pinMode(RAIN_SENSOR_PIN, INPUT);
  pinMode(RELAY_PIN, OUTPUT);
}

void loop()
{
  Blynk.run();

  // Reading temperature and humidity
  float h = dht.readHumidity();
  float t = dht.readTemperature(); // or dht.readTemperature(true) for Fahrenheit

  // Reading soil moisture
  int soilMoistureValue = analogRead(SOIL_MOISTURE_PIN);

  // Reading rain sensor
  int rainSensorValue = digitalRead(RAIN_SENSOR_PIN);

  // Sending data to Blynk app
  Blynk.virtualWrite(V1, h); // Virtual pin V1 for humidity
  Blynk.virtualWrite(V2, t); // Virtual pin V2 for temperature
  Blynk.virtualWrite(V3, soilMoistureValue); // Virtual pin V3 for soil moisture
  Blynk.virtualWrite(V4, rainSensorValue); // Virtual pin V4 for rain sensor

  // Controlling the water pump based on soil moisture
  if (soilMoistureValue < 600) { // Adjust the threshold as per your need
    digitalWrite(RELAY_PIN, LOW); // Turn on the pump
  } else {
    digitalWrite(RELAY_PIN, HIGH); // Turn off the pump
  }

  delay(2000); // Delay between sensor readings
}
