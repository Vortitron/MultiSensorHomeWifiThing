# MultiSensorHomeWifiThing
ESP32 based device with a load of sensors that can provide wifi mesh and voice service and music from esphome and home assistant 


# Smart Home Node Project with ESP32-PoE

This project develops a compact, PoE-powered smart home node based on the Olimex ESP32-PoE-EA-16MB-WROVER, designed to manage a 2.4 GHz mesh network, environmental sensors, voice assistance, music streaming, and multiple SSIDs with VLAN support. The node fits into 60mm x 50mm wall boxes, providing a scalable solution for home automation.

## Aims
- Create a multi-functional smart home node for environmental monitoring, voice interaction, and music streaming.
- Implement a 2.4 GHz mesh network with three isolated SSIDs (private, UK VPN, guest) using VLANs.
- Ensure compatibility with Power over Ethernet (PoE) for easy installation in wall boxes.
- Optimize for low power consumption and heat management in a confined space.
- Allow future expandability with additional peripherals (e.g., OLED display).
# Smart Home Node Project with PoE Splitter and ESP32

This project designs a compact, PoE-powered smart home node using a PoE splitter and ESP32-WROVER-E module, tailored for wall box installation. The node supports a 2.4 GHz mesh network, environmental monitoring, voice assistance, music streaming, and multiple SSIDs with VLAN support, offering flexibility and cost efficiency.

## Aims
- Develop a versatile smart home node for sensor data, voice interaction, and music streaming.
- Implement a 2.4 GHz mesh network with three isolated SSIDs (private, UK VPN, guest) using VLANs.
- Utilize a PoE splitter for power delivery, enabling a standard ESP32 module to fit within 60mm x 50mm wall boxes.
- Ensure low power consumption, heat management, and expandability in a confined space.
- Provide visual feedback with an OLED display.

