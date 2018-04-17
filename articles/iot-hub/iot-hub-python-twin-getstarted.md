---
title: Kom igång med Azure IoT Hub-enhet twins (Python) | Microsoft Docs
description: Hur du använder Azure IoT Hub-enhet twins att lägga till taggar och sedan använda en IoT-hubb-fråga. Azure IoT-SDK för Python används för att implementera simulerade enheten appen och en service-app som lägger till taggar och IoT-hubb-frågan körs.
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: ''
ms.assetid: 314c88e4-cce1-441c-b75a-d2e08e39ae7d
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2017
ms.author: kgremban
ms.openlocfilehash: 44b913d5f30a8465219a6793f48d82cfe39d12b2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-device-twins-python"></a>Kom igång med enheten twins (Python)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I slutet av den här kursen har du två Python konsolappar:

* **AddTagsAndQuery.py**, en Python backend-app som lägger till taggar och frågar enheten twins.
* **ReportConnectivity.py**, en Python-app som simulerar en enhet som ansluter till din IoT-hubb med enhetens identitet skapade tidigare och rapporterar tillståndet anslutningen.

> [!NOTE]
> Artikeln [Azure IoT SDK] [ lnk-hub-sdks] innehåller information om Azure IoT-SDK: er som du kan använda för att skapa både enheten och backend-appar.
> 
> 

Den här kursen behöver du följande:

* [Python 2.x eller 3.x][lnk-python-download]. Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Se till att du lägger till Python i den plattformsspecifika miljövariabeln när du uppmanas att göra det under installationen. Om du använder Python 2.x kan du behöva [installera eller uppgradera *PIP* (pakethanteringssystemet för Python)][lnk-install-pip].
* Om du använder Windows OS installerar du [Visual C++ redistributable package][lnk-visual-c-redist] så att du kan använda native-DLL:er från Python.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

> [!NOTE]
> *Pip*-paketet för `azure-iothub-service-client` och `azure-iothub-device-client` är endast tillgänglig för Windows-Operativsystemet. Information om Linux/Mac OS x finns i Linux- och Mac OS-specifika avsnitt på posten [Förbered din utvecklingsmiljö för Python][lnk-python-devbox] .
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>Skapa service-appen
I det här avsnittet skapar du en Python-konsolapp som lägger till platsen metadata i enheten-dubbla som är associerade med din **{enhets-Id}**. Frågar sedan enheten-twins lagras i IoT-hubb som att markera de enheter som finns i Redmond och de som rapporterar en mobil anslutning.

1. Öppna en kommandotolk och installera **Azure IoT Hub Service SDK för Python**. Stäng kommandotolken när du har installerat SDK.

    ```
    pip install azure-iothub-service-client
    ```

1. Med hjälp av en textredigerare, skapa en ny **AddTagsAndQuery.py** fil.

3. Lägg till följande kod för att importera de moduler som krävs från SDK:n:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubError
    ```
2. Lägg till följande kod, ersätta platshållaren för `[IoTHub Connection String]` och `[Device Id]` med anslutningssträngen för IoT-hubb och enhets-id som du skapade i föregående avsnitt.
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

1. Lägg till följande kod i den **AddTagsAndQuery.py** fil:
   
     ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
        
            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)
        
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)
        
            print ( "Devices in Redmond: " )        
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)
         
                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )
        
            print ( "" )
        
            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)
                
                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```
   
    Den **registret** objektet innehåller de metoder som krävs för att interagera med enheten twins från tjänsten. Koden initierar först den **registret** objekt, uppdateras sedan enheten dubbla för **deviceId**, och slutligen körs två frågor. Först väljer enheten twins av enheter som finns i den **Redmond43** anläggningen och andra förfinar frågan för att markera de enheter som även är anslutna via mobilnät.
   
1. Lägg till följande kod i slutet av **AddTagsAndQuery.py** att implementera den **iothub_service_sample_run** funktionen:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

