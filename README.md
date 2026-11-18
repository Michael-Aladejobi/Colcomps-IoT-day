# Colcomps-IoT-day

This is the perfect final project. It takes them from "I made a light blink" to **"I built a Smart Home device controlled from my phone."** It uses the built-in WiFi of the ESP8266 (which is why that board is so special) and gives them a massive "wow" moment.

Here is the complete, highly professional documentation and the "copy-paste" code for the **Smart Traffic Light Web Server**.

I have written this so it can be added directly as **Section 8** (before your Troubleshooting Guide) or as a standalone **Bonus Project** handout.

***

# SECTION 8: THE SMART TRAFFIC LIGHT (WiFi Web Control)

**The Scenario:** 
You are a smart city engineer. You don't want to stand in the rain to change the traffic lights. You want to control the Red, Yellow, and Green lights from your phone using a beautiful web interface over WiFi. 

In this project, we will turn your ESP8266 into a **WiFi Web Server**. Your phone will connect to the ESP8266, open a web page, and you will click buttons to control the LEDs.

### 🛠️ What You Need
*   ESP8266 Board (connected to your laptop via USB)
*   Your existing 3-LED Traffic Light circuit (Red on D1, Yellow on D2, Green on D3)
*   Your phone (to connect to the WiFi and control the lights)

> ⚠️ **Important:** Make sure your Red LED is on **D1**, Yellow on **D2**, Green on **D3**, and all are connected to GND via resistors.

---

### Step 1: The Code (Copy & Paste)
The ESP8266 is special because it has built-in WiFi. The code below does three things:
1.  It creates its own WiFi network (called an Access Point).
2.  It hosts a beautiful web page with 4 buttons (Red, Yellow, Green, and All Off).
3.  It listens for your button presses and turns the correct LED on.

Copy and paste this entire block into your Arduino IDE:

```cpp
#include <ESP8266WiFi.h>
#include <ESP8266WebServer.h>

// --- WiFi Settings ---
const char* ssid = "Smart_Traffic_Light";  // The WiFi name your phone will see
const char* password = "password123";      // The WiFi password

// --- Pin Definitions ---
#define RED_LED D1
#define YELLOW_LED D2
#define GREEN_LED D3

// Create the web server on port 80
ESP8266WebServer server(80);

// --- The Beautiful Web Page (HTML & CSS) ---
// This is the page that will load on your phone.
String getHTML() {
  String html = "<!DOCTYPE html><html>";
  html += "<head><meta name='viewport' content='width=device-width, initial-scale=1'>";
  html += "<style>";
  html += "body { font-family: Arial, sans-serif; text-align: center; background-color: #1e1e2f; color: white; margin-top: 50px; }";
  html += "h1 { color: #00d2ff; }";
  html += ".btn { border: none; color: white; padding: 20px; text-align: center; text-decoration: none; display: inline-block; font-size: 18px; margin: 10px; border-radius: 12px; cursor: pointer; width: 80%; max-width: 300px; font-weight: bold; transition: 0.3s; }";
  html += ".red { background-color: #ff4c4c; } .red:hover { background-color: #ff0000; }";
  html += ".yellow { background-color: #ffcc00; color: black; } .yellow:hover { background-color: #ffb300; }";
  html += ".green { background-color: #4caf50; } .green:hover { background-color: #00ff00; }";
  html += ".off { background-color: #555; } .off:hover { background-color: #777; }";
  html += "</style></head>";
  html += "<body>";
  html += "<h1>COLCOMPS IoT Day 2026</h1>";
  html += "<h2>Smart Traffic Light</h2>";
  html += "<p>Tap a button to control the lights!</p>";
  
  // The Buttons
  html += "<button class='btn red' onclick=\"fetch('/red')\">Turn RED ON</button><br>";
  html += "<button class='btn yellow' onclick=\"fetch('/yellow')\">Turn YELLOW ON</button><br>";
  html += "<button class='btn green' onclick=\"fetch('/green')\">Turn GREEN ON</button><br>";
  html += "<button class='btn off' onclick=\"fetch('/off')\">ALL OFF</button>";
  
  html += "</body></html>";
  return html;
}

// --- Functions to Control the LEDs ---
void turnOffAll() {
  digitalWrite(RED_LED, LOW);
  digitalWrite(YELLOW_LED, LOW);
  digitalWrite(GREEN_LED, LOW);
}

void handleRed() {
  turnOffAll();
  digitalWrite(RED_LED, HIGH);
  server.send(200, "text/html", getHTML());
}

void handleYellow() {
  turnOffAll();
  digitalWrite(YELLOW_LED, HIGH);
  server.send(200, "text/html", getHTML());
}

void handleGreen() {
  turnOffAll();
  digitalWrite(GREEN_LED, HIGH);
  server.send(200, "text/html", getHTML());
}

void handleOff() {
  turnOffAll();
  server.send(200, "text/html", getHTML());
}

void handleRoot() {
  server.send(200, "text/html", getHTML());
}

// --- Setup ---
void setup() {
  Serial.begin(115200);
  
  // Set pins as outputs
  pinMode(RED_LED, OUTPUT);
  pinMode(YELLOW_LED, OUTPUT);
  pinMode(GREEN_LED, OUTPUT);
  turnOffAll();

  // Start the WiFi Access Point
  Serial.println("Setting up WiFi...");
  WiFi.softAP(ssid, password);
  
  Serial.print("WiFi Created! Connect your phone to: ");
  Serial.println(ssid);
  Serial.print("Open your browser and go to: ");
  Serial.println(WiFi.softAPIP()); // This prints the IP address (usually 192.168.4.1)

  // Link the web addresses to the functions
  server.on("/", handleRoot);
  server.on("/red", handleRed);
  server.on("/yellow", handleYellow);
  server.on("/green", handleGreen);
  server.on("/off", handleOff);

  server.begin();
  Serial.println("Web Server Started!");
}

// --- Loop ---
void loop() {
  server.handleClient(); // Listen for phone requests
}
```

---

### Step 2: Upload the Code
1.  Ensure your board is selected (NodeMCU 1.0) and the correct COM port is selected.
2.  Click the **Upload** button.
3.  Wait for the word "Done uploading" in the console.

---

### Step 3: Connect and Control (The Magic Moment!)
1.  Once uploaded, open the **Serial Monitor** (magnifying glass icon, baud rate 115200). You should see a message saying:
    `WiFi Created! Connect your phone to: Smart_Traffic_Light`
2.  Take your smartphone and open **WiFi Settings**.
3.  Look for the network named **`Smart_Traffic_Light`** and connect to it. 
    *   *Password:* `password123`
    *   *(Note: Your phone might say "No Internet Connection." This is normal! Ignore it and stay connected to this WiFi).*
4.  Open your phone's web browser (Safari or Chrome).
5.  In the address bar, type the IP address shown in your Serial Monitor. It is usually: **`192.168.4.1`**
6.  Press Enter.

**🎉 BOOM!** You should see a beautiful dark-themed web page with buttons. Tap **"Turn RED ON"**, and watch your LED light up instantly. Tap "Turn GREEN ON", and the red light switches off while the green light comes on. 

You have just built a remote-controlled IoT system. No internet required—it's a local network!

---

### 🚀 How this relates to the real world:
This exact logic is how smart home devices work. When you buy a smart bulb (like Tuya or Philips Hue), it has a tiny microcontroller (like the ESP8266) inside it. It creates a small web server, and the app on your phone sends HTTP requests (`/red`, `/green`) to turn the lights on and off. 

You are now an official IoT developer. **Learn | Build | Innovate!**
