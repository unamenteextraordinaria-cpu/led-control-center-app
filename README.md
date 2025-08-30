# ControlApp - ESP32 & Firebase Real-Time Control

This project is a full-stack web application that allows you to remotely control ESP32-based smart devices in real-time. It uses Firebase for backend services, ensuring instant state synchronization between the web interface and the physical hardware.

## Features

- **Real-Time Web Control:** Turn your devices on or off from a clean and responsive web interface.
- **Firebase Backend:** Leverages Firebase Authentication and Realtime Database for a secure and fast backend.
- **Physical Button Sync:** The state can also be toggled using physical buttons connected to the ESP32, with the changes instantly reflected in the web app.
- **Multi-Device Architecture:** The firmware is designed to handle multiple devices, each with its own state and controls. The app automatically detects and displays all configured devices.
- **Customizable UI:** Personalize the look of each device card in the app by changing its accent color.
- **Automatic Device Provisioning:** If a device configured in the ESP32 firmware doesn't exist in the Firebase database, the ESP32 will automatically create it on its first run.

## Tech Stack

- **Frontend:** Next.js, React, TypeScript, Tailwind CSS, ShadCN UI
- **Backend & Database:** Firebase (Authentication & Realtime Database)
- **Hardware:** ESP32 (tested with LOLIN D32)
- **Firmware:** C++ on the Arduino Framework with PlatformIO.

## How It Works

1.  **Web App:** The Next.js application allows users to log in and view their registered devices. It reads and writes device states to a specific path in the Firebase Realtime Database (`/users/<user_id>/devices/<device_id>`).
2.  **Firebase:** Acts as the central nervous system. It authenticates users and stores the state of all devices (e.g., `{ "isOn": true, "name": "Desk Lamp" }`).
3.  **ESP32 Firmware:** The ESP32 connects to WiFi and logs into Firebase as a specific user. It then opens a real-time "stream" for each configured device. When a value changes in the database for a device it's listening to, it triggers an action (like turning a pin HIGH or LOW). Conversely, when a physical button is pressed, the ESP32 updates the value in Firebase, which then updates the web app.

## Getting Started

### 1. Web Application

The web application is ready to be deployed. Ensure you have configured your Firebase project details in `src/lib/firebase.ts`.

### 2. ESP32 Firmware Setup

This project includes firmware for the ESP32. You will need [PlatformIO](https://platformio.org/) to compile and upload it.

1.  **Choose your firmware:**
    *   `src/esp32/firmware.cpp`: Pre-configured for a single device.
    *   `src/esp32/firmware_2_dispositivos.cpp`: Pre-configured for two devices.

2.  **Configure Credentials:** Open the `.cpp` file of your choice and fill in the required credentials at the top of the file:
    *   Your WiFi SSID and Password.
    *   Your Firebase `API_KEY` and `DATABASE_URL` (these are already synced with this project).
    *   A dedicated `USER_EMAIL` and `USER_PASSWORD` for the ESP32 to log in. **It is highly recommended to create a separate, dedicated user account in Firebase Authentication for your hardware.**

3.  **Configure Devices & Pins:** In the same file, modify the `devices` array to match your hardware setup. For each device, you need to define:
    *   `id`: A unique identifier (e.g., `"device_1"`). This must match what you want to see in the database.
    *   `controlPin`: The GPIO pin on the ESP32 that will control the device (e.g., a relay).
    *   `buttonPin`: The GPIO pin connected to a physical push-button.

4.  **PlatformIO Configuration:** Ensure your `platformio.ini` file in the root of your ESP32 project is configured correctly. The firmware is set up for a `lolin_d32` board.

    ```ini
    [env:lolin_d32]
    platform = espressif32
    board = lolin_d32
    framework = arduino
    lib_deps =
      mobizt/Firebase ESP32 Client@~4.4.1
    monitor_speed = 115200
    ```

5.  **Compile & Upload:** Use PlatformIO to upload the firmware to your ESP32. Open the Serial Monitor to see the connection status and real-time events.
