---
title: Moln-till-enhet-meddelanden med Azure IoT Hub (Python) | Microsoft-dokument
description: Så här skickar du meddelanden från molnet till enheten till en enhet från en Azure IoT-hubb med Azure IoT-SDK:er för Python. Du ändrar en simulerad enhetsapp för att ta emot meddelanden från molnet till enheten och ändrar en backend-app för att skicka meddelanden från molnet till enheten.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: robinsh
ms.openlocfilehash: 0c3b35eeed85dd3a1c44dea6ec46203eb812e1e8
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257863"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Skicka meddelanden från molnet till enheten med IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub är en fullständigt hanterad tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan miljontals enheter och en lösningsbakdel. [Snabbstarten Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md) visar hur du skapar en IoT-hubb, etablerar en enhetsidentitet i den och kodar en simulerad enhetsapp som skickar meddelanden från enhet till moln.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här självstudien bygger på [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md). Det visar dig hur du:

* Från din lösningsbaksida skickar du meddelanden från molnet till enheten till en enda enhet via IoT Hub.

* Ta emot meddelanden från molnet till enheten på en enhet.

Du hittar mer information om meddelanden från molnet till enheten i [utvecklarhandboken för IoT Hub](iot-hub-devguide-messaging.md).

I slutet av den här självstudien kör du två Python-konsolappar:

* **SimulatedDevice.py**, en modifierad version av appen som skapats i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md), som ansluter till din IoT-hubb och tar emot meddelanden från molnet till enheten.

* **SendCloudToDeviceMessage.py**, som skickar meddelanden från molnet till enheten till den simulerade enhetsappen via IoT Hub.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Krav

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Ta emot meddelanden i den simulerade enhetsappen

I det här avsnittet skapar du en Python-konsolapp för att simulera enheten och ta emot meddelanden från IoT-hubben från IoT-hubben.

1. Installera **Azure IoT Hub Device SDK för Python**från en kommandotolk i arbetskatalogen:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Skapa en fil med namnet **SimulatedDevice.py**med hjälp av en textredigerare .

1. Lägg till `import` följande satser och variabler i början av **SimulatedDevice.py-filen:**

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. Lägg till följande kod i **SimulatedDevice.py** filen. Ersätt `{deviceConnectionString}` platshållarvärdet med enhetsanslutningssträngen för enheten som du skapade i [skicka telemetrin från en enhet till en snabbstart för IoT-hubb:](quickstart-send-telemetry-python.md)

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Lägg till följande funktion för att skriva ut mottagna meddelanden till konsolen:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("\nMessage received:")

            #print data and both system and application (custom) properties
            for property in vars(message).items():
                print ("    {0}".format(property))

            print( "Total calls received: {}".format(RECEIVED_MESSAGES))
            print()
    ```

1. Lägg till följande kod för att initiera klienten och vänta på att få meddelandet från molnet till enheten:

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging device sample stopped" )
    ```

1. Lägg till följande huvudfunktion:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging device sample..." )
        print ( "Waiting for C2D messages, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

1. Spara och stäng filen **SimulatedDevice.py**.

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

I den här artikeln skapar du en serverd-tjänst för att skicka meddelanden från molnet till enheten via IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md). Om du vill skicka meddelanden från molnet till enheten behöver tjänsten behörigheten **för tjänsten ansluta.** Som standard skapas varje IoT-hubb med en princip med namnet delad åtkomst med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande från molnet till enheten

I det här avsnittet skapar du en Python-konsolapp som skickar meddelanden från molnet till enheten till den simulerade enhetsappen. Du behöver enhets-ID:t för den enhet som du lade till i [skicka telemetrin från en enhet till en snabbstart för IoT-hubb.](quickstart-send-telemetry-python.md) Du behöver också anslutningssträngen för IoT-hubben som du kopierade tidigare i [Hämta anslutningssträngen för IoT-hubben](#get-the-iot-hub-connection-string).

1. Öppna en kommandotolk i arbetskatalogen och installera **Azure IoT Hub Service SDK för Python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. Skapa en fil med namnet **SendCloudToDeviceMessage.py**med hjälp av en textredigerare .

1. Lägg till `import` följande satser och variabler i början av **SendCloudToDeviceMessage.py-filen:**

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Lägg till följande kod i **SendCloudToDeviceMessage.py** filen. Ersätt `{iot hub connection string}` värdena och `{device id}` platshållarvärdena med anslutningssträngen för IoT-hubben och enhets-ID som du noterade tidigare:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Lägg till följande kod för att skicka meddelanden till enheten:

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. Lägg till följande huvudfunktion:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. Spara och stäng **SendCloudToDeviceMessage.py** fil.

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. I kommandotolken i arbetskatalogen kör du följande kommando för att lyssna efter meddelanden från molnet till enheten:

    ```shell
    python SimulatedDevice.py
    ```

    ![Köra den simulerade enhetsappen](./media/iot-hub-python-python-c2d/device-1.png)

1. Öppna en ny kommandotolk i arbetskatalogen och kör följande kommando för att skicka meddelanden från molnet till enheten:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Kör appen för att skicka kommandot moln-till-enhet](./media/iot-hub-python-python-c2d/service.png)

1. Observera de meddelanden som tas emot av enheten.

    ![Mottaget meddelande](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skicka och ta emot meddelanden från molnet till enheten.

Information om hur du ser exempel på kompletta heltäckande lösningar som använder IoT Hub finns i [Azure IoT Remote Monitoring solution accelerator](https://azure.microsoft.com/documentation/suites/iot-suite/).

Mer information om hur du utvecklar lösningar med IoT Hub finns i [utvecklarhandboken för IoT Hub](iot-hub-devguide.md).
