---
title: Kom igång med enhetstvillingar för Azure IoT Hub (Python) | Microsoft Docs
description: Så här använder enhetstvillingar för Azure IoT Hub för att lägga till taggar och sedan använda en IoT Hub-fråga. Du kan använda Azure IoT SDK för Python för att implementera app för simulerade enheter och en service-app som lägger till taggar och kör IoT Hub-frågan.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: kgremban
ms.openlocfilehash: 3e703c999d57cf62064291cf91059a17a959a2c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442639"
---
# <a name="get-started-with-device-twins-python"></a>Kom igång med enhetstvillingar (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I slutet av den här självstudien har du två Python-konsolappar:

* **AddTagsAndQuery.py**, en Python backend-app, som lägger till taggar och frågar enhetstvillingar.

* **ReportConnectivity.py**, en Python-app som simulerar en enhet som ansluter till din IoT-hubb med enhetsidentiteten som skapades tidigare och rapporterar tillståndet för anslutningen.

> [!NOTE]
> Artikeln [Azure IoT SDK: er](iot-hub-devguide-sdks.md) innehåller information om Azure IoT SDK: er som du kan använda för att skapa appar för både enheten och backend-server.

Den här kursen behöver du följande:

* [Python 2.x eller 3.x](https://www.python.org/downloads/). Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Se till att du lägger till Python i den plattformsspecifika miljövariabeln när du uppmanas att göra det under installationen. Om du använder Python 2.x kan du behöva [installera eller uppgradera *PIP* (pakethanteringssystemet för Python)](https://pip.pypa.io/en/stable/installing/).

* Om du använder Windows OS installerar du [Visual C++ redistributable package](https://www.microsoft.com/download/confirmation.aspx?id=48145) så att du kan använda native-DLL:er från Python.

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

> [!NOTE]
> *Pip*-paketet för `azure-iothub-service-client` och `azure-iothub-device-client` är endast tillgänglig för Windows-Operativsystemet. Linux/Mac OS, finns i Linux och Mac OS-specifika avsnitt på den [Förbered din utvecklingsmiljö för Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) publicera.
>

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Hämta anslutningssträngen för IoT-hubben

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-the-service-app"></a>Skapa service-app

I det här avsnittet skapar du en Python-konsolapp som lägger till platsmetadata i enhetstvillingen som är associerade med din **{enhets-ID}**. Därefter beordrar enhetstvillingar som lagras i IoT hub att välja de enheter som finns i Redmond och de som rapporterar en mobil anslutning.

1. Öppna en kommandotolk och installera **Azure IoT Hub Service SDK för Python**. Stäng kommandotolken när du har installerat SDK.

   ```
   pip install azure-iothub-service-client
   ```

2. Använd en textredigerare och skapa en ny **AddTagsAndQuery.py** fil.

3. Lägg till följande kod för att importera de moduler som krävs från SDK:n:

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Lägg till följande kod, Ersätt platshållarvärdet för `[IoTHub Connection String]` och `[Device Id]` med anslutningssträngen för IoT hub och enhets-ID som du skapade i föregående avsnitt.
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. Lägg till följande kod till den **AddTagsAndQuery.py** fil:

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

6. Lägg till följande kod i slutet av **AddTagsAndQuery.py** att implementera den **iothub_service_sample_run** funktionen:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. Kör programmet med:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Du bör se en enhet i resultaten för att fråga ställa för alla enheter i **Redmond43** och ingenting alls under den fråga som begränsar resultaten till enheter som använder ett mobilnät.

    ![första frågan visar alla i Redmond](./media/iot-hub-python-twin-getstarted/1-device-twins-python-service-sample.png)

I nästa avsnitt skapar du en app för enheter som rapporterar anslutningsinformation och ändras resultatet av frågan i föregående avsnitt.

## <a name="create-the-device-app"></a>Skapa en app för enheter

I det här avsnittet skapar du en Python-konsolapp som ansluter till hubben som din **{enhets-ID}**, och sedan de uppdateringar som dess enhetstvilling är rapporterade egenskaper som innehåller informationen om att den är ansluten med hjälp av ett mobilt nätverk.

1. Öppna en kommandotolk och installera **Azure IoT Hub Service SDK för Python**. Stäng kommandotolken när du har installerat SDK.

    ```
    pip install azure-iothub-device-client
    ```

2. Använd en textredigerare och skapa en ny **ReportConnectivity.py** fil.

3. Lägg till följande kod för att importera de moduler som krävs från SDK:n:

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

4. Lägg till följande kod, Ersätt platshållarvärdet för `[IoTHub Device Connection String]` med anslutningssträngen för IoT hub-enheter som du skapade i föregående avsnitt.

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

5. Lägg till följande kod till den **ReportConnectivity.py** fil att implementera enheten twins funktioner:

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

6. Lägg till följande kod i slutet av **ReportConnectivity.py** att implementera den **iothub_client_sample_run** funktionen:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

7. Kör app för enheter:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Du bör se en bekräftelse på enhetstvillingar har uppdaterats.

    ![Uppdatera twins](./media/iot-hub-python-twin-getstarted/2-python-client-sample.png)

8. Nu när enheten rapporteras dess anslutningsinformation som det visas i båda frågor. Gå tillbaka och köra frågor igen:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Den här gången din **{enhets-ID}** ska visas i båda frågeresultatet.

    ![andra frågan](./media/iot-hub-python-twin-getstarted/3-device-twins-python-service-sample.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du har lagts till enhetsmetadata som taggar från en backend-app och skrev en simulerad enhetsapp till rapporten anslutning enhetsinformation i enhetstvillingen. Du också lärt dig hur du frågar efter den här informationen med hjälp av registret.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med den [Kom igång med IoT Hub](quickstart-send-telemetry-python.md) självstudien.

* Konfigurera enheter som använder enhetstvillingens egenskaper med den [Använd önskade egenskaper för att konfigurera enheter](tutorial-device-twins.md) självstudien.

* Kontrollera enheter interaktivt (till exempel aktivera en fans, från en användarstyrd app) och med den [Använd direkta metoder](quickstart-control-device-python.md) självstudien.