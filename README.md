       CAN-CONTROLLED AUTOMOTIVE INFO DISPLAY

Overview

This project demonstrates a multi-node CAN communication system using the LPC2148 ARM7 microcontroller.
It includes:

 - Main Node (m_node): temperature monitoring, RTC display, indicator control, and receiving fuel information.

- Fuel Node: monitoring fuel level via ADC and transmitting to the main node.

- Indicator Node: controlling indicator LEDs based on CAN messages from the main node.

The nodes communicate over Controller Area Network (CAN), with each performing a distinct role in a distributed embedded system.

CAN_Project/
├── m_node.c          # Main node (temperature + RTC + indicator control + CAN Tx/Rx)
├── fuel_node.c       # Fuel monitoring node (ADC + CAN Tx)
├── indicator_node.c  # Indicator node (CAN Rx + LED indicators)
├── adc.c / adc.h     # ADC driver
├── can.c / can_defines.h # CAN driver and configuration
├── lcd.c / lcd.h     # LCD driver for display (used by main node)
├── rtc.c / rtc.h     # RTC driver (used by main node)
├── *.uvproj, *.uvopt # Keil µVision project files

Main Node (m_node)

Role: Central controller of the system.

Tasks:

1.Reads temperature sensor (via ADC).

2.Reads RTC (time, date, day).

3.Displays temperature + RTC information on LCD.

4.Sends indicator signals to Indicator Node when an interrupt is generated (e.g., button/keypad press).

5.Displays indicator status on LCD along with temperature and RTC.

6.Receives fuel data from Fuel Node via CAN and displays it on LCD.

Flow:

- Initialize ADC, RTC, LCD, and CAN.

- Continuous loop:
a) Read temperature + RTC → show on LCD.
b) Receive fuel data from Fuel Node → update LCD.
c) If interrupt triggered → send indicator command via CAN → update LCD with indicator status.

Fuel Node
Role: Monitors fuel level and sends to Main Node.
Tasks:
1. Reads fuel gauge sensor using ADC (on-chip).
2. Continuously transmits fuel level to Main Node via CAN.
Flow:
- Initialize ADC and CAN.
- Continuous loop:
 a) Read fuel sensor ADC value.
 b) Transmit to Main Node over CAN.

Indicator Node

Role: Controls vehicle-like indicators.

Tasks:

1.Continuously waits for CAN data from Main Node.

2.On receiving data, control LEDs connected to the microcontroller to represent indicator ON/OFF status.

Flow:

Initialize CAN and GPIO for LEDs.

Continuous loop:
a) Wait for CAN Rx frame from Main Node.
b) If indicator command received → turn ON/OFF respective LEDs.

Supporting Code
ADC (adc.c, adc.h, adc_defines.h):
- Initializes LPC2148 ADC peripheral.
- Provides functions to read analog sensor values.
- Used in main node (temperature) and fuel node (fuel sensor).
CAN (can.c, can_defines.h):
- Initializes CAN controller with defined bit rate (e.g., 125 kbps).
- Provides APIs for CAN transmit and receive.
- Used across all nodes for communication.
LCD (lcd.c, lcd.h):
- Used only by main node.
- Displays temperature, RTC, fuel data, and indicator status.
RTC (rtc.c, rtc.h):
- Reads real-time clock (time, date, day).
- Data shown on LCD by main node.

System Flow
Temperature Sensor → Main Node → LCD
RTC → Main Node → LCD
Fuel Sensor → Fuel Node → CAN → Main Node → LCD
Interrupt → Main Node → CAN → Indicator Node → LED
- Main Node: Central hub (temperature, RTC, fuel display, indicator control).
- Fuel Node: Sends fuel data to Main Node.
- Indicator Node: Receives indicator commands and controls LEDs.

Requirements
Hardware:
- LPC2148 boards (at least 3 for nodes).
- MCP2551 or similar CAN transceivers.
- Temperature and fuel (analog) sensors.
- LCD module for main node.
- LEDs for indicator node.
Software:
- Keil µVision IDE.
- Flash Magic (for flashing code).

Build & Run

1.Open respective .uvproj in Keil µVision.

2.Build the code for m_node, fuel_node, and indicator_node separately.

3.Flash the binaries to different LPC2148 boards.

4.Connect all boards to the same CAN bus.

5.Observe:

- Main node shows temperature + RTC on LCD.

- Fuel node sends fuel data to main node → main node shows on LCD.

- On interrupt at main node, indicator status sent via CAN → LEDs controlled at indicator node and LCD updated.

Testing

- Change temperature input → confirm updates on LCD.

- Change fuel sensor input → confirm updates on LCD via CAN.

- Trigger interrupt (button/keypad) → confirm indicator LEDs at Indicator Node and LCD update at Main Node.

- Verify CAN frames using CAN analyzer.

Known Limitations
- Indicators limited to LED representation (scalable to automotive relays).
- Designed for Keil µVision; GCC users need porting.

Author
Oruganti Jayaraju.
