
# Project Overview: Wifi Provisioning Using BLE on ESP32

This project demonstrates Wi-Fi provisioning for an ESP32 device using Bluetooth Low Energy (BLE) as the provisioning method. The project uses the Wi-Fi Provisioning Manager (wifi_prov_mgr) provided by ESP-IDF to easily manage and simplify the process of transmitting Wi-Fi credentials (SSID and password) from a mobile device to the ESP32 over BLE.

In many IoT applications, devices need to connect to a Wi-Fi network but do not have a user interface for manually entering network credentials. This project resolves that by leveraging BLE, allowing users to provision their ESP32 devices from a mobile app.

The Wi-Fi Provisioning Manager abstracts the complexity of BLE and Wi-Fi communication and provides a straightforward API to manage the provisioning process. Once the credentials are sent and validated, the ESP32 device connects to the specified Wi-Fi network, enabling it to perform its designated tasks.

## Key Features

- **BLE-based Wi-Fi Provisioning:** Allows users to input Wi-Fi credentials using a BLE-enabled mobile device (smartphone or tablet).
- **Wi-Fi Provisioning Manager:** Simplifies the provisioning process by managing the state transitions, credential storage, and connection retries.
- **Mobile App Support:** Compatible with the __ESP BLE Provisioning mobile app__ for seamless credential entry.
- **Provisioning State Feedback:** Provides real-time feedback to users about the provisioning status (e.g., success, failure, or connection attempts).
- **Event Handling:** Supports event callbacks to monitor the provisioning process and handle events such as successful connection or credential errors.
- **Security:** Uses BLE for secure transmission of Wi-Fi credentials, with an option for secure session initialization.

## Use Case

This project is ideal for IoT devices that require network connectivity but lack a display or user interface for manual configuration. Example applications include smart home devices, sensors, and connected appliances. By using BLE for provisioning, end-users can quickly and securely connect their devices to a Wi-Fi network using just their smartphones.

## How it Works

1. **BLE Setup:** The ESP32 device starts in BLE mode, broadcasting a provisioning service.
2. **Mobile App Interaction:** The user connects to the ESP32 using the ESP BLE Provisioning mobile app, where they input the Wi-Fi credentials.
3. **Wi-Fi Connection:** The credentials are transmitted over BLE to the ESP32, which then attempts to connect to the Wi-Fi network.
4. **Provisioning Status:** The mobile app displays the status of the provisioning process (e.g., connected or failed), and the ESP32 stores the credentials for future connections.

# Prerequisites

Before you begin with the Wi-Fi Provisioning project, ensure you have the following hardware, software, and tools in place:
1. **Hardware Requirements:**
    - **Example:** [ESP32-DevKitC](https://www.mouser.in/c/embedded-solutions/engineering-tools/rf-wireless-development-tools/wifi-development-tools-80211/?q=ESP32-DevKitC-) or any other ESP32-based board with BLE and Wi-Fi capabilities.
    - **USB Cable:** To connect the ESP32 development board to your computer for programming and debugging.
    - **Smartphone or Tablet:** A mobile device with Bluetooth Low Energy (BLE) capability to provision Wi-Fi credentials using the ESP BLE Provisioning app.

2. **Software Requirements:**
    - **ESP-IDF (Espressif IoT Development Framework)**
        - Ensure you have latest ESP-IDF version installed on your development or host machine.
        - Refer to this [document](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/get-started/index.html) from the Espressif to install ESP-IDF on your host machine.
    - **ESP BLE Provisioning Mobile Application**
        - Available for both Android and iOS devices.
            - [Android App](https://play.google.com/store/apps/details?id=com.espressif.provble&pli=1)
            - [iOS App](https://apps.apple.com/in/app/esp-ble-provisioning/id1473590141)
    - **Libraries/Components**
        - Ensure that you have included wifi_prov_mgr component in your ESP-IDF project.
        - For more information: [Wi-Fi Provisioning Manager](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/provisioning/wifi_provisioning.html#id12)

# Quick Start

1. Clone the Repo
2. Navigate to the project directory
3. Set the target board using the idf.py set-target esp32
4. configure the project by using idf.py menuconfig
5. Build and flash the project

6. Provisioning the Device Using BLE
Open the ESP BLE Provisioning App:
Install and open the ESP BLE Provisioning App (available for both Android and iOS).
Connect to the ESP32:
After resetting or starting the ESP32, it will broadcast a BLE service for provisioning. Open the app, and it should detect your ESP32 device.
Enter Wi-Fi Credentials:
Follow the app’s prompts to enter the SSID and password of the Wi-Fi network you want the ESP32 to connect to.
Provisioning Status:
The ESP32 will attempt to connect to the provided Wi-Fi network. You can monitor the status through the mobile app and the serial monitor.

7. Success!
Once the Wi-Fi credentials are successfully provisioned, the ESP32 will connect to the network. You can verify the connection in the serial monitor logs, where it will display the device’s IP address.

# Features