1. Kör programmet med:
   
    ```cmd/sh
    python AddTagsAndQuery.py
    ```
   
    Du bör se en enhet i resultaten för frågan ställa för alla enheter i **Redmond43** och ingen för frågan som begränsar resultat till enheter som använder ett mobilnät.
   
    ![första frågan][1]

I nästa avsnitt skapar du en enhetsapp som rapporterar information om anslutningar och ändras resultatet av frågan i föregående avsnitt.

## <a name="create-the-device-app"></a>Skapa enhetsapp
I det här avsnittet skapar du en Python-konsolapp som ansluter till din hubb som din **{enhets-Id}**, och sedan uppdaterar sin enhet dubbla har rapporterat att innehålla den information som den är ansluten med hjälp av ett mobilnät.

1. Öppna en kommandotolk och installera **Azure IoT Hub Service SDK för Python**. Stäng kommandotolken när du har installerat SDK.

    ```
    pip install azure-iothub-device-client
    ```

1. Med hjälp av en textredigerare, skapa en ny **ReportConnectivity.py** fil.

3. Lägg till följande kod för att importera de moduler som krävs från SDK:n:

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

2. Lägg till följande kod, ersätta platshållaren för `[IoTHub Device Connection String]` med anslutningssträngen för IoT hub-enhet som du skapade i föregående avsnitt.
   
    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

1. Lägg till följande kod i den **ReportConnectivity.py** fil att implementera enheten twins funktioner:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "" )
        print ( "Twin callback called with:" )
        print ( "    updateStatus: %s" % update_state )
        print ( "    payload: %s" % payload )

    def send_reported_state_callback(status_code, user_context):
        print ( "" )
        print ( "Confirmation for reported state called with:" )
        print ( "    status_code: %d" % status_code )

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_twin_callback(
                device_twin_callback, TWIN_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            if client.protocol == IoTHubTransportProvider.MQTT:
                print ( "Sending data as reported property..." )

                reported_state = "{\"connectivity\":\"cellular\"}"

                client.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "Press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= TIMER_COUNT:
                    status = client.get_send_status()
                    time.sleep(10)
                    status_counter += 1 
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```   

    Den **klienten** objektet innehåller de metoder som du behöver för att interagera med enheten twins från enheten. Föregående kod när den initieras den **klienten** objekt, hämtar enheten dubbla för din enhet och uppdaterar egenskapen rapporterade med information om anslutningar.

1. Lägg till följande kod i slutet av **ReportConnectivity.py** att implementera den **iothub_client_sample_run** funktionen:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

1. Kör enhetsapp
   
    ```cmd/sh
    python ReportConnectivity.py
    ```
   
    Du bör se en bekräftelse enheten twins har uppdaterats.

    ![Uppdatera twins][2]

6. Nu när enheten rapporteras dess anslutningsinformation, bör den visas i båda frågor. Gå tillbaka och kör frågorna igen:
   
    ```cmd/sh
    python AddTagsAndQuery.py
    ```
   
    Den här gången din **{enhets-Id}** ska visas i båda frågeresultaten.
   
    ![andra frågan][3]

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du lagt till enhetsmetadata som taggar från en backend-app och skrev en simulerad enhetsapp till enheten anslutningsinformation i dubbla för enheten. Du också fått lära dig hur man frågar den här informationen med hjälp av registret.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med den [Kom igång med IoT-hubb] [ lnk-iothub-getstarted] självstudiekursen
* Konfigurera enheter med hjälp av enheten två egenskaper med den [Använd önskad egenskaper att konfigurera enheter] [ lnk-twin-how-to-configure] självstudiekursen
* Kontrollera enheter interaktivt (till exempel aktivera fläktar från en användare-kontrollerade app), med den [metoder från direkt] [ lnk-methods-tutorial] kursen.

<!-- images -->
[1]: media/iot-hub-python-twin-getstarted/1.png
[2]: media/iot-hub-python-twin-getstarted/2.png
[3]: media/iot-hub-python-twin-getstarted/3.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-python-devbox]: https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-python-getstarted.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
