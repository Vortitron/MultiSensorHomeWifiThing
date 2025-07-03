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

## Components
- **Olimex ESP32-PoE-EA-16MB-WROVER**: Main microcontroller with 16 MB flash, 8 MB PSRAM, external antenna, and PoE support.
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

## Why We Chose the Components and Considerations

### Olimex ESP32-PoE-EA-16MB-WROVER
- **Why**: Chosen for its PoE and Ethernet integration, external antenna for better range in concrete walls, and the WROVER variant’s 8 MB PSRAM for memory-intensive tasks like ESP-Spotify-Connect. The 16 MB flash supports complex firmware.
- **Considerations**: The non-isolated version saves ~70 SEK per unit (~560 SEK total) since USB and PoE won’t be used simultaneously. The USB-to-UART interface reduces available pins to ~12, requiring careful allocation. Heat management is critical in the 60mm x 50mm box, mitigated with a heat sink.

### TCA9548A I2C Multiplexer
- **Why**: Enables multiple I2C devices (CCS811, BH1750, OLED) to share two pins (SDA/SCL), freeing GPIO resources. Cost-effective at ~5–10 SEK per unit.
- **Considerations**: Requires I2C address management to avoid conflicts. Adds minor latency (~1–2 ms) when switching channels, acceptable for sensor polling. Fits the box size with proper mounting.

### MQ135 Gas Sensor
- **Why**: Provides air quality monitoring (CO2, VOCs) for ventilation control, using a single analog pin.
- **Considerations**: Requires calibration for accuracy. ADC pin usage (e.g., GPIO 34) is limited to one sensor due to ESP32 constraints.

### DHT22 Temperature and Humidity Sensor
- **Why**: Reliable digital sensor for climate monitoring, using one GPIO pin.
- **Considerations**: Slow response (~2s) is acceptable for home use. Ensure proper placement to avoid heat from the box affecting readings.

### CCS811 Air Quality Sensor
- **Why**: Offers precise CO2 and TVOC detection via I2C, complementing MQ135.
- **Considerations**: Shares I2C bus with BH1750 and OLED, managed by the TCA9548A. Requires periodic baseline calibration.

### BH1750 Light Sensor
- **Why**: Provides accurate ambient light measurement via I2C, useful for automation (e.g., lighting control).
- **Considerations**: I2C address must differ from CCS811; the multiplexer handles this. Sensitivity may vary in concrete rooms.

### HC-SR501 PIR Motion Sensor
- **Why**: Detects occupancy with one digital pin, enhancing security/automation.
- **Considerations**: False positives possible near heat sources; position away from the box.

### Reed Switch
- **Why**: Monitors door/window status with one digital pin, ideal for security.
- **Considerations**: Requires a 10kΩ pull-up resistor. Ensure magnet alignment for reliable triggering.

### INMP441 MEMS Microphone
- **Why**: Delivers I2S input for voice commands, using three pins for high-quality audio.
- **Considerations**: Requires careful placement to capture clear voice input. I2S bus must be isolated from output to avoid feedback.

### MAX98357A I2S Amplifier
- **Why**: Provides I2S audio output for voice and music, using three pins with the 5V speaker.
- **Considerations**: Generates heat (~0.5–1W); pair with a heat sink. Ensure 3.3V logic level compatibility with ESP32.

### 5V 3W 8Ω Speaker
- **Why**: Affordable audio output for voice assistant and music streaming.
- **Considerations**: Volume limited by box size and power; avoid overdriving to prevent distortion.

### 433 MHz Transmitter/Receiver
- **Why**: Enables wireless control (e.g., lights) with two pins.
- **Considerations**: Interference possible on 2.4 GHz; use shielding or adjust frequency if needed.

### Aluminum Heat Sink
- **Why**: Manages heat from the ESP32 and MAX98357A in the confined box.
- **Considerations**: Small size (~5 SEK) sufficient for ~1.7W total load. Ensure good thermal contact.

### 1A Fuse
- **Why**: Protects against PoE overcurrent, enhancing safety.
- **Considerations**: Install in-line with Cat6 wiring; replace if blown to avoid downtime.

### OLED Display (0.96" I2C)
- **Why**: Adds a visual status display (e.g., temperature, music status) using the existing I2C bus.
- **Considerations**: Shares TCA9548A channels with other I2C devices; ensure unique address (e.g., 0x3C). Low power (~0.1W) fits the load.

## Pin and Wiring Design
- **Total Usable Pins**: ~12 GPIO pins available on the ESP32-PoE-EA-16MB-WROVER, accounting for Ethernet, PoE, USB-to-UART, and boot constraints.
- **Pin Assignment**:
  - GPIO 21: I2C SDA (to TCA9548A).
  - GPIO 22: I2C SCL (to TCA9548A).
  - GPIO 34: ADC (MQ135).
  - GPIO 25: Digital (DHT22).
  - GPIO 26: Digital (HC-SR501).
  - GPIO 27: Digital (Reed Switch with 10kΩ pull-up).
  - GPIO 32: I2S SCK (INMP441).
  - GPIO 33: I2S WS (INMP441).
  - GPIO 35: I2S SD (INMP441).
  - GPIO 12: 433 MHz TX.
  - GPIO 13: 433 MHz RX.
  - GPIO 15: I2S SCK (MAX98357A).
  - GPIO 16: I2S WS (MAX98357A).
  - GPIO 17: I2S SD (MAX98357A).
- **Wiring Design**:
  - **I2C Bus**: Connect TCA9548A to GPIO 21/22, with CCS811, BH1750, and OLED on separate channels (e.g., 0x5A, 0x23, 0x3C). Use 4.7kΩ pull-up resistors on SDA/SCL.
  - **I2S**: Route INMP441 (GPIO 32, 33, 35) and MAX98357A (GPIO 15, 16, 17) with shielded cables to reduce noise. Ensure 3.3V logic levels.
  - **Power**: Connect PoE (Cat6) to the board’s RJ45 jack, with a 1A fuse in-line. Power MAX98357A and speaker from the 5V PoE output via the board’s regulator.
  - **Heat Management**: Attach a heat sink to the ESP32 and MAX98357A. Drill 2mm vents in the wall box for passive cooling.
  - **Placement**: Mount the ESP32 vertically, with the antenna oriented for optimal 2.4 GHz coverage. Secure the TCA9548A and sensors with adhesive or standoffs.
- **Considerations**:
  - **Pin Constraints**: Zero spare pins; the TCA9548A ensures I2C devices share GPIO 21/22. Avoid using GPIO 0, 2, 15 for peripherals unless configured for boot stability.
  - **Wiring**: Use jumper wires or a custom PCB to minimize clutter. Insulate all connections to prevent shorts in the tight space.
  - **Heat**: Monitor internal temperature (~60–75°C expected) with the DHT22; adjust ventilation if nearing 85°C.
  - **Interference**: Shield 433 MHz wiring to avoid 2.4 GHz Wi-Fi conflicts. Test VLAN traffic separation with the managed switch.

## Other Devices
- **ESP32-C5 Nodes (Outdoor)**: Used for cameras, operating independently on 2.4/5 GHz.
- **Raspberry Pi Zero 2 W (Entry Points and VPN)**: Handles VPN routing and DHCP for “UKNet,” connected via PoE and the managed switch.

This project focuses on the ESP32-PoE node as the core unit, with other devices supporting specific functions.
