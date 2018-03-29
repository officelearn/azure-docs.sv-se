---
title: Moln till enhet meddelanden med Azure IoT Hub (Python) | Microsoft Docs
description: Hur du skickar meddelanden moln till enhet till en enhet från en Azure IoT-hubb med hjälp av Azure IoT-SDK för Python. Du kan ändra en simulerad enhetsapp för att ta emot meddelanden moln till enhet och ändra en backend-app för att skicka meddelanden moln till enhet.
services: iot-hub
documentationcenter: python
author: msebolt
manager: timlt
editor: ''
ms.assetid: 3ca8a78f-ade2-46e8-8a49-d5d599cdf1f1
ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo
ms.openlocfilehash: 9a669bda2a41ea6aa474549ee9798889523a27f8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Skicka meddelanden moln till enhet med IoT-hubb (Python)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


## <a name="introduction"></a>Introduktion
Azure IoT Hub är en helt hanterad tjänst som hjälper till att aktivera tillförlitlig och säker dubbelriktad kommunikation mellan miljoner enheter och en lösning tillbaka avslutas. Den [Kom igång med IoT-hubb] kursen visar hur du skapar en IoT-hubb, etablera en enhetsidentitet i den och code en simulerad enhetsapp som skickar meddelanden från enhet till moln.

Den här kursen bygger på [Kom igång med IoT-hubb]. Den visar hur till:

* Skicka meddelanden moln till enhet på en enhet till IoT-hubb från din lösningens serverdel.
* Ta emot meddelanden moln till enhet på en enhet.
* Begära leverans bekräftelse från din lösningens serverdel (*feedback*) för meddelanden som skickas till en enhet från IoT-hubb.

Du hittar mer information om moln till enhet meddelanden i den [IoT-hubb Utvecklarhandbok][IoT Hub developer guide - C2D].

I slutet av den här kursen kan köra du två Python-konsolappar:

* **SimulatedDevice.py**, en modifierad version av app som skapats i [Kom igång med IoT-hubb], som ansluter till din IoT-hubb och tar emot meddelanden moln till enhet.
* **SendCloudToDeviceMessage.py**, som skickar ett moln till enhet-meddelande till appen simulerade enheten via IoT-hubb och tar emot dess leverans bekräftelse.

> [!NOTE]
> IoT-hubben har SDK stöd för många enhetsplattformar och språk (inklusive C, Java och Javascript) via SDK för Azure IoT-enhet. Stegvisa instruktioner om hur du ansluter enheten till den här självstudiekursen kod och vanligtvis Azure IoT Hub finns i [Azure IoT Developer Center].
> 

För att kunna genomföra den här kursen behöver du följande:

* [Python 2.x eller 3.x][lnk-python-download]. Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Se till att du lägger till Python i den plattformsspecifika miljövariabeln när du uppmanas att göra det under installationen. Om du använder Python 2.x kan du behöva [installera eller uppgradera *PIP* (pakethanteringssystemet för Python)][lnk-install-pip].
* Om du använder Windows OS installerar du [Visual C++ redistributable package][lnk-visual-c-redist] så att du kan använda native-DLL:er från Python.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

> [!NOTE]
> *Pip*-paketet för `azure-iothub-service-client` och `azure-iothub-device-client` är endast tillgänglig för Windows-Operativsystemet. Linux/Mac OS x finns i Linux och Mac OS-specifika avsnitt på [Förbered din utvecklingsmiljö för Python] [lnk-python-devbox] post.
> 


## <a name="receive-messages-in-the-simulated-device-app"></a>Ta emot meddelanden i appen simulerade enheten
I det här avsnittet skapar du en Python-konsolapp för att simulera enheten och ta emot meddelanden moln till enhet från IoT-hubben.

1. Med hjälp av en textredigerare, skapa en **SimulatedDevice.py** fil.

1. Lägg till följande `import` instruktioner och variabler i början av den **SimulatedDevice.py** fil:
   
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

1. Lägg till följande kod i **SimulatedDevice.py** fil. Ersätt värdet för ”{deviceConnectionString}”-platshållare med enheten anslutningssträngen för den enhet som du skapade i den [Kom igång med IoT-hubb] kursen:
   
    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Lägg till följande funktion om du vill skriva ut mottagna meddelanden till konsolen:
   
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

1. Lägg till följande kod för att initiera klienten och vänta på att meddelandet moln till enhet:
   
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

1. Lägg till följande huvudsakliga funktion:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Spara och Stäng **SimulatedDevice.py** fil.


## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande moln till enhet
I det här avsnittet skapar du en Python-konsolapp som skickar moln till enhet meddelanden i appen simulerade enheten. Du behöver enhets-ID på den enhet som du lade till i den [Kom igång med IoT-hubb] kursen. Du måste också anslutningssträngen IoT-hubb för din hubb hittar du i den [Azure-portalen].

1. Med hjälp av en textredigerare, skapa en **SendCloudToDeviceMessage.py** fil.

1. Lägg till följande `import` instruktioner och variabler i början av den **SendCloudToDeviceMessage.py** fil:
   
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

1. Lägg till följande kod i **SendCloudToDeviceMessage.py** fil. Ersätt värdet för ”{IoTHubConnectionString}” platshållaren med IoT-hubb anslutningssträngen för hubben som du skapade i den [Kom igång med IoT-hubb] kursen. Ersätt platshållaren ”{deviceId}” med enhets-ID på den enhet som du lade till i den [Kom igång med IoT-hubb] kursen:
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Lägg till följande funktion om du vill skriva ut feedback meddelanden till konsolen:
   
    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

1. Lägg till följande kod för att skicka ett meddelande till din enhet och hantera meddelandet feedback när enheten har bekräftat meddelandet moln till enhet:
   
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

1. Lägg till följande huvudsakliga funktion:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

1. Spara och Stäng **SendCloudToDeviceMessage.py** fil.


## <a name="run-the-applications"></a>Köra programmen
Nu är det dags att köra programmen.

1. Öppna en kommandotolk och installera den **Azure IoT Hub-enhet SDK för Python**.

    ```
    pip install azure-iothub-device-client
    ```

1. I Kommandotolken, kör du följande kommando för att lyssna efter meddelanden som moln till enhet:
   
    ```shell
    python SimulatedDevice.py 
    ```
   
    ![Kör appen simulerade enheten][img-simulated-device]

1. Öppna en ny kommandotolk och installera den **Azure IoT-hubb Service SDK för Python**.

    ```
    pip install azure-iothub-service-client
    ```

1. Kör följande kommando för att skicka ett meddelande moln till enhet och vänta tills meddelandet feedback i en kommandotolk:
   
    ```shell
    python SendCloudToDeviceMessage.py 
    ```
   
    ![Kör appen för att skicka kommandot moln till enhet][img-send-command]
   
1. Observera meddelandet som togs emot av enheten.

    ![Meddelande togs emot][img-message-recieved]


## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen beskrivs hur du skickar och tar emot meddelanden moln till enhet. 

Exempel på fullständiga lösningar för slutpunkt till slutpunkt med IoT-hubb finns [Azure IoT Suite].

Mer information om hur du utvecklar lösningar med IoT-hubb finns i [IoT-hubb Utvecklarhandbok].

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[Kom igång med IoT-hubb]: iot-hub-node-node-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[IoT-hubb Utvecklarhandbok]: iot-hub-devguide.md
[Azure IoT Developer Center]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure-portalen]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
