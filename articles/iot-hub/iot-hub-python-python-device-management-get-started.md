---
title: Komma igång med Azure IoT Hub device management (Python) | Microsoft-dokument
description: Så här använder du IoT Hub-enhetshantering för att initiera en omstart av en fjärrenhet. Du använder Azure IoT SDK för Python för att implementera en simulerad enhetsapp som innehåller en direkt metod och en tjänstapp som anropar den direkta metoden.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: f376831175840284fdfd15f367542d33ad9f7177
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759770"
---
# <a name="get-started-with-device-management-python"></a>Komma igång med enhetshantering (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

I den här självstudiekursen lär du dig att:

* Använd Azure-portalen för att skapa en IoT-hubb och skapa en enhetsidentitet i din IoT-hubb.

* Skapa en simulerad enhetsapp som innehåller en direkt metod som startar om enheten. Direkta metoder anropas från molnet.

* Skapa en Python-konsolapp som anropar metoden starta om direkt i den simulerade enhetsappen via din IoT-hubb.

I slutet av den här självstudien har du två Python-konsolappar:

* **dmpatterns_getstarted_device.py**, som ansluter till din IoT-hubb med enhetsidentiteten som skapats tidigare, får en omstart direkt metod, simulerar en fysisk omstart och rapporterar tiden för den senaste omstarten.

* **dmpatterns_getstarted_service.py**, som anropar en direkt metod i den simulerade enhetsappen, visar svaret och visar de uppdaterade rapporterade egenskaperna.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Krav

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet får du:

* Skapa en Python-konsolapp som svarar på en direkt metod som anropas av molnet

* Simulera en omstart av en enhet

* Använd de rapporterade egenskaperna för att aktivera enhetstvillingfrågor för att identifiera enheter och när de senast startades om

1. Kör följande kommando för att installera **azure-iot-device-paketet i** kommandotolken:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Skapa en fil med namnet **dmpatterns_getstarted_device.py** i arbetskatalogen med hjälp av en textredigerare.

3. Lägg till `import` följande satser i början av filen **dmpatterns_getstarted_device.py.**

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Lägg till **variabeln CONNECTION_STRING.** Ersätt `{deviceConnectionString}` platshållarvärdet med enhetens anslutningssträng. Du har kopierat den här anslutningssträngen tidigare i [Registrera en ny enhet i IoT-hubben](#register-a-new-device-in-the-iot-hub).  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Lägg till följande funktionsinringningar för att implementera den direkta metoden på enheten.

    ```python
    def reboot_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("rebootDevice")  # blocking call

            # Act on the method by rebooting the device...
            print( "Rebooting device" )
            time.sleep(20)
            print( "Device rebooted")

            # ...and patching the reported properties
            current_time = str(datetime.datetime.now())
            reported_props = {"rebootTime": current_time}
            client.patch_twin_reported_properties(reported_props)
            print( "Device twins updated with latest rebootTime")

            # Send a method response indicating the method request was resolved
            resp_status = 200
            resp_payload = {"Response": "This is the response from the device"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

6. Starta direktmetodlyssnaren och vänta.

    ```python
    def iothub_client_init():
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            # Start a thread listening for "rebootDevice" direct method invocations
            reboot_listener_thread = threading.Thread(target=reboot_listener, args=(client,))
            reboot_listener_thread.daemon = True
            reboot_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Spara och stäng **filen dmpatterns_getstarted_device.py.**

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för återförsök (till exempel en exponentiell backoff), som föreslås i artikeln [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Utlösa en fjärromstart på enheten med en direkt metod

I det här avsnittet skapar du en Python-konsolapp som initierar en fjärrstart på en enhet med en direkt metod. Appen använder enhetstvillingfrågor för att identifiera den senaste omstartstiden för den enheten.

1. Kör följande kommando för att installera **azure-iot-hub-paketet** i kommandotolken:

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Skapa en fil med namnet **dmpatterns_getstarted_service.py** i arbetskatalogen med hjälp av en textredigerare.

3. Lägg till `import` följande satser i början av filen **dmpatterns_getstarted_service.py.**

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Lägg till följande variabeldeklarationer. Ersätt `{IoTHubConnectionString}` platshållarvärdet med anslutningssträngen för IoT-hubb som du kopierade tidigare i [Hämta anslutningssträngen för IoT-hubben](#get-the-iot-hub-connection-string). Ersätt `{deviceId}` platshållarvärdet med det enhets-ID som du registrerade i [Registrera en ny enhet i IoT-hubben](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Lägg till följande funktion för att anropa enhetsmetoden för att starta om målenheten, fråga sedan efter enhetstvillingarna och få den senaste omstartstiden.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            # Call the direct method.
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)
            response = registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = registry_manager.get_twin(DEVICE_ID)

                    if twin_info.properties.reported.get("rebootTime") != None :
                        print ("Last reboot time: " + twin_info.properties.reported.get("rebootTime"))
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}".format(ex) )
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

6. Spara och stäng **filen dmpatterns_getstarted_service.py.**

## <a name="run-the-apps"></a>Kör apparna

Du är nu redo att köra apparna.

1. Kör följande kommando i kommandotolken för att börja lyssna efter metoden starta om direkt.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. Vid en annan kommandotolk kör du följande kommando för att utlösa fjärrstarten och fråga efter enhetstvillingen för att hitta den senaste omstartstiden.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. Enhetssvaret på direktmetoden i konsolen visas.

   Följande visar enhetens svar på metoden reboot direct:

   ![Utdata för simulerad enhetsapp](./media/iot-hub-python-python-device-management-get-started/device.png)

   Följande visar tjänsten som anropar metoden reboot direct och avsöker enhetstvillingen efter status:

   ![Utdata för omstart av omstart](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]