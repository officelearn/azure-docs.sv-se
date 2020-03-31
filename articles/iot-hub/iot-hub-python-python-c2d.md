---
title: Moln-till-enhet-meddelanden med Azure IoT Hub (Python) | Microsoft-dokument
description: Så här skickar du meddelanden från molnet till enheten till en enhet från en Azure IoT-hubb med Azure IoT-SDK:er för Python. Du ändrar en simulerad enhetsapp för att ta emot meddelanden från molnet till enheten och ändrar en backend-app för att skicka meddelanden från molnet till enheten.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 3613062cf8765a4aec3327b660bb5818898f2dd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110430"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Skicka meddelanden från molnet till enheten med IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub är en fullständigt hanterad tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan miljontals enheter och en lösningsbakdel. [Snabbstarten Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md) visar hur du skapar en IoT-hubb, etablerar en enhetsidentitet i den och kodar en simulerad enhetsapp som skickar meddelanden från enhet till moln.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här självstudien bygger på [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md). Det visar dig hur du:

* Från din lösningsbaksida skickar du meddelanden från molnet till enheten till en enda enhet via IoT Hub.

* Ta emot meddelanden från molnet till enheten på en enhet.

* Från din lösningsbaksida, begär leveransbekräftelse *(feedback)* för meddelanden som skickas till en enhet från IoT Hub.

Du hittar mer information om meddelanden från molnet till enheten i [utvecklarhandboken för IoT Hub](iot-hub-devguide-messaging.md).

I slutet av den här självstudien kör du två Python-konsolappar:

* **SimulatedDevice.py**, en modifierad version av appen som skapats i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md), som ansluter till din IoT-hubb och tar emot meddelanden från molnet till enheten.

* **SendCloudToDeviceMessage.py**, som skickar ett meddelande från molnet till enheten till den simulerade enhetsappen via IoT Hub och sedan får sin leveransbekräftelse.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Krav

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Ta emot meddelanden i den simulerade enhetsappen

I det här avsnittet skapar du en Python-konsolapp för att simulera enheten och ta emot meddelanden från IoT-hubben från IoT-hubben.

1. Skapa en **SimulatedDevice.py-fil** med hjälp av en textredigerare.

2. Lägg till `import` följande satser och variabler i början av **SimulatedDevice.py-filen:**

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. Lägg till följande kod i **SimulatedDevice.py** filen. Ersätt platshållarvärdet "{deviceConnectionString}" med enhetsanslutningssträngen för enheten som du skapade i [snabbstarten Skicka telemetri från en enhet till en snabbstart för IoT-hubb:](quickstart-send-telemetry-python.md)

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Lägg till följande funktion för att skriva ut mottagna meddelanden till konsolen:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("Message received")
            print( "    Data: <<{}>>".format(message.data) )
            print( "    Properties: {}".format(message.custom_properties))
            print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

5. Lägg till följande kod för att initiera klienten och vänta på att få meddelandet från molnet till enheten:

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
            print ( "IoTHubDeviceClient sample stopped" )
    ```

6. Lägg till följande huvudfunktion:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Spara och stäng **SimulatedDevice.py** filen.

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

I den här artikeln skapar du en serverd-tjänst för att skicka meddelanden från molnet till enheten via IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md). Om du vill skicka meddelanden från molnet till enheten behöver tjänsten behörigheten **för tjänsten ansluta.** Som standard skapas varje IoT-hubb med en princip med namnet delad åtkomst med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande från molnet till enheten

I det här avsnittet skapar du en Python-konsolapp som skickar meddelanden från molnet till enheten till den simulerade enhetsappen. Du behöver enhets-ID:t för den enhet som du lade till i [skicka telemetrin från en enhet till en snabbstart för IoT-hubb.](quickstart-send-telemetry-python.md) Du behöver också anslutningssträngen för IoT-hubben som du kopierade tidigare i [Hämta anslutningssträngen för IoT-hubben](#get-the-iot-hub-connection-string).

1. Skapa en **SendCloudToDeviceMessage.py-fil** med hjälp av en textredigerare.

2. Lägg till `import` följande satser och variabler i början av **SendCloudToDeviceMessage.py-filen:**

    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

3. Lägg till följande kod i **SendCloudToDeviceMessage.py** filen. Ersätt platshållarvärdena {iot hub connection string}" och "{device id}" med ioT-hubbanslutningssträngen och enhets-ID:et som du noterade tidigare:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Lägg till följande funktion för att skriva ut feedbackmeddelanden till konsolen:

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Lägg till följande kod för att skicka ett meddelande till enheten och hantera feedbackmeddelandet när enheten bekräftar meddelandet från molnet till enheten:

    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

6. Lägg till följande huvudfunktion:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. Spara och stäng **SendCloudToDeviceMessage.py** fil.

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. Öppna en kommandotolk och installera **Azure IoT Hub Device SDK för Python**.

    ```shell
    pip install azure-iothub-device-client
    ```

2. Kör följande kommando i kommandotolken för att lyssna efter meddelanden från molnet till enheten:

    ```shell
    python SimulatedDevice.py
    ```

    ![Köra den simulerade enhetsappen](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Öppna en ny kommandotolk och installera **Azure IoT Hub Service SDK för Python**.

    ```shell
    pip install azure-iothub-service-client
    ```

4. Kör följande kommando i kommandotolken för att skicka ett meddelande från molnet till enheten och vänta på feedback från meddelandet:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Kör appen för att skicka kommandot moln-till-enhet](./media/iot-hub-python-python-c2d/send-command.png)

5. Observera meddelandet som tas emot av enheten.

    ![Mottaget meddelande](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skicka och ta emot meddelanden från molnet till enheten.

Information om hur du ser exempel på kompletta heltäckande lösningar som använder IoT Hub finns i [Azure IoT Remote Monitoring solution accelerator](https://azure.microsoft.com/documentation/suites/iot-suite/).

Mer information om hur du utvecklar lösningar med IoT Hub finns i [utvecklarhandboken för IoT Hub](iot-hub-devguide.md).
