# Nidhish
// Libraries
#include <DHT.h>
#include <LiquidCrystal_I2C.h>
#include <Wire.h>

// Constants
#define DHTPIN A3          // Pin connected to the DHT sensor
#define DHTTYPE DHT11      // DHT 11 type sensor
#define BUZZER_PIN 7       // Pin connected to the buzzer
#define HEARTBEAT_PIN A0   // Pin connected to the heartbeat sensor
#define TEMP_THRESHOLD 37  // Temperature threshold in Celsius
#define BPM_THRESHOLD 180  // Heartbeat threshold in beats per minute

// Initialize DHT sensor and LCD
DHT dht(DHTPIN, DHTTYPE);
LiquidCrystal_I2C lcd(0x27, 16, 2); // LCD address set to 0x27 for a 16x2 display

// Variables
int h;  // Stores humidity value
int t;  // Stores temperature value
int bpm; // Stores heartbeat value

void setup() {
    // Initialize serial communication
    Serial.begin(9600);
    Serial.println("Temperature, Humidity, and Heartbeat Monitor");

    // Initialize DHT sensor
    dht.begin();

    // Initialize LCD
    lcd.init(); // Initialize the LCD
    lcd.backlight(); // Turn on the LCD backlight

    // Set up the buzzer pin
    pinMode(BUZZER_PIN, OUTPUT);
    digitalWrite(BUZZER_PIN, LOW); // Ensure the buzzer is off initially
}

void loop() {
    // Read temperature and humidity from DHT sensor
    h = dht.readHumidity();
    t = dht.readTemperature();

    // Read heartbeat sensor value
    bpm = analogRead(HEARTBEAT_PIN); // Replace with actual heartbeat sensor reading logic if available
    bpm = map(bpm, 0, 1023, 0, 200); // Example mapping to BPM range, adjust based on sensor specifics

    // Print temp, humidity, and heartbeat values to the serial monitor
    Serial.print("Humidity: ");
    Serial.print(h);
    Serial.print(" %, Temp: ");
    Serial.print(t);
    Serial.println(" °C");

    Serial.print("Heartbeat: ");
    Serial.print(bpm);
    Serial.println(" BPM");

    // Display temperature and humidity on the LCD
    lcd.setCursor(0, 0);
    lcd.print(" emergency alert");

    lcd.setCursor(0, 1);
    lcd.print("T:");
    lcd.print(t);
    lcd.print("C");

    lcd.setCursor(11, 1);
    lcd.print("H:");
    lcd.print(h);
    lcd.print("%");

    // Check temperature and heartbeat thresholds
    if (t > TEMP_THRESHOLD || bpm > BPM_THRESHOLD) {
        digitalWrite(BUZZER_PIN, HIGH); // Activate the buzzer
    } else {
        digitalWrite(BUZZER_PIN, LOW); // Deactivate the buzzer
    }

    delay(1000); // Delay between readings
}
