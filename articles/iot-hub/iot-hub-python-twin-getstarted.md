---
title: Komma igång med Azure IoT Hub-enhetstvillingar (Python) | Microsoft-dokument
description: Så här använder du Azure IoT Hub-enhetstvillingar för att lägga till taggar och sedan använda en IoT Hub-fråga. Du använder Azure IoT SDK:er för Python för att implementera den simulerade enhetsappen och en tjänstapp som lägger till taggarna och kör IoT Hub-frågan.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 0bb1371de827fbb68afd5d114f49afa4acec0deb
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759728"
---
# <a name="get-started-with-device-twins-python"></a>Komma igång med enhetstvillingar (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I slutet av den här självstudien har du två Python-konsolappar:

* **AddTagsAndQuery.py**, en Python back-end-app, som lägger till taggar och frågor enhet tvillingar.

* **ReportConnectivity.py**, en Python-app, som simulerar en enhet som ansluter till din IoT-hubb med enhetsidentiteten som skapats tidigare, och rapporterar dess anslutningstillstånd.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Krav

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Skapa tjänstappen

I det här avsnittet skapar du en Python-konsolapp som lägger till platsmetadata till enhetstvillingen som är associerad med **ditt {Device ID}**. Sedan frågar de enhetstvillingar som lagras i IoT-hubben och väljer de enheter som finns i Redmond och sedan de som rapporterar en mobilanslutning.

1. Öppna en kommandotolk i arbetskatalogen och installera **Azure IoT Hub Service SDK för Python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

2. Skapa en ny **AddTagsAndQuery.py-fil** med hjälp av en textredigerare.

3. Lägg till följande kod för att importera de moduler som krävs från SDK:n:

   ```python
   import sys
   from time import sleep
   from azure.iot.hub import IoTHubRegistryManager
   from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult
   ```

4. Lägg till följande kod. Ersätt `[IoTHub Connection String]` med anslutningssträngen för IoT-hubb som du kopierade i [Hämta anslutningssträngen för IoT-hubben](#get-the-iot-hub-connection-string). Ersätt `[Device Id]` med det enhets-ID som du registrerade i [Registrera en ny enhet i IoT-hubben](#register-a-new-device-in-the-iot-hub).
  
    ```python
    IOTHUB_CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"
    ```

5. Lägg till följande **AddTagsAndQuery.py** kod i AddTagsAndQuery.py-filen:

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)

            new_tags = {
                    'location' : {
                        'region' : 'US',
                        'plant' : 'Redmond43'
                    }
                }

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(tags=new_tags, properties= TwinProperties(desired={'power_level' : 1}))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            # Add a delay to account for any latency before executing the query
            sleep(1)

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant: {}".format(', '.join([twin.device_id for twin in query_result.items])))

            print()

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity = 'cellular'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant using cellular network: {}".format(', '.join([twin.device_id for twin in query_result.items])))

        except Exception as ex:
            print("Unexpected error {0}".format(ex))
            return
        except KeyboardInterrupt:
            print("IoT Hub Device Twin service sample stopped")
    ```

    **IoTHubRegistryManager-objektet** exponerar alla metoder som krävs för att interagera med enhetstvillingar från tjänsten. Koden initierar först **IoTHubRegistryManager-objektet,** uppdaterar sedan enhetstvillingen för **DEVICE_ID**och kör slutligen två frågor. Den första väljer bara enhetstvillingar för enheter som finns i **Redmond43-anläggningen,** och den andra förfinar frågan för att välja endast de enheter som också är anslutna via ett mobilnät.

6. Lägg till följande kod i slutet av **AddTagsAndQuery.py** för att implementera **funktionen iothub_service_sample_run:**

    ```python
    if __name__ == '__main__':
        print("Starting the Python IoT Hub Device Twin service sample...")
        print()

        iothub_service_sample_run()
    ```

7. Kör programmet med:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Du bör se en enhet i resultatet för frågan som frågar efter alla enheter som finns i **Redmond43** och ingen för frågan som begränsar resultaten till enheter som använder ett mobilnät.

    ![första frågan som visar alla enheter i Redmond](./media/iot-hub-python-twin-getstarted/service-1.png)

I nästa avsnitt skapar du en enhetsapp som rapporterar anslutningsinformationen och ändrar resultatet av frågan i föregående avsnitt.

## <a name="create-the-device-app"></a>Skapa enhetsappen

I det här avsnittet skapar du en Python-konsolapp som ansluter till hubben som **ditt {Device ID}** och uppdaterar sedan enhetstvillingens rapporterade egenskaper så att den innehåller den information som den är ansluten med ett mobilnät.

1. Installera **Azure IoT Hub Device SDK för Python**från en kommandotolk i arbetskatalogen:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Skapa en ny **ReportConnectivity.py-fil** med hjälp av en textredigerare.

3. Lägg till följande kod för att importera de moduler som krävs från enhetenSDK:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Lägg till följande kod. Ersätt `[IoTHub Device Connection String]` platshållarvärdet med den enhetsanslutningssträng som du kopierade i [Registrera en ny enhet i IoT-hubben](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Lägg till följande kod i **ReportConnectivity.py-filen** för att implementera funktionen för enhetstvillingar:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoT Hub Device Twin device sample stopped" )
    ```

    **IoTHubModuleClient-objektet** exponerar alla metoder som du behöver för att interagera med enhetstvillingar från enheten. Den tidigare koden, när den initierar **IoTHubModuleClient-objektet,** hämtar enhetstvillingen för enheten och uppdaterar den rapporterade egenskapen med anslutningsinformationen.

6. Lägg till följande kod i slutet av **ReportConnectivity.py** för att implementera **funktionen iothub_client_sample_run:**

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub Device Twin device sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Kör enhetsappen:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Du bör se bekräftelse på att enhetstvillingens rapporterade egenskaper har uppdaterats.

    ![uppdatera rapporterade egenskaper från enhetsapp](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Nu när enheten rapporterade sin anslutningsinformation bör den visas i båda frågorna. Gå tillbaka och kör frågorna igen:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Den här gången ska **ditt {Device ID}** visas i båda frågeresultaten.

    ![andra frågan på tjänstapp](./media/iot-hub-python-twin-getstarted/service-2.png)

    I enhetsappen visas en bekräftelse på att den önskade egenskapsvillingkorrigeringen som skickades av tjänstappen togs emot.

    ![ta emot önskade egenskaper på enhetsappen](./media/iot-hub-python-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du har lagt till enhetsmetadata som taggar från en backend-app och skrev en simulerad enhetsapp för att rapportera enhetsanslutningsinformation i enhetstvillingen. Du har också lärt dig hur du frågar den här informationen med hjälp av registret.

Använd följande resurser för att lära dig hur du:

* Skicka telemetri från enheter med självstudien [Kom igång med IoT Hub.](quickstart-send-telemetry-python.md)

* Konfigurera enheter med hjälp av enhetstvillingens önskade egenskaper med de [önskade egenskaperna Använd för att konfigurera enhetsdator.](tutorial-device-twins.md)

* Styra enheter interaktivt (till exempel aktivera en fläkt från en användarstyrd app) med självstudiekursen [Använd direkta metoder.](quickstart-control-device-python.md)
