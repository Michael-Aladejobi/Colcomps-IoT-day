Here is the complete, professional, and fun `README.md` for your project. It is written to be pasted directly into GitHub, GitLab, or any markdown viewer. It covers both the Web-Controlled Smart Home and the Ultrasonic Parking Sensor. 

***

# 🚀 COLCOMPS IoT Day 2026: Smart Home & Parking Automation

Welcome to the official repository for the **COLCOMPS IoT Day 2026** practical projects! 

This repository contains two complete projects designed for absolute beginners. By following this guide, you will transform a simple ESP8266 microcontroller into a **WiFi-controlled Smart Home** and a **Physical World Parking Sensor**.

*Learn | Build | Innovate!*

---

## 🛠️ Hardware Requirements
For both projects, you will need the components from your training kit:
- 1x ESP8266 (NodeMCU) Development Board
- 1x Breadboard (MB102)
- 3x LEDs (Red, Yellow, Green)
- 1x Active Buzzer Module
- 1x HC-SR04 Ultrasonic Sensor
- Resistors (220Ω or 330Ω)
- Jumper Wires (Male-to-Male)
- USB Data Cable

---

# Project 1: Smart Home Controller (WiFi Web Server)

### 📖 Overview
This project turns the ESP8266 into a **local WiFi Access Point**. You will be able to connect your phone to the ESP8266 and use a beautiful, clean web dashboard to control the LEDs and the Buzzer independently, or all at once.

### 🔌 Wiring Guide
| Component | ESP8266 Pin | Notes |
| :--- | :--- | :--- |
| **Red LED** | D1 | Positive leg (via resistor). Negative to GND. |
| **Yellow LED** | D2 | Positive leg (via resistor). Negative to GND. |
| **Green LED** | D3 | Positive leg (via resistor). Negative to GND. |
| **Buzzer** | D5 | Signal pin. VCC to 3V3, GND to GND. |

### 💻 The Code
Copy and paste the code below into your Arduino IDE and upload it to your ESP8266.

