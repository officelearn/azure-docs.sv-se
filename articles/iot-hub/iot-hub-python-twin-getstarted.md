---
title: Kom igång med Azure IoT Hub Device Ungarns (python) | Microsoft Docs
description: 'Så här använder du Azure IoT Hub-enheten för att lägga till taggar och sedan använda en IoT Hub fråga. Du använder Azure IoT SDK: er för python för att implementera den simulerade Device-appen och en service-app som lägger till taggarna och kör IoT Hubs frågan.'
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: kgremban
ms.openlocfilehash: 53e3d32497c7aae6c584d23b9baddbaeaf1bd822
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405878"
---
# <a name="get-started-with-device-twins-python"></a>Kom igång med enhets garn (python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I slutet av den här självstudien kommer du att ha två python-konsol program:

* **AddTagsAndQuery.py**, en python-backend-app, som lägger till taggar och frågor enhets dubbla.

* **ReportConnectivity.py**, en python-app, som simulerar en enhet som ansluter till din IoT Hub med enhets identiteten som skapades tidigare och rapporterar dess anslutnings tillstånd.

> [!NOTE]
> Artikeln [Azure IoT SDK](iot-hub-devguide-sdks.md) : er innehåller information om Azure IoT SDK: er som du kan använda för att bygga både enhets-och backend-appar.

För att slutföra den här självstudien behöver du följande:

* [Python 2. x eller 3. x](https://www.python.org/downloads/). Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Se till att du lägger till Python i den plattformsspecifika miljövariabeln när du uppmanas att göra det under installationen. Om du använder Python 2.x kan du behöva [installera eller uppgradera *PIP* (pakethanteringssystemet för Python)](https://pip.pypa.io/en/stable/installing/).

* Om du använder Windows OS installerar du [Visual C++ redistributable package](https://www.microsoft.com/download/confirmation.aspx?id=48145) så att du kan använda native-DLL:er från Python.

* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

> [!NOTE]
> *Pip*-paketet för `azure-iothub-service-client` och `azure-iothub-device-client` är endast tillgänglig för Windows-Operativsystemet. För Linux/Mac OS, se avsnitten Linux och Mac OS-vissa i avsnittet [förbereda din utvecklings miljö för python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) post.
>

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Skapa tjänst appen

I det här avsnittet skapar du en python-konsol-app som lägger till platsens metadata till den enhet som är kopplad till **{Device ID}** . Den frågar sedan enheten efter varandra i IoT Hub och väljer enheterna som finns i Redmond och sedan de som rapporterar en mobil anslutning.

1. Öppna en kommandotolk och installera **Azure IoT Hub Service SDK för Python**. Stäng kommandotolken när du har installerat SDK.

   ```
   pip install azure-iothub-service-client
   ```

2. Skapa en ny **AddTagsAndQuery.py** -fil med hjälp av en text redigerare.

3. Lägg till följande kod för att importera de moduler som krävs från SDK:n:

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Lägg till följande kod och ersätt plats hållaren `[IoTHub Connection String]` för `[Device Id]` och med anslutnings strängen för IoT Hub och enhets-ID: t som du skapade i föregående avsnitt.
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. Lägg till följande kod i **AddTagsAndQuery.py** -filen:

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

    Objektet **Registry** visar alla de metoder som krävs för att interagera med enheten tillsammans från tjänsten. Koden initierar först **Registry** -objektet, uppdaterar sedan enheten till **deviceId**och kör slutligen två frågor. Först väljer du endast enheten med dubbla enheter som finns i **Redmond43** -anläggningen och den andra refinar frågan så att endast de enheter som också är anslutna via mobil nät verket återställs.

6. Lägg till följande kod i slutet av **AddTagsAndQuery.py** för att implementera funktionen **iothub_service_sample_run** :

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. Kör programmet med:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Du bör se en enhet i resultatet för frågan som frågar efter alla enheter som finns i **Redmond43** och ingen för den fråga som begränsar resultatet till enheter som använder ett mobil nät verk.

    ![första frågan som visar alla enheter i Redmond](./media/iot-hub-python-twin-getstarted/1-device-twins-python-service-sample.png)

I nästa avsnitt skapar du en enhets app som rapporterar anslutnings informationen och ändrar resultatet för frågan i föregående avsnitt.

## <a name="create-the-device-app"></a>Skapa enhets appen

I det här avsnittet ska du skapa en python-konsol-app som ansluter till din hubb som **{Device ID}** och sedan uppdaterar dess enhets dubbla rapporter som innehåller den information som den är ansluten till med ett mobilt nätverk.

1. Öppna en kommandotolk och installera **Azure IoT Hub Service SDK för Python**. Stäng kommandotolken när du har installerat SDK.

    ```
    pip install azure-iothub-device-client
    ```

2. Skapa en ny **ReportConnectivity.py** -fil med hjälp av en text redigerare.

3. Lägg till följande kod för att importera de moduler som krävs från SDK:n:

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

4. Lägg till följande kod och ersätt plats hållaren `[IoTHub Device Connection String]` för med anslutnings strängen för IoT Hub-enheten som du skapade i föregående avsnitt.

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

5. Lägg till följande kod i **ReportConnectivity.py** -filen för att implementera enhetens dubbla funktioner:

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

    **Klient** objekt visar alla metoder som du behöver för att interagera med enheten tillsammans från enheten. Föregående kod, efter att den initierat **klient** objekt, hämtar enheten till enheten och uppdaterar den rapporterade egenskapen med anslutnings informationen.

6. Lägg till följande kod i slutet av **ReportConnectivity.py** för att implementera funktionen **iothub_client_sample_run** :

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

7. Kör enhets appen:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Du bör se en bekräftelse på att enheten har uppdaterats.

    ![uppdatera dubbla](./media/iot-hub-python-twin-getstarted/2-python-client-sample.png)

8. Nu när enheten rapporterade anslutnings information, bör den visas i båda frågorna. Gå tillbaka och kör frågorna igen:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Den här gången ska **{Device ID}** visas i båda frågeresultaten.

    ![andra frågan](./media/iot-hub-python-twin-getstarted/3-device-twins-python-service-sample.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du har lagt till metadata för enheten som taggar från en backend-app och skrev en simulerad Device-app för att rapportera enhetens anslutnings information på enheten. Du har också lärt dig hur du frågar den här informationen med hjälp av registret.

Använd följande resurser för att lära dig att:

* Skicka telemetri från enheter med självstudierna [Kom igång med IoT Hub](quickstart-send-telemetry-python.md) .

* Konfigurera enheter med enhetens dubbla egenskaper med hjälp av självstudierna [Använd önskade egenskaper för att konfigurera enheter](tutorial-device-twins.md) .

* Kontrol lera enheter interaktivt (t. ex. genom att aktivera en fläkt från en användardefinierad app) med självstudierna [Använd direkt metoder](quickstart-control-device-python.md) .