---
title: Meddelanden från molnet till enheten med Azure IoT Hub (Python) | Microsoft Docs
description: Hur du skickar meddelanden från moln till enhet till en enhet från Azure IoT hub med Azure IoT SDK för Python. Du kan ändra en simulerad enhetsapp för att ta emot meddelanden från moln till enhet och ändra en backend-app för att skicka meddelanden från moln-till-enhet.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: kgremban
ms.openlocfilehash: 7ac668bdbc3698be3ed2aa50a428cef84e68369a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61441415"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Skicka meddelanden från moln till enhet med IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduktion
Azure IoT Hub är en helt hanterad tjänst som hjälper dig att aktivera pålitlig och säker dubbelriktad kommunikation mellan miljontals enheter och tillhandahåller serverdelen. Den [Kom igång med IoT Hub](quickstart-send-telemetry-python.md) snabbstarten visar hur du skapar en IoT-hubb, etablera en enhetsidentitet i den och koda en simulerad enhetsapp som skickar meddelanden från enheten till molnet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här självstudien bygger på [Kom igång med IoT Hub](quickstart-send-telemetry-python.md). Den visar hur du:

* Skicka meddelanden från moln till enhet på en enhet via IoT Hub från lösningens backend-servrar.

* Ta emot meddelanden från molnet till enheten på en enhet.

* Från lösningens backend-servrar, begära leverans bekräftelse (*feedback*) för meddelanden som skickas till en enhet från IoT Hub.

Du hittar mer information om meddelanden från molnet till enheten i den [utvecklarhandboken för IoT Hub](iot-hub-devguide-messaging.md).

I slutet av den här kursen kan du köra två Python-konsolappar:

* **SimulatedDevice.py**, en modifierad version av appen som skapats i [Kom igång med IoT Hub](quickstart-send-telemetry-python.md), som ansluter till din IoT hub och tar emot meddelanden från molnet till enheten.

* **SendCloudToDeviceMessage.py**, som skickar ett moln-till-enhet-meddelande till den simulerade enhetsappen via IoT Hub och sedan ta emot dess leverans bekräftelse.

