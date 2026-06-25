# ESP32 NTP Digital Clock 🕐

An ESP32-based digital clock that syncs real-time data via NTP and displays time in HH:MM format on a TM1637 7-segment display. Connects to WiFi on startup, fetches time from pool.ntp.org, and features a blinking colon. Built using Arduino IDE with IST (UTC+5:30) timezone support.

---

## 📷 Preview

> A 4-digit 7-segment display showing current time with a blinking colon separator.

---

## 🛠 Hardware Requirements

| Component | Quantity |
|---|---|
| ESP32 Development Board | 1 |
| TM1637 4-Digit 7-Segment Display | 1 |
| Jumper Wires | 4 |

---

## 🔌 Pin Configuration

| TM1637 Pin | ESP32 Pin |
|---|---|
| CLK | GPIO 22 |
| DIO | GPIO 21 |
| VCC | 3.3V |
| GND | GND |

---

## 📦 Libraries Required

Install the following libraries from **Arduino Library Manager**:

- `TM1637Display` by Avishay Orpaz
- `WiFi.h` *(built-in with ESP32 board package)*
- `time.h` *(built-in)*

---

## ⚙️ Setup & Installation

1. Install **Arduino IDE** and add ESP32 board support
2. Install the **TM1637Display** library via Library Manager
3. Clone this repository:
   ```
   git clone https://github.com/your-username/esp32-ntp-clock.git
   ```
4. Open `sketch_apr27a.ino` in Arduino IDE
5. Update your WiFi credentials in the code:
   ```cpp
   const char* ssid = "your_wifi_name";
   const char* password = "your_wifi_password";
   ```
6. Select **ESP32 Dev Module** as the board
7. Upload the sketch

---

## 🌍 Timezone Configuration

The default timezone is set to **IST (UTC+5:30)**. To change it, update this line:

```cpp
const long gmtOffset_sec = 19800; // IST = 5*3600 + 30*60
```

Common timezone offsets:

| Timezone | Offset (seconds) |
|---|---|
| IST (India) | 19800 |
| UTC | 0 |
| EST (US East) | -18000 |
| PST (US West) | -28800 |
| CET (Europe) | 3600 |

---

## 💻 Code Overview

```cpp
#include <WiFi.h>
#include <time.h>
#include <TM1637Display.h>

const char* ssid = "your_wifi_name";
const char* password = "your_wifi_password";

#define CLK 22
#define DIO 21

TM1637Display display(CLK, DIO);

const char* ntpServer = "pool.ntp.org";
const long gmtOffset_sec = 19800;
const int daylightOffset_sec = 0;

bool colonState = false;

void setup() {
  Serial.begin(115200);
  display.setBrightness(5);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) delay(500);
  configTime(gmtOffset_sec, daylightOffset_sec, ntpServer);
}

void loop() {
  struct tm timeinfo;
  if (!getLocalTime(&timeinfo)) return;

  int displayTime = timeinfo.tm_hour * 100 + timeinfo.tm_min;
  colonState = !colonState;

  if (colonState)
    display.showNumberDecEx(displayTime, 0b01000000, true);
  else
    display.showNumberDec(displayTime, true);

  delay(500);
}
```

---

## 🚀 How It Works

1. ESP32 connects to the provided WiFi network
2. Syncs time from `pool.ntp.org` using NTP
3. Reads current hour and minute every 500ms
4. Formats time as a 4-digit number (e.g., 14:35 → 1435)
5. Displays on TM1637 with a blinking colon

---

## 🔧 Troubleshooting

| Issue | Solution |
|---|---|
| Display not working | Check CLK/DIO pin connections |
| WiFi not connecting | Verify SSID and password |
| Wrong time shown | Update `gmtOffset_sec` for your timezone |
| Time not syncing | Check internet connection |

---.



## 🙌 Acknowledgements

- [TM1637Display Library](https://github.com/avishorp/TM1637) by Avishay Orpaz
- ESP32 Arduino Core by Espressif

-
