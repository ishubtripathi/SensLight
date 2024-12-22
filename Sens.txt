#include <Wire.h>
#include <Adafruit_SSD1306.h>
#include <Adafruit_GFX.h>
#include <WiFi.h>
#include <HTTPClient.h>
#include <NTPClient.h>
#include <WiFiUdp.h>

// Wi-Fi credentials
const char* ssid = "WIFI_SSID";  // Replace with your Wi-Fi SSID
const char* password = "WIFI_PASSWORD";    // Replace with your Wi-Fi Password and comment if you are using public wifi

// OpenWeatherMap API credentials
// visit - https://openweathermap.org/  
const char* weatherApiKey = "API_KEY"; // Replace with open weather API
const char* city = "State_name";  // Replace with your actual state name

// Define the pins for the ultrasonic sensor (HC-SR04)
const int trigPin = 5;   // GPIO 5
const int echoPin = 18;  // GPIO 18

// Define sound speed in cm/uS
#define SOUND_SPEED 0.034
#define CM_TO_INCH 0.393701

long duration;
float distanceCm;
float distanceInch;

// Initialize the OLED display (use I2C address 0x3C for most OLED displays)
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);  // Reset pin is not used (-1)

// Pin to control light (LED/Relay)
const int lightPin = 13;  // GPIO 13 for controlling the light (LED/Relay)

// Time setup
WiFiUDP ntpUDP;
NTPClient timeClient(ntpUDP, "pool.ntp.org", 19800, 60000);  // IST (GMT+5:30)

// Variables to store temperature and time
float temperature = 0.0;
String currentTime = "";

void setup() {
  Serial.begin(115200);
  pinMode(trigPin, OUTPUT);   // Sets the trigPin as an Output
  pinMode(echoPin, INPUT);    // Sets the echoPin as an Input
  pinMode(lightPin, OUTPUT);  // Sets the lightPin as an Output

  // Initialize the OLED display
  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println(F("SSD1306 allocation failed"));
    for (;;)
      ;
  }
  display.display();  // Clear the buffer
  delay(2000);

  // Connect to Wi-Fi
  WiFi.begin(ssid, password); // if using private wifi network
  WiFi.begin(ssid); // if using public wifi network
  Serial.print("Connecting to Wi-Fi");
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.print(".");
  }
  Serial.println("\nConnected to Wi-Fi");

  // Initialize NTP client
  timeClient.begin();

  // Clear OLED after setup
  display.clearDisplay();
}

void loop() {
  // Update time and fetch temperature
  updateTime();
  fetchTemperature();

  // Measure distance
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);
  distanceCm = duration * SOUND_SPEED / 2;
  distanceInch = distanceCm * CM_TO_INCH;

  // Display data on OLED
  display.clearDisplay();

  // Left Half: Time (Top Half)
  display.setTextSize(3);  // Large font for hours
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0, 0);  // Adjusted for top-left position
  display.print(currentTime.substring(0, currentTime.indexOf(":")));  // Hours

  // Minutes just below the hours
  display.setTextSize(4);  // Larger font for minutes
  display.setCursor(0, 30);  // Position below the hours
  display.print(currentTime.substring(currentTime.indexOf(":") + 1, currentTime.indexOf(" ")));  // Minutes

  // AM/PM status in exponential format
  display.setTextSize(1);  // Smaller font for AM/PM
  display.setCursor(50, 50);  // Adjusted position for AM/PM
  display.print(currentTime.substring(currentTime.indexOf(" ") + 1));

  // Draw vertical dividing line
  display.drawLine(64, 0, 64, 64, SSD1306_WHITE);

  // Right Half: Details (Temperature, Distance, Light)
  display.setTextSize(1);  // Small font for details

  // Temperature Text
  display.setCursor(70, 0);  // Start position for Temperature text
  display.print("Temp");

  // Actual Temperature value
  display.setCursor(70, 10);  // Position below the "Temp:" text
  display.print(temperature);
  display.print("C");

  // Distance Text
  display.setCursor(70, 20);  // Start position for Distance text
  display.print("Dist");

  // Actual Distance value
  display.setCursor(70, 30);  // Position below the "Dist:" text
  display.print(distanceInch);
  display.print("in");

  // Light Text
  display.setCursor(70, 40);  // Start position for Light status text
  display.print("Light");

  // Light status (ON/OFF)
  display.setCursor(70, 50);  // Position below the "Light:" text
  if (distanceInch < 30) {
    digitalWrite(lightPin, LOW);  // Turn on the light
    display.print("ON");
  } else {
    digitalWrite(lightPin, HIGH);  // Turn off the light
    display.print("OFF");
  }

  // Draw horizontal dividing line
  // display.drawLine(0, 63, 128, 63, SSD1306_WHITE);

  // Update the OLED
  display.display();

  delay(500);  // Wait 0.5 seconds
}

void updateTime() {
  timeClient.update();
  int hours = timeClient.getHours();
  int minutes = timeClient.getMinutes();
  String amPm = "AM";

  // Convert to 12-hour format
  if (hours >= 12) {
    amPm = "PM";
    if (hours > 12) hours -= 12;
  } else if (hours == 0) {
    hours = 12;
  }

  // Format time string
  currentTime = String(hours) + ":" + (minutes < 10 ? "0" : "") + String(minutes) + " " + amPm;
}

void fetchTemperature() {
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;
    String apiUrl = String("http://api.openweathermap.org/data/2.5/weather?q=") + city + "&appid=" + weatherApiKey + "&units=metric";
    http.begin(apiUrl);
    int httpResponseCode = http.GET();

    if (httpResponseCode == 200) {
      String payload = http.getString();
      int tempIndex = payload.indexOf("\"temp\":") + 7;
      temperature = payload.substring(tempIndex, payload.indexOf(",", tempIndex)).toFloat();
    } else {
      Serial.println("Failed to fetch temperature");
    }
    http.end();
  }
}