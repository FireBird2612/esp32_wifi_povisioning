# Code Exaplanation of app_main.c

## Required header files?
Following are the header files which are required for using desired peripheral API.

**1. FreeRTOS:**

```c
    #include <freertos/FreeRTOS.h>
    #include <freertos/task.h>
    #include <freertos/event_groups.h>
```

ESP-IDF uses the FreeRTOS real-time operating system as the core for managing tasks, events, and synchronization within ESP32 applications.

``#include <freertos/FreeRTOS.h>``
- This is the main FreeRTOS header file that provides core definitions, macros, and functions used by the FreeRTOS kernel. 
- It is required in any application using FreeRTOS functionalities.
- Refer the header file [here](https://github.com/FreeRTOS/FreeRTOS-Kernel/blob/main/include/FreeRTOS.h)

``#include <freertos/task.h>``
- This file provides the API functions related to ***task creation***, ***deletion***, and ***management*** in **FreeRTOS**.
- Refer the header file [here](https://github.com/FreeRTOS/FreeRTOS-Kernel/blob/main/include/task.h)

``#include <freertos/event_groups.h>``
- This file contains the API functions for managing event groups, which are used for task synchronization via flags.
- Refer the header file [here](https://github.com/FreeRTOS/FreeRTOS-Kernel/blob/main/include/event_groups.h)


**2. ESP based header files:**

```c
    #include <esp_log.h>
    #include <esp_wifi.h>
    #include <esp_event.h>
    #include <nvs_flash.h>
    #include <wifi_provisioning/manager.h>
```

``#include <esp_log.h>``
- Used for logging and debugging purposes.
- It provides a set of functions and macros to log messages with varying levels of severity and detailed information, making it easier to monitor and debug your application.
- Refer the header file [here](https://github.com/espressif/esp-idf/blob/master/components/log/include/esp_log.h)

``#include <esp_wifi.h>``
- provides functions, data types, and structures for controlling the Wi-Fi functionality of the ESP32.
- This header is part of the ESP-IDF's Wi-Fi stack and allows you to configure, manage, and operate Wi-Fi connections in both Station (STA) and Access Point (AP) modes.
- Refere the header file [here](https://github.com/espressif/esp-idf/blob/master/components/esp_wifi/include/esp_wifi.h)

``#include <esp_event.h>``
- It is part of the event loop library and is used for event handling.
- It provides APIs to define, register, and manage events in the ESP32 system, allowing for asynchronous event-driven programming. 
- This is essential for managing various events like Wi-Fi connections, disconnections, Bluetooth events, or even custom application events.
- Read [here](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/system/esp_event.html) for in-depth understanding.
- Refer the header file [here](https://github.com/espressif/esp-idf/blob/v5.3.1/components/esp_event/include/esp_event.h)

``#include <nvs_flash.h>``
- It is the part of the Non-volatile Storage (NVS) Library.
- It provides functions to store and retrieve data in the non-volatile flash memory of the ESP32, allowing data to be retained across device resets or power cycles.
- NVS is particularly useful for storing configuration data, calibration values, Wi-Fi credentials, and other small pieces of information that need to persist.
- Read [here](https://docs.espressif.com/projects/esp-idf/en/v4.2/esp32/api-reference/storage/nvs_flash.html) for in-depth understanding.
- Refer the header file[here](https://github.com/espressif/esp-idf/blob/v4.2/components/nvs_flash/include/nvs_flash.h)

``#include <wifi_provisioning/manager.h>``
- This library is used to simplify the process of provisioning Wi-Fi credentials for an ESP32 device.
- It provides a mechanism to set up a device on a network by allowing users to input their network credentials (SSID, password) using various transport mechanisms like BLE(in our case) or SoftAP.
- Read [here](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/provisioning/wifi_provisioning.html#wifi-prov-check-state) for in-depth understanding.
- Refer the header file [here](https://github.com/espressif/esp-idf/blob/v5.3.1/components/wifi_provisioning/include/wifi_provisioning/manager.h)

``#include <wifi_provisioning/scheme_ble.h>``
- It is used specifically for Wi-Fi provisioning via Bluetooth Low Energy (BLE).
- This header provides functions and configurations to set up the ESP32 for provisioning over BLE, which is one of the supported transport schemes (the other common option being SoftAP).
- Refer [here](https://github.com/espressif/esp-idf/blob/master/components/wifi_provisioning/include/wifi_provisioning/scheme_ble.h)


``#include "qrcode.h"``
- This header file is included in the managed_components directory which is repsonsible for generating qrcode.








