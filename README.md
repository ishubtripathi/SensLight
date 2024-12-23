# SensLight
SensLight is a smart automation system designed to turn lights on or off based on object proximity, measured by an ultrasonic sensor. If an object is detected within a range of 30 inches, the light automatically turns on. When the object moves beyond the 30-inch range, the light turns off. SensLight also displays real-time distance, light status, temperature, and the current time using data fetched from OpenWeather API and NTP servers. The project integrates IoT capabilities for smart home automation, combining simplicity and functionality in one compact system.

## Features  

- **Proximity-based Light Automation**:  
  Automatically turns the light ON when an object is detected within 30 inches and OFF when the object moves farther.  

- **Real-Time Distance Measurement**:  
  Displays the distance of the object in both centimeters and inches.  

- **Temperature Display**:  
  Fetches real-time temperature data from OpenWeather API and displays it on the OLED screen.  

- **Time Display**:  
  Shows the current time in a 12-hour format (with AM/PM) using NTP servers for accurate synchronization.  

- **Compact OLED Display Output**:  
  The OLED screen displays:  
  - Time  
  - Temperature  
  - Distance  
  - Light status  

## Hardware Components  

- **ESP32**:  
  Microcontroller for Wi-Fi connectivity and data processing.  

- **Ultrasonic Sensor (HC-SR04)**:  
  Used for detecting object distance.  

- **OLED Display (0.96 inch)**:  
  For visualizing real-time data (128x64 resolution).  

- **Relay or LED**:  
  To control the light based on object proximity.  

## Software Requirements  

- **Arduino IDE**  
- OpenWeatherMap API Key  

## Dependencies  

The following libraries are required for this project:  
1. **ESP32 Libraries**  
   - `WiFi.h`  
   - `HTTPClient.h`  

2. **Adafruit Libraries**  
   - `Adafruit_SSD1306.h`  
   - `Adafruit_GFX.h`  

3. **Time Library**  
   - `NTPClient.h`  


## Setup Instructions  

1. **Hardware Setup**:  
   - Connect the Ultrasonic Sensor (HC-SR04):  
     - Trig Pin → GPIO 5  
     - Echo Pin → GPIO 18  
     - VCC → 5V  
     - GND → GND  

   - Connect the OLED Display (0.96-inch, I2C):  
     - SDA → GPIO 21  
     - SCL → GPIO 22  
     - VCC → 3.3V or 5V  
     - GND → GND  

   - Connect the light (LED or Relay) to GPIO 13 for control.  

2. **Software Setup**:  
   - Install the required libraries in the Arduino IDE:  
     - `Adafruit_SSD1306`  
     - `Adafruit_GFX`  
     - `NTPClient`  
   - Configure your Wi-Fi credentials in the `ssid` and `password` variables.  
   - Generate an OpenWeather API key and replace `weatherApiKey` with your API key.  

3. **Upload Code**:  
  -  Use the Arduino IDE to upload the code to your ESP32.  



## Usage  

1. Power on the ESP32 module.  
2. The system will automatically connect to the specified Wi-Fi network.  
3. The ultrasonic sensor will measure the distance of any object in front of it:  
   - Light turns ON if the object is within 30 inches.  
   - Light turns OFF if the object moves farther than 30 inches.  
4. The OLED display will show the following real-time information:  
   - Distance (in cm and inches).  
   - Current time (in 12-hour format with AM/PM).  
   - Temperature (in Celsius).  
   - Light status (ON/OFF).  

## Example Output  

- Time: 12:15 PM
- Temp: 28.5°C
- Dist: 25.7 in
- Light: ON

## Future Enhancements  

- Add mobile app integration for remote control and monitoring.  
- Implement voice commands for light control.  
- Expand functionality to include multiple sensors for larger areas.  

## License  

This project is licensed under the MIT License. Feel free to contribute and enhance it further!  

## Contact  

For any queries or contributions, feel free to connect:  
- **Email**: ishubtripathi@gmail.com  
- **LinkedIn**: [Shubhrant Tripathi](https://www.linkedin.com/in/ishubtripathi/)  

## Screenshots

![SensLight Display](/asset/sensLight.png)