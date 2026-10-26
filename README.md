# ESP32-Serial-Monitor-Fix
Code snippet and USB CDC checklist for fixing ESP32, C3, S3, and ESP32-CAM Serial Monitor issues.

# ESP32 Serial Monitor Not Working — Code & Quick Checklist

Quick test sketch and Arduino IDE settings for fixing blank or garbage Serial Monitor output on classic ESP32, ESP32-C3, ESP32-S3, ESP32-C6, and ESP32-CAM.

---

### 1. Safe Serial Test Sketch (Classic ESP32 + Native USB C3/S3/C6)
On native USB boards (ESP32-C3, S3, C6), `setup()` often runs before your PC finishes opening the virtual COM port. Adding a 3-second timeout wait ensures you don't miss the first `Serial.println()` messages without freezing the board if no PC is connected:

```cpp
void setup() {
  Serial.begin(115200);
  unsigned long t0 = millis();
  while (!Serial && millis() - t0 < 3000) {
    delay(10); // native USB (C3/S3/C6): wait up to 3 s for the PC
  }
  Serial.println("Hello from setup!");
}

void loop() {
  Serial.println("Serial is working...");
  delay(2000);
}
```

---

### 2. Arduino IDE `Tools` Menu Checklist (C3 / S3 / C6 / Super Mini)
* **Baud Rate:** Set Serial Monitor dropdown to `115200 baud` (matches `Serial.begin(115200)` and ROM boot messages).
* **Single Native USB Port (ESP32-C3 Super Mini, C6, S3 Mini):**
  * Go to `Tools -> USB CDC On Boot -> Enabled` (then re-upload the sketch).
* **Dual USB Ports (ESP32-S3-DevKitC-1):**
  * Plugged into **UART / COM** port: `USB CDC On Boot -> Disabled`
  * Plugged into **USB (Native)** port: `USB CDC On Boot -> Enabled`
* **ESP32-CAM Stuck on `waiting for download`:**
  * Disconnect `GPIO 0` from `GND`, then press the **RST** button on the camera board.
