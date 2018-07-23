---
title: Kom igång med enhetstvillingar för Azure IoT Hub (Python) | Microsoft Docs
description: Så här använder enhetstvillingar för Azure IoT Hub för att lägga till taggar och sedan använda en IoT Hub-fråga. Du kan använda Azure IoT SDK för Python för att implementera app för simulerade enheter och en service-app som lägger till taggar och kör IoT Hub-frågan.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: kgremban
ms.openlocfilehash: e75b6bbd688ae6e4422d5caba160095763e84994
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185626"
---
# <a name="get-started-with-device-twins-python"></a>Kom igång med enhetstvillingar (Python)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I slutet av den här självstudien har du två Python-konsolappar:

* **AddTagsAndQuery.py**, en Python backend-app, som lägger till taggar och frågar enhetstvillingar.
* **ReportConnectivity.py**, en Python-app som simulerar en enhet som ansluter till din IoT-hubb med enhetsidentiteten som skapades tidigare och rapporterar tillståndet för anslutningen.

> [!NOTE]
> Artikeln [Azure IoT SDK: er] [ lnk-hub-sdks] innehåller information om Azure IoT SDK: er som du kan använda för att skapa appar för både enheten och backend-server.
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

## <a name="create-the-service-app"></a>Skapa service-app
I det här avsnittet skapar du en Python-konsolapp som lägger till platsmetadata i enhetstvillingen som är associerade med din **{enhets-Id}**. Därefter beordrar enhetstvillingar som lagras i IoT hub att välja de enheter som finns i Redmond och de som rapporterar en mobil anslutning.

1. Öppna en kommandotolk och installera **Azure IoT Hub Service SDK för Python**. Stäng kommandotolken när du har installerat SDK.

    ```
    pip install azure-iothub-service-client
    ```

1. Använd en textredigerare och skapa en ny **AddTagsAndQuery.py** fil.

3. Lägg till följande kod för att importera de moduler som krävs från SDK:n:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubError
    ```
2. Lägg till följande kod, Ersätt platshållarvärdet för `[IoTHub Connection String]` och `[Device Id]` med anslutningssträngen för IoT hub och enhets-id som du skapade i föregående avsnitt.
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

1. Lägg till följande kod till den **AddTagsAndQuery.py** fil:
   
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
   
    Den **registret** objektet innehåller alla metoder som krävs för att interagera med enhetstvillingar från tjänsten. Koden först initierar den **registret** objekt och uppdateringar i enhetstvillingen för **deviceId**, och slutligen körs två frågor. Först väljer endast enhetstvillingar av enheter som finns i den **Redmond43** anläggning och andra förfinar fråga för att välja endast de enheter som även är anslutna via mobilnät.
   
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
   
    Du bör se en enhet i resultaten för att fråga ställa för alla enheter i **Redmond43** och ingenting alls under den fråga som begränsar resultaten till enheter som använder ett mobilnät.
   
    ![första frågan][1]

I nästa avsnitt skapar du en app för enheter som rapporterar anslutningsinformation och ändras resultatet av frågan i föregående avsnitt.

## <a name="create-the-device-app"></a>Skapa en app för enheter
I det här avsnittet skapar du en Python-konsolapp som ansluter till hubben som din **{enhets-Id}**, och sedan de uppdateringar som dess enhetstvilling är rapporterade egenskaper som innehåller informationen om att den är ansluten med hjälp av ett mobilt nätverk.

1. Öppna en kommandotolk och installera **Azure IoT Hub Service SDK för Python**. Stäng kommandotolken när du har installerat SDK.

    ```
    pip install azure-iothub-device-client
    ```

1. Använd en textredigerare och skapa en ny **ReportConnectivity.py** fil.

3. Lägg till följande kod för att importera de moduler som krävs från SDK:n:

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

2. Lägg till följande kod, Ersätt platshållarvärdet för `[IoTHub Device Connection String]` med anslutningssträngen för IoT hub-enheter som du skapade i föregående avsnitt.
   
    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

1. Lägg till följande kod till den **ReportConnectivity.py** fil att implementera enheten twins funktioner:

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

    Den **klienten** objektet innehåller alla metoder som du behöver för att interagera med enhetstvillingar från enheten. Föregående kod när den initierar den **klienten** objekt, hämtar enhetstvillingen för din enhet och uppdaterar sin rapporterad egenskap med anslutningsinformation.

1. Lägg till följande kod i slutet av **ReportConnectivity.py** att implementera den **iothub_client_sample_run** funktionen:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

1. Kör app för enheter
   
    ```cmd/sh
    python ReportConnectivity.py
    ```
   
    Du bör se en bekräftelse på enhetstvillingar har uppdaterats.

    ![Uppdatera twins][2]

6. Nu när enheten rapporteras dess anslutningsinformation som det visas i båda frågor. Gå tillbaka och köra frågor igen:
   
    ```cmd/sh
    python AddTagsAndQuery.py
    ```
   
    Den här gången din **{enhets-Id}** ska visas i båda frågeresultatet.
   
    ![andra frågan][3]

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du har lagts till enhetsmetadata som taggar från en backend-app och skrev en simulerad enhetsapp till rapporten anslutning enhetsinformation i enhetstvillingen. Du också lärt dig hur du frågar efter den här informationen med hjälp av registret.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med den [Kom igång med IoT Hub] [ lnk-iothub-getstarted] självstudien
* Konfigurera enheter som använder enhetstvillingens egenskaper med den [Använd önskade egenskaper för att konfigurera enheter] [ lnk-twin-how-to-configure] självstudien
* Kontrollera enheter interaktivt (till exempel aktivera en fans, från en användarstyrd app) och med den [Använd direkta metoder] [ lnk-methods-tutorial] självstudien.

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

[lnk-iothub-getstarted]: quickstart-send-telemetry-python.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/quickstart-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: tutorial-device-twins.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: quickstart-control-device-node.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
