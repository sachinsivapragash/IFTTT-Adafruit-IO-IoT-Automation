# IFTTT + Adafruit IO IoT Automation

## 📌 Overview

This project demonstrates **IoT automation using ESP32, IFTTT, Adafruit IO, and Google Assistant**.

The system allows a user to control a connected bulb using a **voice command**. IFTTT receives the Google Assistant trigger and sends the corresponding value to an **Adafruit IO feed**. The ESP32, which is already subscribed to the same feed through MQTT, receives the command and controls the relay connected to the bulb.

The project demonstrates how **cloud-based IoT services can be integrated with an ESP32 to create a hands-free automation system**.

---

## ⚙️ How It Works

```text
Google Assistant
       ↓
   Voice Command
       ↓
      IFTTT
       ↓
Adafruit IO Feed
  "bulb-control"
       ↓
      MQTT
       ↓
      ESP32
       ↓
      Relay
       ↓
   230V AC Bulb
```

The same `bulb-control` feed is used by the existing dashboard and the IFTTT automation. The ESP32 does not need to know whether the command came from the dashboard or from IFTTT—it simply responds to the value received through MQTT.

---

## 🎯 Objective

* Implement **IoT automation without manual button control**
* Connect **Google Assistant with IFTTT**
* Send automation commands to **Adafruit IO**
* Receive commands on an **ESP32 through MQTT**
* Control a physical bulb using a **relay**
* Demonstrate cloud-based IoT communication and automation

---

## 🧰 Hardware Used

* **ESP32**
* **Relay Module**
* **230V AC Bulb**
* **Smartphone with Google Assistant**

The ESP32, relay, and bulb use the same hardware arrangement established in the previous task. No additional wiring is required for the IFTTT automation.

---

## 💻 Software & Platforms

* **Arduino IDE 2.3.10**
* **Adafruit MQTT Library**
* **Adafruit IO**
* **IFTTT**
* **Google Assistant**
* **Google Home**

---

## 🔌 Feed Used

**Adafruit IO Feed:**

```text
bulb-control
```

The feed acts as the communication bridge between IFTTT and the ESP32.

| Command  | Value | Result   |
| -------- | ----: | -------- |
| Bulb On  |   `1` | Bulb ON  |
| Bulb off |   `0` | Bulb OFF |

IFTTT sends these values to the feed, and the ESP32 processes them through the existing `processBulbCommand()` logic.

---

## 🗣️ Voice Automation

### Bulb ON

The user says:

```text
Okay Google, activate Bulb On
```

Flow:

```text
Google Assistant
       ↓
IFTTT "Bulb On" Applet
       ↓
Adafruit IO
       ↓
bulb-control = 1
       ↓
ESP32
       ↓
Relay ON
       ↓
Bulb ON
```

### Bulb OFF

The user says:

```text
Okay Google, activate Bulb off
```

Flow:

```text
Google Assistant
       ↓
IFTTT "Bulb off" Applet
       ↓
Adafruit IO
       ↓
bulb-control = 0
       ↓
ESP32
       ↓
Relay OFF
       ↓
Bulb OFF
```

Two separate IFTTT applets were configured because the Google Assistant **Activate scene** trigger performs a fixed action rather than automatically reading the current bulb state.

---

## 🔧 IFTTT Configuration

### Applet 1 — Bulb ON

**Trigger:**

```text
Google Assistant → Activate scene
```

**Action:**

```text
Adafruit → Send data to Adafruit IO
```

**Feed:**

```text
bulb-control
```

**Data:**

```text
1
```

### Applet 2 — Bulb OFF

**Trigger:**

```text
Google Assistant → Activate scene
```

**Action:**

```text
Adafruit → Send data to Adafruit IO
```

**Feed:**

```text
bulb-control
```

**Data:**

```text
0
```

The Adafruit IO account must be authorized to allow IFTTT to read and write feed data.

---

## 💡 Code Implementation

No new ESP32 code was required for this task.

The **same ESP32 sketch from the previous task** remains subscribed to the `bulb-control` feed and controls the relay through `processBulbCommand()`.

This demonstrates the advantage of using a cloud-based MQTT architecture: **different control sources can publish to the same feed without requiring changes to the ESP32 firmware.**

### Important Logic

```text
IFTTT sends "1"
        ↓
Adafruit IO
        ↓
ESP32 receives "1"
        ↓
Relay ON
```

```text
IFTTT sends "0"
        ↓
Adafruit IO
        ↓
ESP32 receives "0"
        ↓
Relay OFF
```

---

## 🔄 System Architecture

```text
                  ┌─────────────────┐
                  │ Google Assistant│
                  └────────┬────────┘
                           │
                     Voice Command
                           │
                           ▼
                  ┌─────────────────┐
                  │      IFTTT      │
                  │  Applet Trigger │
                  └────────┬────────┘
                           │
                      MQTT Data
                           │
                           ▼
                  ┌─────────────────┐
                  │   Adafruit IO   │
                  │  bulb-control   │
                  └────────┬────────┘
                           │
                         MQTT
                           │
                           ▼
                  ┌─────────────────┐
                  │      ESP32      │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │  Relay Module   │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │   230V AC Bulb  │
                  └─────────────────┘
```

---

## 🧪 Testing & Evidence

The system was tested by:

* Verifying the **Adafruit IO library installation**
* Re-compiling the existing ESP32 sketch
* Confirming **Wi-Fi + MQTT connectivity**
* Configuring the ESP32 as **ESP32 Dev Module**
* Testing the configured serial port
* Creating and connecting both IFTTT applets
* Testing voice commands through Google Assistant
* Confirming the bulb responds to ON/OFF commands
* Verifying the Adafruit IO dashboard state alongside the physical bulb

---

## 🛠️ Challenges & Solutions

### 1. Separate ON and OFF Automation

A single IFTTT Activate-scene trigger could not automatically toggle the bulb based on its current state.

**Solution:** Two separate applets were created:

```text
Bulb On  →  Send 1
Bulb off →  Send 0
```

### 2. Adafruit IO Authorization

IFTTT could not write to the feed until Adafruit IO authorization was completed.

**Solution:** The required read/write permission was granted to IFTTT.

### 3. Matching Data Values

The ESP32 sketch expected specific `1` and `0` values.

**Solution:** The IFTTT actions were configured to send exactly those values.

---

## 📚 Key Concepts Learned

* **IoT Automation**
* **IFTTT Applets**
* **Google Assistant Integration**
* **Adafruit IO**
* **MQTT Communication**
* **Cloud-Based Device Control**
* **ESP32**
* **Relay Control**
* **Trigger → Action Architecture**
* **Publish/Subscribe Communication**

---

## 💭 Reflection

This task demonstrated that the ESP32 does not need to be directly connected to every control interface. IFTTT can act as another publisher to the same Adafruit IO feed, while the ESP32 continues to listen to that feed through MQTT.

This makes it possible to add different control methods without changing the core ESP32 firmware.

---

## 📁 Repository Structure

```text
ifttt-adafruit-io-automation/
│
├── ifttt_adafruit_io_automation.ino
└── README.md
```

---

## 🚀 Project Outcome

Successfully implemented a **voice-controlled IoT automation system** in which:

**Google Assistant → IFTTT → Adafruit IO → ESP32 → Relay → Bulb**

The project demonstrates the integration of **voice commands, cloud services, MQTT communication, and physical IoT hardware** into a single automated workflow.