```cpp
#include <ESP8266WiFi.h>
#include <ESP8266WebServer.h>

// --- WiFi Settings ---
const char* ssid = "Smart_Home_Automation";  
const char* password = "password123";      

// --- Pin Definitions ---
#define RED_LED D1
#define YELLOW_LED D2
#define GREEN_LED D3
#define BUZZER D5

ESP8266WebServer server(80);

// --- State Variables ---
bool redState = false;
bool yellowState = false;
bool greenState = false;
bool buzzerState = false;

// --- Elegant, Self-Contained UI ---
String getHTML() {
  String html = "<!DOCTYPE html><html><head>";
  html += "<meta name='viewport' content='width=device-width, initial-scale=1'>";
  html += "<style>";
  html += "body { font-family: Arial, sans-serif; background-color: #f4f7f6; text-align: center; margin: 0; padding: 20px; color: #333; }";
  html += "h1 { color: #2c3e50; font-size: 24px; margin-bottom: 5px; }";
  html += "p { color: #7f8c8d; font-size: 14px; margin-bottom: 30px; }";
  html += ".grid { display: flex; flex-wrap: wrap; justify-content: center; gap: 15px; max-width: 400px; margin: 0 auto; }";
  html += ".btn { width: 150px; height: 120px; border: none; border-radius: 12px; font-size: 16px; font-weight: bold; cursor: pointer; display: flex; flex-direction: column; justify-content: center; align-items: center; color: white; transition: 0.2s; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }";
  html += ".btn:active { transform: scale(0.95); }";
  html += ".red { background-color: #e74c3c; } .red.off { background-color: #bdc3c7; color: #7f8c8d; }";
  html += ".yellow { background-color: #f1c40f; color: #333; } .yellow.off { background-color: #bdc3c7; color: #7f8c8d; }";
  html += ".green { background-color: #2ecc71; } .green.off { background-color: #bdc3c7; color: #7f8c8d; }";
  html += ".purple { background-color: #9b59b6; } .purple.off { background-color: #bdc3c7; color: #7f8c8d; }";
  html += ".master-container { margin-top: 30px; display: flex; flex-direction: column; gap: 10px; align-items: center; }";
  html += ".master-btn { width: 100%; max-width: 315px; padding: 15px; border: none; border-radius: 8px; font-size: 16px; font-weight: bold; cursor: pointer; color: white; }";
  html += ".all-on { background-color: #34495e; } .all-off { background-color: #95a5a6; }";
  html += ".status { margin-top: 8px; font-size: 14px; font-weight: normal; }";
  html += "</style></head>";

  html += "<body>";
  html += "<h1>Smart Home Controller</h1>";
  html += "<p>COLCOMPS IoT Day 2026</p>";
  
  html += "<div class='grid'>";
  html += "<button class='btn red " + String(redState ? "" : "off") + "' onclick=\"fetch('/toggle_red').then(() => location.reload())\">";
  html += "RED LED<span class='status'>" + String(redState ? "ON" : "OFF") + "</span></button>";
  
  html += "<button class='btn yellow " + String(yellowState ? "" : "off") + "' onclick=\"fetch('/toggle_yellow').then(() => location.reload())\">";
  html += "YELLOW LED<span class='status'>" + String(yellowState ? "ON" : "OFF") + "</span></button>";

  html += "<button class='btn green " + String(greenState ? "" : "off") + "' onclick=\"fetch('/toggle_green').then(() => location.reload())\">";
  html += "GREEN LED<span class='status'>" + String(greenState ? "ON" : "OFF") + "</span></button>";

  html += "<button class='btn purple " + String(buzzerState ? "" : "off") + "' onclick=\"fetch('/toggle_buzzer').then(() => location.reload())\">";
  html += "ALARM<span class='status'>" + String(buzzerState ? "ON" : "OFF") + "</span></button>";
  
  html += "</div>"; 

  html += "<div class='master-container'>";
  html += "<button class='master-btn all-on' onclick=\"fetch('/all_on').then(() => location.reload())\">TURN ALL ON</button>";
  html += "<button class='master-btn all-off' onclick=\"fetch('/all_off').then(() => location.reload())\">TURN ALL OFF</button>";
  html += "</div>";

  html += "</body></html>";
  return html;
}

// --- Hardware Control Functions ---
void updateHardware() {
  digitalWrite(RED_LED, redState ? HIGH : LOW);
  digitalWrite(YELLOW_LED, yellowState ? HIGH : LOW);
  digitalWrite(GREEN_LED, greenState ? HIGH : LOW);
  digitalWrite(BUZZER, buzzerState ? HIGH : LOW);
}

void handleToggleRed()    { redState = !redState; updateHardware(); server.send(200, "text/plain", "OK"); }
void handleToggleYellow() { yellowState = !yellowState; updateHardware(); server.send(200, "text/plain", "OK"); }
void handleToggleGreen()  { greenState = !greenState; updateHardware(); server.send(200, "text/plain", "OK"); }
void handleToggleBuzzer() { buzzerState = !buzzerState; updateHardware(); server.send(200, "text/plain", "OK"); }

void handleAllOn() {
  redState = true; yellowState = true; greenState = true; buzzerState = true;
  updateHardware();
  server.send(200, "text/plain", "OK");
}

void handleAllOff() {
  redState = false; yellowState = false; greenState = false; buzzerState = false;
  updateHardware();
  server.send(200, "text/plain", "OK");
}

void handleRoot() {
  server.send(200, "text/html", getHTML());
}

void setup() {
  Serial.begin(115200);
  
  pinMode(RED_LED, OUTPUT);
  pinMode(YELLOW_LED, OUTPUT);
  pinMode(GREEN_LED, OUTPUT);
  pinMode(BUZZER, OUTPUT);
  
  digitalWrite(RED_LED, LOW);
  digitalWrite(YELLOW_LED, LOW);
  digitalWrite(GREEN_LED, LOW);
  digitalWrite(BUZZER, LOW);

  WiFi.softAP(ssid, password);
  
  Serial.println("Web Server Started!");
  Serial.print("Connect to WiFi: "); Serial.println(ssid);
  Serial.print("Open Browser to: "); Serial.println(WiFi.softAPIP()); 

  server.on("/", handleRoot);
  server.on("/toggle_red", handleToggleRed);
  server.on("/toggle_yellow", handleToggleYellow);
  server.on("/toggle_green", handleToggleGreen);
  server.on("/toggle_buzzer", handleToggleBuzzer);
  server.on("/all_on", handleAllOn);
  server.on("/all_off", handleAllOff);

  server.begin();
}

void loop() {
  server.handleClient(); 
}
```