## Components
- **ESP32-WROVER-E Module**: Microcontroller with 4 MB flash, 8 MB PSRAM, 2.4 GHz Wi-Fi, and BLE.
- **PoE Splitter (TP-Link TL-POE10R)**: Extracts 5V/2.4A from Cat6 for powering the node.
- **TCA9548A I2C Multiplexer**: 8-channel multiplexer to manage multiple I2C devices.
- **MQ135 Gas Sensor**: Detects air quality (CO2, VOCs).
- **DHT22 Temperature and Humidity Sensor**: Monitors indoor climate.
- **CCS811 Air Quality Sensor**: Measures CO2 and TVOC levels.
- **BH1750 Light Sensor**: Tracks ambient light intensity.
- **HC-SR501 PIR Motion Sensor**: Detects motion for occupancy.
- **Reed Switch**: Monitors door/window status.
- **INMP441 MEMS Microphone**: Provides I2S input for voice commands.
- **MAX98357A I2S Amplifier**: Drives audio output for voice and music.
- **5V 3W 8Ω Speaker**: Outputs audio for voice assistant and music streaming.
- **433 MHz Transmitter/Receiver**: Enables wireless control (e.g., remote switches).
- **Aluminum Heat Sink**: Manages thermal load in the wall box.
- **1A Fuse**: Protects against overcurrent in the PoE circuit.
- **OLED Display (0.96" I2C)**: Small display for status feedback (e.g., sensor readings).
- **Jumper Wires**: For internal connections.

## Why We Chose the Components and Considerations

### ESP32-WROVER-E Module
- **Why**: Selected for its 8 MB PSRAM to support ESP-Spotify-Connect for music streaming, 36 GPIOs for flexibility, and lower cost (~70–90 SEK) compared to PoE-integrated boards. The non-PoE design fits the splitter approach.
- **Considerations**: Requires external 5V from the PoE splitter. Heat management is key in the box (~60–75°C expected), mitigated with a heat sink. USB-to-serial pins (e.g., GPIO 1, 3) are reserved but unused during operation.

### PoE Splitter (TP-Link TL-POE10R)
- **Why**: Provides 5V/2.4A from Cat6, enabling a standard ESP32 module to replace the bulkier PoE board, saving space and cost (~50–100 SEK).
- **Considerations**: External mounting avoids box constraints but adds wiring complexity. Ensure a quality splitter with surge protection to match 802.3af standards.

### TCA9548A I2C Multiplexer
- **Why**: Allows multiple I2C devices (CCS811, BH1750, OLED) to share two pins, freeing GPIOs. Cost-effective at ~5–10 SEK.
- **Considerations**: Requires I2C address management. Minor latency (~1–2 ms) is acceptable for sensor/OLED updates.

### MQ135 Gas Sensor
- **Why**: Offers air quality monitoring with one analog pin.
- **Considerations**: Needs calibration; ADC usage limited to one sensor (e.g., GPIO 34).

### DHT22 Temperature and Humidity Sensor
- **Why**: Reliable climate monitoring with one digital pin.
- **Considerations**: Slow response (~2s) suits home use; avoid heat interference from the box.

### CCS811 Air Quality Sensor
- **Why**: Precise CO2/TVOC detection via I2C, complementing MQ135.
- **Considerations**: Shares I2C bus via TCA9548A; requires periodic calibration.

### BH1750 Light Sensor
- **Why**: Accurate light measurement via I2C for automation.
- **Considerations**: I2C address must differ; multiplexer handles this.

### HC-SR501 PIR Motion Sensor
- **Why**: Occupancy detection with one digital pin.
- **Considerations**: Avoid false triggers near heat sources.

### Reed Switch
- **Why**: Door/window monitoring with one digital pin.
- **Considerations**: Needs a 10kΩ pull-up; ensure magnet alignment.

### INMP441 MEMS Microphone
- **Why**: High-quality I2S input for voice commands, using three pins.
- **Considerations**: Requires clear placement; isolate I2S from output to avoid feedback.

### MAX98357A I2S Amplifier
- **Why**: I2S audio output for voice/music, using three pins with the speaker.
- **Considerations**: Generates heat (~0.5–1W); use a heat sink. Ensure 3.3V logic compatibility.

### 5V 3W 8Ω Speaker
- **Why**: Affordable audio output for voice and music.
- **Considerations**: Volume limited by box size; avoid overdriving.

### 433 MHz Transmitter/Receiver
- **Why**: Wireless control with two pins.
- **Considerations**: Potential 2.4 GHz interference; use shielding.

### Aluminum Heat Sink
- **Why**: Manages heat from ESP32 and MAX98357A.
- **Considerations**: Small size (~5 SEK) sufficient for ~1.7W load; ensure contact.

### 1A Fuse
- **Why**: Protects against PoE overcurrent.
- **Considerations**: Install in-line; replace if blown.

### OLED Display (0.96" I2C)
- **Why**: Visual status display using existing I2C.
- **Considerations**: Shares TCA9548A; ensure unique address (e.g., 0x3C).

### Jumper Wires
- **Why**: Facilitates internal connections in the box.
- **Considerations**: Use insulated wires to prevent shorts.

## Pin and Wiring Design

### Pin Assignment
- **Total Usable Pins**: ~14 GPIOs available on ESP32-WROVER-E (36 total, minus Ethernet simulation, USB, boot pins).
- **Pinout**:
  - GPIO 21: I2C SDA (to TCA9548A)
  - GPIO 22: I2C SCL (to TCA9548A)
  - GPIO 34: ADC (MQ135)
  - GPIO 25: Digital (DHT22)
  - GPIO 26: Digital (HC-SR501)
  - GPIO 27: Digital (Reed Switch with 10kΩ pull-up to 3.3V)
  - GPIO 32: I2S SCK (INMP441)
  - GPIO 33: I2S WS (INMP441)
  - GPIO 35: I2S SD (INMP441)
  - GPIO 12: 433 MHz TX
  - GPIO 13: 433 MHz RX
  - GPIO 15: I2S SCK (MAX98357A)
  - GPIO 16: I2S WS (MAX98357A)
  - GPIO 17: I2S SD (MAX98357A)
- **Spare Pins**: GPIO 4, 23, 36 (3 pins for LEDs or expansion).


[PoE Splitter (External)]
  | Cat6 ---- [1A Fuse] ---- 5V ----+-----------------+
  | GND --------------------------+-----------------+
                                   |
                                   v
+-------------------------------+
| Wall Box (60mm x 50mm)        |
|                               |
| [ESP32-WROVER-E]              |
|  - GPIO 21 ---- SDA --------- [TCA9548A]
|  - GPIO 22 ---- SCL --------- |  - CH0 ---- [CCS811]
|  - GPIO 34 ---- ADC --------- [MQ135]       |  - CH1 ---- [BH1750]
|  - GPIO 25 ---- Data -------- [DHT22]       |  - CH2 ---- [OLED]
|  - GPIO 26 ---- Signal ------ [HC-SR501]    |
|  - GPIO 27 ---- Signal ------ [Reed Switch] --- 10kΩ --- 3.3V
|  - GPIO 32 ---- SCK --------- [INMP441]
|  - GPIO 33 ---- WS ----------|
|  - GPIO 35 ---- SD ----------|
|  - GPIO 12 ---- TX ---------- [433 MHz]
|  - GPIO 13 ---- RX ----------|
|  - GPIO 15 ---- SCK --------- [MAX98357A] ---- [Speaker]
|  - GPIO 16 ---- WS ----------|
|  - GPIO 17 ---- SD ----------|
|  - 5V ----------------------+---- [MAX98357A, MQ135, HC-SR501, 433 MHz]
|  - 3.3V -------------------+---- [TCA9548A, DHT22, CCS811, BH1750, OLED, INMP441]
|  - GND -------------------+---- [All GNDs]
|  - Heat Sink ------------+---- [ESP32, MAX98357A]
|  - Vents ----------------+---- [Box Top/Bottom]
+-------------------------------+



+-------------------+
| PoE Switch        |
| (Cat6 Output)     |
+-------------------+
         |
         v
+-------------------+
| PoE Splitter      |
| (External)        |
| - 5V/2.4A        |
| - GND            |
+-------------------+
         |
         v
+-------------------+
| Wall Box (60x50mm)|
|                   |
| +---------------+ |
| | ESP32-WROVER-E| |
| |               | |- GPIO to Peripherals
| +---------------+ |
| | TCA9548A      | |- I2C Hub
| | (I2C Mux)     | |
| +---------------+ |
| | Sensors       | |- MQ135, DHT22, CCS811, BH1750
| |               | |
| +---------------+ |
| | I2S Devices   | |- INMP441, MAX98357A + Speaker
| |               | |
| +---------------+ |
| | 433 MHz Module| |
| +---------------+ |
| | OLED Display  | |
| +---------------+ |
| | Heat Sink     | |
| | Vents         | |
+-------------------+


ExplanationPoE Switch: External source providing Cat6 power.
PoE Splitter: External block converting Cat6 to 5V/GND, feeding the wall box.
Wall Box: Contains the ESP32 and all peripherals, organized into functional blocks (controller, I2C hub, sensors, audio, wireless, display, thermal management).
Flow: Power and data move from the splitter to the ESP32, then to peripherals via GPIOs.

Pin and Wiring DesignPin AssignmentTotal Usable Pins: ~14 GPIOs on ESP32-WROVER-E (36 total, minus Ethernet simulation, USB, boot pins like GPIO 0, 2, 15).
Pinout:GPIO 21: I2C SDA (to TCA9548A)
GPIO 22: I2C SCL (to TCA9548A)
GPIO 34: ADC (MQ135)
GPIO 25: Digital (DHT22)
GPIO 26: Digital (HC-SR501)
GPIO 27: Digital (Reed Switch with 10kΩ pull-up to 3.3V)
GPIO 32: I2S SCK (INMP441)
GPIO 33: I2S WS (INMP441)
GPIO 35: I2S SD (INMP441)
GPIO 12: 433 MHz TX
GPIO 13: 433 MHz RX
GPIO 15: I2S SCK (MAX98357A)
GPIO 16: I2S WS (MAX98357A)
GPIO 17: I2S SD (MAX98357A)

Spare Pins: GPIO 4, 23, 36 (3 pins for LEDs or expansion).

Wiring ConsiderationsPoE Splitter: Mount externally on the wall or in a slim enclosure. Connect Cat6 to the input, and route 5V/GND to the box via a 1A fused line using insulated wires or a connector (e.g., JST).
ESP32-WROVER-E: Use a breakout board (e.g., ESP32-DevKitC) inside the box, secured with standoffs. Wire GPIOs as per the pinout, avoiding GPIO 0, 2, 15 for peripherals unless boot-configured (e.g., pull-up on GPIO 15).
I2C Bus: Connect TCA9548A to GPIO 21/22, with CCS811 (CH0), BH1750 (CH1), and OLED (CH2) on separate channels. Add 4.7kΩ pull-up resistors between SDA/SCL and 3.3V.
I2S: Use shielded, twisted-pair cables for INMP441 (GPIO 32/33/35) and MAX98357A (GPIO 15/16/17) to reduce noise, grounding shields to ESP32 GND. Position the speaker facing outward.
Power Distribution: Route 5V from the splitter to MAX98357A, MQ135, HC-SR501, and 433 MHz; 3.3V (regulated by ESP32) to TCA9548A, DHT22, CCS811, BH1750, OLED, and INMP441. Tie all GNDs to a common bus.
Heat Management: Attach aluminum heat sinks to the ESP32 and MAX98357A. Drill 2mm vents on the box top and bottom for passive cooling, monitoring temperature with the DHT22.
Interference: Wrap 433 MHz wiring with foil or use a shielded cable to minimize 2.4 GHz Wi-Fi interference. Test VLAN separation with the managed switch.
Box Layout: Arrange components vertically—ESP32 and TCA9548A on one side, I2S devices and sensors on the other—ensuring the 50mm depth. Secure with adhesive or standoffs, keeping wires tidy with jumper wires or a mini breadboard.

Other DevicesESP32-C5 Nodes (Outdoor): Used for cameras, operating independently.
Raspberry Pi Zero 2 W (Entry Points and VPN): Manages VPN routing and DHCP for “UKNet,” connected via PoE and the managed switch.

This project focuses on the ESP32-PoE splitter node as the core unit, with other devices supporting specific functions.

---

### Notes
- **Wiring Diagram**: The revised text-based diagram uses a vertical layout to clarify power flow from the splitter to the ESP32 and peripherals, addressing the previous misalignment. It’s structured for easy schematic translation.
- **Miranda Diagram**: The block diagram is simplified with clear boundaries, showing the external splitter and internal box components, improving readability.
- **No Code**: The focus remains on hardware design, as requested.
- **Fit**: The PoE splitter externalizes power, allowing the ESP32-WROVER-E (~55mm x 29mm) and peripherals to fit inside with a better layout.

