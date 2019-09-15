---
title: Meddelanden från moln till enhet med Azure IoT Hub (python) | Microsoft Docs
description: 'Skicka meddelanden från moln till enhet till en enhet från en Azure IoT Hub med Azure IoT-SDK: er för python. Du ändrar en simulerad enhets app för att ta emot meddelanden från molnet till enheten och ändra en backend-app för att skicka meddelanden från molnet till enheten.'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 4cda59448856630468076ef63c51b8a216a31bd0
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001955"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Skicka meddelanden från moln till enhet med IoT Hub (python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub är en fullständigt hanterad tjänst som hjälper till att möjliggöra tillförlitlig och säker dubbelriktad kommunikation mellan miljon tals enheter och Server delen av lösningen. Snabb starten [Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-python.md) visar hur du skapar en IoT-hubb, etablerar en enhets identitet i den och kodar en simulerad enhets app som skickar enhet till molnet-meddelanden.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här självstudien bygger på [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md). Det visar hur du:

* Skicka meddelanden från moln till enhet från Server delen av lösningen till en enda enhet via IoT Hub.

* Ta emot meddelanden från molnet till enheten på en enhet.

* Från Server delen av lösningen kan du begära leverans bekräftelse (*feedback*) för meddelanden som skickas till en enhet från IoT Hub.

Du hittar mer information om moln-till-enhet-meddelanden i [guiden för IoT Hub utvecklare](iot-hub-devguide-messaging.md).

I slutet av den här självstudien kör du två python-konsol program:

* **SimulatedDevice.py**, en modifierad version av appen som skapats i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md), som ansluter till din IoT-hubb och tar emot meddelanden från molnet till enheten.

* **SendCloudToDeviceMessage.py**, som skickar ett meddelande från moln till enhet till den simulerade Device-appen via IoT Hub, och sedan får leverans bekräftelse.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="receive-messages-in-the-simulated-device-app"></a>Ta emot meddelanden i den simulerade Device-appen

I det här avsnittet skapar du en python-konsol-app för att simulera enheten och ta emot meddelanden från molnet till enheten från IoT Hub.

1. Skapa en **SimulatedDevice.py** -fil med hjälp av en text redigerare.

2. Lägg till följande `import` -instruktioner och variabler i början av **SimulatedDevice.py** -filen:

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. Lägg till följande kod i **SimulatedDevice.py** -filen. Ersätt placeholder-värdet {deviceConnectionString} med enhets anslutnings strängen för enheten som du skapade i [Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-python.md) -snabb start:

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Lägg till följande funktion för att skriva ut mottagna meddelanden till-konsolen:

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

5. Lägg till följande kod för att initiera klienten och vänta på att ta emot meddelanden från molnet till enheten:

    ```python
    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )
    ```

6. Lägg till följande huvud funktion:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Spara och Stäng **SimulatedDevice.py** -filen.

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutnings strängen för IoT Hub

I den här artikeln skapar du en backend-tjänst för att skicka meddelanden från molnet till enheten via IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-python.md). För att skicka meddelanden från molnet till enheten måste tjänsten ha behörighet för **tjänst anslutning** . Som standard skapas varje IoT Hub med en delad åtkomst princip med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande från moln till enhet

I det här avsnittet skapar du en python-konsol-app som skickar meddelanden från molnet till enheten till den simulerade Device-appen. Du behöver enhets-ID för enheten som du har lagt till i [Skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-python.md) -snabb start. Du behöver också den IoT Hub-anslutningssträng som du kopierade tidigare i [Hämta anslutnings strängen för IoT Hub](#get-the-iot-hub-connection-string).

1. Skapa en **SendCloudToDeviceMessage.py** -fil med hjälp av en text redigerare.

2. Lägg till följande `import` -instruktioner och variabler i början av **SendCloudToDeviceMessage.py** -filen:

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

3. Lägg till följande kod i **SendCloudToDeviceMessage.py** -filen. Ersätt plats hållarna {IoT Hub-anslutningssträng} och {Device ID} med IoT Hub-anslutningssträngen och enhets-ID: t som du antecknade tidigare:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Lägg till följande funktion för att skriva ut feedback-meddelanden till-konsolen:

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Lägg till följande kod för att skicka ett meddelande till enheten och hantera feedback-meddelandet när enheten bekräftar meddelandet från molnet till enheten:

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

6. Lägg till följande huvud funktion:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. Spara och Stäng **SendCloudToDeviceMessage.py** -filen.

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. Öppna en kommando tolk och installera **Azure IoT Hub Device SDK för python**.

    ```shell
    pip install azure-iothub-device-client
    ```

2. Kör följande kommando i kommando tolken för att lyssna efter meddelanden från molnet till enheten:

    ```shell
    python SimulatedDevice.py
    ```

    ![Kör den simulerade Device-appen](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Öppna en ny kommando tolk och installera **Azure IoT Hub service SDK för python**.

    ```shell
    pip install azure-iothub-service-client
    ```

4. Kör följande kommando i en kommando tolk för att skicka ett meddelande från moln till enhet och vänta på feedback från meddelandet:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Kör appen för att skicka kommandot från molnet till enheten](./media/iot-hub-python-python-c2d/send-command.png)

5. Observera meddelandet som mottagits av enheten.

    ![Meddelande mottaget](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skickar och tar emot meddelanden från molnet till enheten.

Exempel på kompletta lösningar från slut punkt till slut punkt som använder IoT Hub finns i [Azure IoT Remote Monitoring Solution Accelerator](https://azure.microsoft.com/documentation/suites/iot-suite/).

Mer information om hur du utvecklar lösningar med IoT Hub finns i [IoT Hub Developer Guide](iot-hub-devguide.md).
