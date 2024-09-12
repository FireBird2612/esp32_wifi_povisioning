# app_main.c Wi-Fi Provisioning Manager Example
## Overview
This example demonstrates how to use the ESP-IDF Wi-Fi provisioning manager to enable a device to be provisioned over Wi-Fi using different transport layers such as BLE and SoftAP, we use BLE as our transport layer.
It includes handling events, initializing Wi-Fi, and generating a QR code for provisioning.

## Includes
Refer this [documentation](https://github.com/FireBird2612/esp32_wifi_povisioning/blob/main/docs/code_explanation.md) which explains the header files used and references.

## Global Variables

```c
    static const char *TAG = "app"; // Log tag for the application

    static int wifi_conn_count; // Count for Wi-Fi connection attempts

#if CONFIG_EXAMPLE_PROV_SECURITY_VERSION_2
#if CONFIG_EXAMPLE_PROV_SEC2_DEV_MODE
#define EXAMPLE_PROV_SEC2_USERNAME "wifiprov"
#define EXAMPLE_PROV_SEC2_PWD "abcd1234"

    // Salt and verifier for security version 2
    static const char sec2_salt[] = { /* Salt values */ };
    static const char sec2_verifier[] = { /* Verifier values */ };

    static esp_err_t example_get_sec2_salt(const char **salt, uint16_t *salt_len) { /* Implementation */ }
    static esp_err_t example_get_sec2_verifier(const char **verifier, uint16_t *verifier_len) { /* Implementation */ }
#endif
#endif
```

- TAG: Used for logging.
- wifi_conn_count: Tracks Wi-Fi connection attempts.
- Security Version 2: Defines salt and verifier for development mode.


## Event Handlers

```c
    const int WIFI_CONNECTED_EVENT = BIT0; // Event bit for Wi-Fi connection
    static EventGroupHandle_t wifi_event_group;

    static void event_handler(void* arg, esp_event_base_t event_base, int32_t event_id, void* event_data) {
        // Handles different events related to provisioning and Wi-Fi.
    }
```
- WIFI_CONNECTED_EVENT: Defines an event bit for signaling Wi-Fi connection status.
- wifi_event_group: Event group for managing Wi-Fi events.
- event_handler: Function to handle events related to Wi-Fi provisioning and connectivity.


## Wifi Initialization

```c
static void wifi_init_sta(void) {
    ESP_ERROR_CHECK(esp_wifi_set_mode(WIFI_MODE_STA)); // Set Wi-Fi mode to STA
    ESP_ERROR_CHECK(esp_wifi_start()); // Start Wi-Fi
}
```
- Initializes Wi-Fi in station mode.


## Device Service Name

```c
static void get_device_service_name(char *service_name, size_t max)
{
        uint8_t eth_mac[6];
        const char *ssid_prefix = "PROV_";
        esp_wifi_get_mac(WIFI_IF_STA, eth_mac);
        snprintf(service_name, max, "%s%02X%02X%02X",
                ssid_prefix, eth_mac[3], eth_mac[4], eth_mac[5]);
}
```

- Get_device_service_name: Constructs a unique service name using the device's MAC address.

## Provisioning Data Handler

```c
esp_err_t custom_prov_data_handler(uint32_t session_id, const uint8_t *inbuf, ssize_t inlen, uint8_t **outbuf, ssize_t *outlen, void *priv_data)
{
        if (inbuf) {
            ESP_LOGI(TAG, "Received data: %.*s", inlen, (char *)inbuf);
        }
        char response[] = "SUCCESS";
        *outbuf = (uint8_t *)strdup(response);
        if (*outbuf == NULL) {
            ESP_LOGE(TAG, "System out of memory");
            return ESP_ERR_NO_MEM;
        }
        *outlen = strlen(response) + 1; /* +1 for NULL terminating byte */

        return ESP_OK;
}
```

Handles custom provisioning data and sends a "SUCCESS" response.
Handler for the optional provisioning endpoint registered by the application.
The data format can be chosen by applications. 
Here, we are using plain ascii text.
Applications can choose to use other formats like protobuf, JSON, XML, etc.
    

## QR Code Generation

```c
static void wifi_prov_print_qr(const char *name, const char *username, const char *pop, const char *transport)
{
        if (!name || !transport) {
            ESP_LOGW(TAG, "Cannot generate QR code payload. Data missing.");
            return;
        }
        char payload[150] = {0};
        if (pop) {
    #if CONFIG_EXAMPLE_PROV_SECURITY_VERSION_1
            snprintf(payload, sizeof(payload), "{\"ver\":\"%s\",\"name\":\"%s\"" \
                        ",\"pop\":\"%s\",\"transport\":\"%s\"}",
                        PROV_QR_VERSION, name, pop, transport);
    #elif CONFIG_EXAMPLE_PROV_SECURITY_VERSION_2
            snprintf(payload, sizeof(payload), "{\"ver\":\"%s\",\"name\":\"%s\"" \
                        ",\"username\":\"%s\",\"pop\":\"%s\",\"transport\":\"%s\"}",
                        PROV_QR_VERSION, name, username, pop, transport);
    #endif
        } else {
            snprintf(payload, sizeof(payload), "{\"ver\":\"%s\",\"name\":\"%s\"" \
                        ",\"transport\":\"%s\"}",
                        PROV_QR_VERSION, name, transport);
        }
    #ifdef CONFIG_EXAMPLE_PROV_SHOW_QR
        ESP_LOGI(TAG, "Scan this QR code from the provisioning application for Provisioning.");
        esp_qrcode_config_t cfg = ESP_QRCODE_CONFIG_DEFAULT();
        esp_qrcode_generate(&cfg, payload);
    #endif /* CONFIG_APP_WIFI_PROV_SHOW_QR */
        ESP_LOGI(TAG, "If QR code is not visible, copy paste the below URL in a browser.\n%s?data=%s", QRCODE_BASE_URL, payload);
}
```

- wifi_prov_print_qr: Generates and displays a QR code for Wi-Fi provisioning.


## Bringing it all together - main()

1. Configuration on NVS:
    **Q.** Why do we need to initialize the NVS flash?
    - Since, we need to store the credentials received from the BLE, we store this credentials in the section of the flash called NVS?
    - NVS uses a portion of main flash memory through the esp_partition API.
    - The Non-volatile storage (NVS) library is designed to store key-value pairs in flash.
    - Even after reprogramming unless you are not manually erasing the NVS flash the data stored in the flash will still be retained.

2. Initialization of TCP/IP:
    - Initialize the network interface layer, which is responsible for managing network connections and data transmission.
    - This function is typically called at the beginning of your application to set up the basic network infrastructure.
    - This network interface will be used to communicate with the BLE for provisioning and Wi-Fi for data transmission and reception.
    - Once the network interface is initialized, the ESP32 can start sending and receiving data over the network using the TCP/IP stack.

3. Initialization of Event loop:
    - We need to initialize the event loop which will handle various asynchronous events from Wi-Fi, Provision Manager, Security, BLE, IP.
    - We do this by creating a event group by calling an API ``xEventGroupCreate()``
    - This will manage synchronization between different tasks or threads.
    - To conclude, with the help of the event loop, we can signal other tasks for completion, waiting, coordinating the flow of daata between network stack and the main application logic.

4. Registration of ``event_handler()`` for Wi-Fi, IP and Provisioning related events:
    - Unless and until we do not register these events with the handler being used we will not be able to perform desired synchronization.
    - To do this we take the help of ``esp_event_handler_regiter`` API.

5. Initialization of wifi and netif with default configuration:
    - The netif was already initialized in step 2, which is like getting all the utensils ready and the environment setup for us to start cooking. 
    - Now, we will create a network interface for WiFi station, which will manage the network connection and dtat transmssion for the WiFi. To do all of this we call the API, `` esp_netif_create_default_wifi_sta()``
    - Since we will be provisioning over BLE as our transport layer, we are setting up our WiFi in station mode with default setting ``wifi_init_config_t cfg = WIFI_INIT_CONFIG_DEFAULT()`` so that once we get the credentials from the user over BLE we can use those credentials to connect to the AP.
    - Make a call to ``esp_wifi_init(&cfg)`` to initialize the WiFi with our default configuration.

6. Configuration of Wi-Fi Provisioning Manager:
    - Phew! Finally, we can configure the Wi-Fi Provisioning Manager.
    - The provisoning scheme we are using is ``wifi_prov_scheme_ble`` so that can provision wifi credentials over ble.
    - We also need to configure the event handler for the ``wifi_prov_scheme_ble`` scheme which is required so that we can free the resource that was allocated for the BLE for provisioning and we only want to free the resource after we meet these two conditions:
        1. If your application does not require BLE or Bluetooth Classic functionality
        2. After the provisioning is finished.
    - The first condition is quite simple and straight forward and can be acheived by ``scheme_event_handler = WIFI_PROV_SCHEME_BLE_EVENT_HANDLER_FREE_BTDM``.
    Q. What about the 2nd condition? 
    -  It will be taken care of once we are able to successfully provisioned thanks to the power of event_hander (synchrhonisation!)
        
    - but before you go ahead and initialize the provisioning manager, you need to configure few things:
        1. NVS
        2. TCP/IP
        3. Event Loop
        4. Wi-Fi
        - These are critical components which are required to configure and initialize before invoking `wifi_prov_mgr_init()`
 
    - Checking to see if the Wi-Fi Provisioning Manager is already provisioned.
    - How?
    - By calling the API ``wifi_prov_mgr_is_provisioned()``, this will ultimately check wether we have wifi credentials stored in the NVS.
        - If not provisioned:
            - To provison, we need to start the provisoining service and to do that again we need to configure few things such as device name for BLE provisoning.
            - But it is not as simple as that, since we will transmit wifi credentials over BLE we need to take good measure to secure this transmission session only and only if you don't want someone to take control of your wifi network through poor security measures.
            - The program is configured with ``PROV_SECURITY_VERSION_1``
            - Basically, there are types of security level to choose from 0, 1 and 2. 
            - These security measures are all based upon ``protocom`` manageer.
            - You can refer [Unified Provisioning](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/provisioning/provisioning.html)
            - The service key should be NULL.
            - For the scheme, ``wifi_prov_scheme_ble``, a custom 128bit UUID should be configured when adverstising for BLE. 
            - Additionally, we can also create endpoints to transmit/receive custom data, in our case the ESP32 scans for the wifi networks and sends these wifi networks to the smartphone for us to select and share the credentials from.
            - Additionally, for reprovisioning by invoking the API, ``wifi_prov_mgr_disable_auto_stop()`` we can disable the auto stop feature so that we can provision again.
            - Now, can make a call to ``wifi_prov_mgr_start_provisioning()`` to start provisoning.
            - The provisioning service automatically finishes only if it receives valid Wi-Fi AP credentials followed by successful connection of device to the AP with IP obtained.

        - If provisioned:
            - Release the resources since, we don't the wifi provisioning manager.
            - and start the wifi in station mode to connect to AP as per received credentials.

7. We wait for Wi-Fi station to connect to the AP.
8. Since we have configured for reprovisioning
    - If the wifi connection to the previously provisioned network credentials fails even after trying to attempt the connection for 'n' number of times, due to various reasons such as network not available, or not it range, the program will then restart the provisioning process again by reset the wifi credentials stored into NVS, initializing the Wi-Fi Provisioning Manager, etc.
    - All of this process occurs at runtime and you are not limited to connect to only one single network once provisioned.