> [!NOTE]
> IoT Hub har SDK-stöd för många enhetsplattformar och språk (inklusive C, Java och Javascript) via SDK: er för Azure IoT-enheter. Stegvisa instruktioner om hur du ansluter enheten till den här självstudien kod och vanligen på Azure IoT Hub finns i den [Azure IoT Developer Center](https://www.azure.com/develop/iot).
>

För att kunna genomföra den här kursen behöver du följande:

* [Python 2.x eller 3.x](https://www.python.org/downloads/). Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Se till att du lägger till Python i den plattformsspecifika miljövariabeln när du uppmanas att göra det under installationen. Om du använder Python 2.x kan du behöva [installera eller uppgradera *PIP* (pakethanteringssystemet för Python)](https://pip.pypa.io/en/stable/installing/).

* Om du använder Windows OS installerar du [Visual C++ redistributable package](https://www.microsoft.com/download/confirmation.aspx?id=48145) så att du kan använda native-DLL:er från Python.

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

> [!NOTE]
> *Pip*-paketet för `azure-iothub-service-client` och `azure-iothub-device-client` är endast tillgänglig för Windows-Operativsystemet. Linux/Mac OS, finns i Linux och Mac OS-specifika avsnitt på den [Förbered din utvecklingsmiljö för Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) publicera.
>

## <a name="receive-messages-in-the-simulated-device-app"></a>Ta emot meddelanden i den simulerade enhetsappen

I det här avsnittet skapar du en Python-konsolapp för att simulera enheten och ta emot meddelanden från moln till enhet från IoT hub.

1. Använd en textredigerare och skapa en **SimulatedDevice.py** fil.

2. Lägg till följande `import` instruktioner och variabler i början av den **SimulatedDevice.py** fil:

   ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError

    RECEIVE_CONTEXT = 0
    WAIT_COUNT = 10
    RECEIVED_COUNT = 0
    RECEIVE_CALLBACKS = 0
    ```

3. Lägg till följande kod i **SimulatedDevice.py** fil. Ersätt ”{deviceConnectionString}” platshållarvärdet med anslutningssträngen för den enhet som du skapade i den [Kom igång med IoT Hub](quickstart-send-telemetry-python.md) Snabbstart:

    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Lägg till följande funktion om du vill skriva ut mottagna meddelanden till konsolen:

    ```python
    def receive_message_callback(message, counter):
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "Received Message [%d]:" % counter )
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        counter += 1
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        return IoTHubMessageDispositionResult.ACCEPTED

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def print_last_message_time(client):
        try:
            last_message = client.get_last_message_receive_time()
            print ( "Last Message: %s" % time.asctime(time.localtime(last_message)) )
            print ( "Actual time : %s" % time.asctime() )
        except IoTHubClientError as iothub_client_error:
            if iothub_client_error.args[0].result == IoTHubClientResult.INDEFINITE_TIME:
                print ( "No message received" )
            else:
                print ( iothub_client_error )
    ```

5. Lägg till följande kod för att initiera klienten och vänta med att ta emot meddelandet moln till enhet:

    ```python
    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    status = client.get_send_status()
                    print ( "Send status: %s" % status )
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

        print_last_message_time(client)
    ```

6. Lägg till följande huvudsakliga funktion:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

7. Spara och Stäng **SimulatedDevice.py** fil.

## <a name="send-a-cloud-to-device-message"></a>Skicka ett moln-till-enhet-meddelande

I det här avsnittet skapar du en Python-konsolapp som skickar meddelanden från molnet till enheten till den simulerade enhetsappen. Du behöver enhets-ID för enheten som du lade till i den [Kom igång med IoT Hub](quickstart-send-telemetry-python.md) Snabbstart. Du måste också IoT Hub-anslutningssträngen för hubben som du hittar i den [Azure-portalen](https://portal.azure.com).

1. Använd en textredigerare och skapa en **SendCloudToDeviceMessage.py** fil.

2. Lägg till följande `import` instruktioner och variabler i början av den **SendCloudToDeviceMessage.py** fil:

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

3. Lägg till följande kod i **SendCloudToDeviceMessage.py** fil. Ersätt ”{IoTHubConnectionString}” platshållarvärdet med IoT Hub-anslutningssträngen för hubben som du skapade i den [Kom igång med IoT Hub](quickstart-send-telemetry-python.md) Snabbstart. Ersätt platshållaren ”{deviceId}” med enhets-ID för enheten som du lade till i den [Kom igång med IoT Hub](quickstart-send-telemetry-python.md) Snabbstart:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Lägg till följande funktion om du vill skriva ut meddelanden till konsolen:

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Lägg till följande kod för att skicka ett meddelande till din enhet och hantera feedback-meddelande när enheten bekräftar moln-till-enhet-meddelande:

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

6. Lägg till följande huvudsakliga funktion:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. Spara och Stäng **SendCloudToDeviceMessage.py** fil.

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. Öppna en kommandotolk och installera den **Azure IoT Hub Device SDK för Python**.

    ```
    pip install azure-iothub-device-client
    ```

2. Kör följande kommando för att lyssna efter meddelanden från molnet till enheten i Kommandotolken:

    ```shell
    python SimulatedDevice.py
    ```

    ![Kör den simulerade enhetsappen](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Öppna en kommandotolk och installera den **Azure IoT Hub Service SDK för Python**.

    ```
    pip install azure-iothub-service-client
    ```

4. Kör följande kommando för att skicka ett moln-till-enhet-meddelande och vänta tills meddelandet feedback i en kommandotolk:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Kör appen för att skicka kommandot moln till enhet](./media/iot-hub-python-python-c2d/send-command.png)

5. Observera meddelandet tas emot av enheten.

    ![Det mottagna meddelandet](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Nästa steg

I de här självstudierna lärde du dig att skicka och ta emot meddelanden från moln till enhet.

Exempel på fullständiga lösningar för slutpunkt till slutpunkt som använder IoT Hub finns i [Azure IoT lösningsacceleratorn för fjärrövervakning](https://azure.microsoft.com/documentation/suites/iot-suite/).

Mer information om hur du utvecklar lösningar med IoT Hub finns det [utvecklarhandboken för IoT Hub](iot-hub-devguide.md).
