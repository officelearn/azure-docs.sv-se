---
title: "Kom igång med Azure IoT Hub enhetshantering (Python) | Microsoft Docs"
description: "Hur du använder IoT-hubb enhetshantering för att initiera en omstart av fjärranslutna enheter. Azure IoT-SDK för Python används för att implementera en simulerad enhetsapp som innehåller en direkt metod och en service-appen som anropar metoden direkt."
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/02/2018
ms.author: v-masebo
ms.openlocfilehash: b9dfbd775de3bb54e405e4054bca4e2b878305a3
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2018
---
# <a name="get-started-with-device-management-python"></a>Komma igång med hantering av enheter (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använda Azure portal för att skapa en IoT-hubb och skapa en enhetsidentitet i din IoT-hubb.
* Skapa en simulerad enhetsapp som innehåller en direkt metod som startar om enheten. Direkta metoder anropas från molnet.
* Skapa en Python-konsolapp som anropar metoden omstart direkt i appen simulerade enheten via din IoT-hubb.

I slutet av den här kursen har du två Python konsolappar:

**dmpatterns_getstarted_device.PY**, som ansluter till din IoT-hubb med enhetens identitet skapade tidigare, tar emot en direkt metod för omstart, simulerar en fysisk omstart och rapporterar tid för den senaste omstarten.

**dmpatterns_getstarted_service.PY**, som anropar en direkt metod i appen simulerade enheten visar svaret och visar den uppdaterade rapporterade egenskaper.

För att kunna genomföra den här kursen behöver du följande:

* [Python 2.x eller 3.x][lnk-python-download]. Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Se till att du lägger till Python i den plattformsspecifika miljövariabeln när du uppmanas att göra det under installationen. Om du använder Python 2.x kan du behöva [installera eller uppgradera *PIP* (pakethanteringssystemet för Python)][lnk-install-pip].
    * Installera den [azure-iothub-enhet-klient](https://pypi.org/project/azure-iothub-device-client/) paketet med hjälp av kommandot`pip install azure-iothub-device-client`
    * Installera den [azure-iothub-service-klient](https://pypi.org/project/azure-iothub-service-client/) paketet med hjälp av kommandot`pip install azure-iothub-service-client`
* Om du använder Windows OS installerar du [Visual C++ redistributable package][lnk-visual-c-redist] så att du kan använda native-DLL:er från Python.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp
I det här avsnittet kommer du att:

* Skapa en Python-konsolapp som svarar på en direkt metod som anropas av molnet
* Simulera en omstart av enheten
* Använda egenskaperna rapporterade för att aktivera enheten dubbla frågor för att identifiera enheter och när de senaste startas om

1. Med hjälp av en textredigerare, skapa en **dmpatterns_getstarted_device.py** fil.

1. Lägg till följande `import` instruktioner i början av den **dmpatterns_getstarted_device.py** fil.
   
    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

1. Lägga till variabler, inklusive en **anslutningssträng** variabeln och klient-start.  Ersätt anslutningssträngen med anslutningssträngen enhet.  
   
    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    SEND_REPORTED_STATE_CALLBACKS = 0
    METHOD_CALLBACKS = 0
    ```

1. Lägg till följande funktion-återanrop för att implementera metoden direkt på enheten.
   
    ```python
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS
    
        print ( "Device twins updated." )

    def device_method_callback(method_name, payload, user_context):
        global METHOD_CALLBACKS
    
        if method_name == "rebootDevice":
            print ( "Rebooting device..." )
        
            time.sleep(20)
        
            print ( "Device rebooted." )
        
            current_time = str(datetime.datetime.now())
            reported_state = "{\"rebootTime\":\"" + current_time + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        
            print ( "Updating device twins: rebootTime" )
            
        device_method_return_value = DeviceMethodReturnValue()
        device_method_return_value.response = "{ \"Response\": \"This is the response from the device\" }"
        device_method_return_value.status = 200
    
        return device_method_return_value
    ```

1. Starta lyssnaren direkta metoden och vänta.
   
    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
        
    def iothub_client_sample_run():
        try:
            iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Spara och Stäng den **dmpatterns_getstarted_device.py** fil.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff), vilket rekommenderas i MSDN-artikeln om [hantering av tillfälliga fel][lnk-transient-faults].


## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlös en fjärransluten omstart på enheten med en direkt metod
I det här avsnittet skapar du en Python-konsolapp som initierar en fjärransluten omstart på en enhet med en direkt metod. Appen använder enheten dubbla frågor för att identifiera senast omstart för enheten.

1. Med hjälp av en textredigerare, skapa en **dmpatterns_getstarted_service.py** fil.

1. Lägg till följande `import` instruktioner i början av den **dmpatterns_getstarted_service.py** fil.
   
    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

1. Lägg till följande variabeldeklarationer. Endast ersätta platshållarvärdena för _IoTHubConnectionString_ och _deviceId_.
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. Lägg till följande funktion för att anropa metoden enheten för att starta om målenheten och fråga efter enhet twins och få senast omstart.
   
    ```python
    def iothub_devicemethod_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
        
            print ( "" )
            print ( "Invoking device to reboot..." )

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )
        
            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = iothub_twin_method.get_twin(DEVICE_ID)
                
                    if twin_info.find("rebootTime") != -1:
                        print ( "Last reboot time: " + twin_info[twin_info.find("rebootTime")+11:twin_info.find("rebootTime")+37])
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

1. Spara och Stäng den **dmpatterns_getstarted_service.py** fil.


## <a name="run-the-apps"></a>Kör apparna
Nu är det dags att köra apparna.

1. I Kommandotolken kör följande kommando för att börja lyssna efter omstart direkta metoden.
   
    ```
    python dmpatterns_getstarted_device.py
    ```

1. I en annan kommandotolk, kör du följande kommando för att utlösa remote omstart och fråga för enheten dubbla att söka efter senaste omstart tid.
   
    ```
    python dmpatterns_getstarted_service.py
    ```

1. Svaret från enheten till den direkta metoden i konsolen visas.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
