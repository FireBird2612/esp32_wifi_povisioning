# Wi-Fi Provisioning Manager

- Wifi Provisioning is an ESP-IDF component that provides APIs which are responsible for controlling the Wi-Fi Provisioning service for receiving and configuring Wi-Fi credentials over SoftAP and Bluetooth LE transport via secure protocol communication sessions.
- You can refer to ESP-IDF documentation on [Wi-Fi Provisioning](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/provisioning/wifi_provisioning.html#_CPPv418wifi_prov_mgr_init22wifi_prov_mgr_config_t), for in-depth understanding

## Configuration and Initialization?
- Before you go ahead and initialize the provisioning manager, you need to configure few things:
    1. ESP-IDF
    2. NVS
    3. TCP/IP
    4. Event Loop
    5. Wi-Fi
- These are critical components which are required to configure and initialize before invoking `wifi_prov_mgr_init()`
---
### Configuration of Wi-Fi Provision Manager?

- It is required to set `scheme` and `scheme_event_handler` field.
- Since our application uses the BLE as a transport layer for handling the provision commands, the `scheme` field should be `wifi_prov_scheme_ble`.
- Depending upon wether the application requires BLE/Bluetooth Classic (if Bluedroid is the host) functionality, you can free the resources used by the BLE when after provisioning is finished and you have already established the connection with desired Wi-Fi network.
    - To do this set the `scheme_event_handler` with `WIFI_PROV_SCHEME_BLE_EVENT_HANDLER_FREE_BTDM`

wifi_prov_mgr_config_t config = {
    .scheme = wifi_prov_scheme_ble,                         
    .scheme_event_handler = WIFI_PROV_SCHEME_BLE_EVENT_HANDLER_FREE_BTDM
}
- Initialize Wi-Fi Provsisoning Service by calling `wifi_prov_mgr_init(config)`

### Check the Provision state
- You can check the provision state by calling the function `wifi_prov_mgr_is_provisioned()`
    