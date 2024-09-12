# Code Exaplanation of app_main.c

## Required header files?
- Following are the header files which are required for using desired peripheral API.

    #include <stdio.h>
    #include <string.h>
    #include <freertos/FreeRTOS.h>
    #include <freertos/task.h>
    #include <freertos/event_groups.h>
    
    #include <esp_log.h>
    #include <esp_wifi.h>
    #include <esp_event.h>
    #include <nvs_flash.h>
    #include <wifi_provisioning/manager.h>