### 🚀 How to Run It
1. Upload the code to your ESP8266.
2. Open the **Serial Monitor** (115200 baud). You will see the WiFi name and the IP address (usually `192.168.4.1`).
3. On your smartphone, go to WiFi settings and connect to **`Smart_Home_Automation`** (Password: `password123`). *Note: Your phone may say "No Internet Connection." That is normal!*
4. Open your phone's web browser and type `192.168.4.1` into the address bar.
5. Tap the buttons to control the lights and the alarm!

---

# Project 2: Smart Parking Sensor (Ultrasonic + Alarm)

### 📡 Overview
This project uses an **HC-SR04 Ultrasonic Sensor** to measure distance. Imagine a car trying to park; if it gets too close to the wall, the system triggers a Red LED and a Buzzer alarm. 

### 🔌 Wiring Guide
| Component | ESP8266 Pin | Notes |
| :--- | :--- | :--- |
| **Ultrasonic VCC** | 3V3 | Power for the sensor |
| **Ultrasonic GND** | GND | Ground |
| **Ultrasonic Trig** | D5 | Sends the sound pulse |
| **Ultrasonic Echo** | D6 | Listens for the bounce back |
| **Red LED** | D1 | Positive leg (via resistor). Negative to GND. |
| **Buzzer** | D2 | Signal pin. VCC to 3V3, GND to GND. |

### 💻 The Code
*(Copy and paste this into a new Arduino sketch)*

```cpp
// --- Pin Definitions ---
#define TRIG_PIN D5
#define ECHO_PIN D6
#define RED_LED D1
#define BUZZER D2

void setup() {
  Serial.begin(115200);
  
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);
  pinMode(RED_LED, OUTPUT);
  pinMode(BUZZER, OUTPUT);
  
  digitalWrite(RED_LED, LOW);
  digitalWrite(BUZZER, LOW);
  
  Serial.println("Smart Parking System Initialized!");
}

void loop() {
  // 1. Send an ultrasonic pulse
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);
  
  // 2. Read the echo
  long duration = pulseIn(ECHO_PIN, HIGH);
  
  // 3. Calculate distance in centimeters
  int distance = duration * 0.034 / 2;
  
  Serial.print("Distance: ");
  Serial.print(distance);
  Serial.println(" cm");

  // 4. Logic: If object is closer than 20cm, trigger alarm
  if (distance > 0 && distance < 20) {
    Serial.println("⚠️ ALERT: Object Detected Too Close!");
    digitalWrite(RED_LED, HIGH);
    digitalWrite(BUZZER, HIGH);
  } else {
    digitalWrite(RED_LED, LOW);
    digitalWrite(BUZZER, LOW);
  }
  
  delay(200); // Small delay between readings
}
```

### 🧪 How to Test It
1. Upload the code and open the **Serial Monitor** (115200 baud).
2. Place your hand in front of the two silver cylinders (the ultrasonic sensor).
3. Move your hand closer until it is less than 20cm away. The Red LED should turn on, and the buzzer should sound!
4. Move your hand away, and the alarm will stop.

---

## 🚀 Final Challenge for Students
Can you combine both projects? 
- Add a "Disable Alarm" button to the Web Server UI. 
- If the Ultrasonic sensor detects an object, the ESP8266 should send an alert to the web page.

---

## 👨‍🏫 Contact & Resources
**Facilitator:** Mike
**Website:** [https://mike.innopavetech.com](https://mike.innopavetech.com)

**Free IoT Resources:** 
Continue your learning journey with free IoT courses and tutorials at:
🌐 [https://www.innopavetech.com/courses/iot](https://www.innopavetech.com/courses/iot)

*Thank you for participating in COLCOMPS IoT Day 2026.*
**Learn | Build | Innovate!**
