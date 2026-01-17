# robocup2026-esp32-kanto

> **⚠️ ARCHIVE NOTICE**: This repository is being archived. The project has moved to a new repository: [techno-robocup/robocup2026-esp32-program](https://github.com/techno-robocup/robocup2026-esp32-program)
>
> Please use the new repository for future development and contributions.

## Overview

This repository contains ESP32 firmware for the RoboCup 2026 Kanto project. It's an embedded robotics system that controls motors, servos, and sensors on an ESP32 microcontroller using the Arduino framework with FreeRTOS for real-time task management.

## Features

- **Motor Control**: PWM-based control for 4 independent motors (tyres)
- **Servo Control**: PD controller for precise arm positioning with feedback
- **Sensor Integration**: 
  - 3 ultrasonic sensors for distance measurement
  - Wire detection sensor
  - Button input
- **Serial Communication**: Message-based protocol for command and telemetry
- **Real-time Operation**: FreeRTOS tasks for concurrent, deterministic control
- **Thread Safety**: Mutex-based synchronization for shared data access

## Hardware Configuration

### Pin Mapping

| Component | Pin(s) | Description |
|-----------|--------|-------------|
| Motors (Tyres) | 13, 14, 15, 16 | PWM control for 4 wheels |
| Button | 21 | Digital input |
| Arm Servo Pulse | 17 | PWM output for servo control |
| Arm Feedback | 34 | Analog input for position feedback |
| Wire Sensor | 32 | Digital input for wire detection |
| Ultrasonic 1 | 18 (trig), 19 (echo) | Distance sensor front |
| Ultrasonic 2 | 22 (trig), 23 (echo) | Distance sensor middle |
| Ultrasonic 3 | 26 (trig), 27 (echo) | Distance sensor rear |

### Specifications

- **Platform**: ESP32 (espressif32)
- **Framework**: Arduino
- **Serial Baud Rate**: 9600
- **Motor PWM Interval**: 40ms
- **Servo PWM**: ~50Hz (20ms period), 1000-2000μs pulse width

## Technology Stack

- **C++11** with Arduino extensions
- **PlatformIO** for build management
- **FreeRTOS** for task scheduling and synchronization
- **ESP32Servo** library (v3.0.8) for servo control

## Project Structure

```
├── src/                    # Main application code
│   └── main.cpp           # Entry point with setup() and loop()
├── lib/                    # Custom libraries
│   ├── armio/             # Arm servo control with PD controller
│   ├── motorio/           # Motor PWM control
│   ├── mutex_guard/       # RAII mutex wrapper for thread safety
│   ├── serialio/          # Serial communication with message protocol
│   └── usonicio/          # Ultrasonic sensor interface
├── include/               # Public header files
├── test/                  # Test directory
├── platformio.ini         # PlatformIO configuration
├── .clang-format          # Code formatting rules
└── format.sh              # Script to format all code files
```

## Development

### Prerequisites

- [PlatformIO](https://platformio.org/) installed
- ESP32 development board
- USB cable for programming and serial communication

### Building

```bash
# Build the project
pio run
```

### Uploading to ESP32

```bash
# Upload firmware to connected ESP32
pio run --target upload
```

### Serial Monitor

```bash
# Open serial monitor to view output and send commands
pio device monitor
```

### Code Formatting

Always format code before committing:

```bash
./format.sh
```

This runs `clang-format` on all C++ files using the Google style guide configuration.

## Libraries

### MOTORIO
Motor control class with PWM output and timing interval management.

**Usage:**
```cpp
MOTORIO motor(pin, interval_ms);
motor.run_msec(pulse_width);  // 1000-2000μs
```

### ARMIO
Servo arm control with PD (Proportional-Derivative) controller for precise positioning.

**Features:**
- Feedback-based position control
- Wire sensor integration
- Automatic PD controller updates

**Usage:**
```cpp
ARMIO arm(pulse_pin, feedback_pin, wire_sig_pin);
arm.init_pwm();
arm.arm_set_position(target, enable);
arm.updatePD();  // Call regularly to update controller
```

### UltrasonicIO
Ultrasonic distance sensor interface using trigger/echo pins.

**Usage:**
```cpp
UltrasonicIO sensor(trig_pin, echo_pin);
long distance;
sensor.readUsonic(&distance);
```

### SerialIO
Message-based serial communication protocol.

**Features:**
- Structured message format with ID and payload
- Non-blocking message checking
- Bidirectional communication

**Usage:**
```cpp
SerialIO serial;
serial.init();

// Sending
Message msg(id, "payload");
serial.sendMessage(msg);

// Receiving
if (serial.isMessageAvailable()) {
    Message received = serial.receiveMessage();
}
```

### MutexGuard
RAII-style mutex wrapper for automatic lock/unlock.

**Usage:**
```cpp
{
    MutexGuard guard(semaphore);
    // Critical section - mutex automatically released on scope exit
}
```

## Serial Protocol

The system communicates via serial at 9600 baud using a message format:
- Motor commands: `ID:M,value1,value2,value3,value4`
- Arm commands: `ID:A,position,enable`
- Status queries: System responds with sensor data

## Concurrency Model

The firmware uses FreeRTOS for concurrent operation:
- **Main Loop**: Handles serial communication, sensor readings
- **Motor Task**: Runs at 15ms intervals for motor control
- **Mutex Protection**: Shared data (motor values) protected with semaphores

## Code Style

- **Style Guide**: Google C++ Style Guide
- **Indentation**: 2 spaces (no tabs)
- **Line Length**: 100 characters maximum
- **Header Guards**: Both `#pragma once` and traditional guards

## License

This project is licensed under the GNU General Public License v3.0. See [LICENSE](LICENSE) for details.

## Contributing

**Note**: This repository is being archived. Please direct all contributions to the new repository: [techno-robocup/robocup2026-esp32-program](https://github.com/techno-robocup/robocup2026-esp32-program)

## Acknowledgments

Developed for RoboCup 2026 Kanto project.
