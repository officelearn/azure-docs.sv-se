---
title: "Komma igång med Azure IoT Hub (Python) | Microsoft Docs"
description: "Lär dig hur du skickar meddelanden från enheten till molnet i Azure IoT Hub med IoT SDK:er för Python. Skapa appar för simulerade enheter och tjänster för att registrera din enhet, skicka meddelanden och läsa meddelanden från IoT Hub."
services: iot-hub
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: python
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: dkshir
ms.custom: na
ms.openlocfilehash: acc237afc5c7eccbf2caf876973c147eb8574d7b
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-python"></a>Anslut din simulerade enhet till IoT Hub med hjälp av Python
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

När du är klar med den här självstudiekursen har du två Python-appar:

* **CreateDeviceIdentity.py**, som skapar en enhetsidentitet och en associerad säkerhetsnyckel för att ansluta din app för simulerade enheter.
* **SimulatedDevice.py**, som ansluter till din IoT Hub med enhetsidentiteten som skapades tidigare och som regelbundet skickar ett telemetrimeddelande med hjälp av MQTT-protokollet.

> [!NOTE]
> Artikeln om [Azure IoT SDK:er][lnk-hub-sdks] innehåller information om Azure IoT SDK:er som du kan använda för att skapa båda apparna så att de kan köras på enheter och på lösningens backend-servrar.
> 
> 

För att kunna genomföra den här kursen behöver du följande:

* [Python 2.x eller 3.x][lnk-python-download]. Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Se till att du lägger till Python i den plattformsspecifika miljövariabeln när du uppmanas att göra det under installationen. Om du använder Python 2.x kan du behöva [installera eller uppgradera *PIP* (pakethanteringssystemet för Python)][lnk-install-pip].
* Om du använder Windows OS installerar du [Visual C++ redistributable package][lnk-visual-c-redist] så att du kan använda native-DLL:er från Python.
* [Node.js 4.0 eller senare][lnk-node-download]. Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Detta krävs för att installera [IoT Hub Explorer-verktyget][lnk-iot-hub-explorer].
* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.

> [!NOTE]
> *Pip*-paketet för `azure-iothub-service-client` och `azure-iothub-device-client` är endast tillgänglig för Windows-Operativsystemet. Information om Linux/Mac OS x finns i Linux- och Mac OS-specifika avsnitt på posten [Förbered din utvecklingsmiljö för Python][lnk-python-devbox] .
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Nu har du skapat din IoT Hub. Använd IoT Hub-värdnamnet och IoT Hub-anslutningssträngen i resten av den här självstudiekursen.

> [!NOTE]
> Du kan också enkelt skapa din IoT Hub på en kommandorad med hjälp av Python- eller Node.js-baserad Azure CLI. Artikeln [Create an IoT hub using the Azure CLI 2.0] [ lnk-azure-cli-hub] (Skapa en IoT Hub med Azure CLI 2.0) innehåller korta instruktioner för hur du gör. 
> 

## <a name="create-a-device-identity"></a>Skapa en enhetsidentitet
Det här avsnittet visar hur du skapar en Python-konsolapp som skapar en enhetsidentitet i identitetsregistret för din IoT Hub. En enhet kan endast ansluta till IoT Hub om den har en post i identitetsregistret. Mer information finns i avsnittet om **identitetsregistret** i [utvecklarhandboken för IoT Hub][lnk-devguide-identity]. När du kör den här konsolappen genererar det ett unikt enhets-ID och en nyckel som din enhet kan använda för att identifiera sig själv när den skickar ”enhet-till-molnet”-meddelanden till IoT Hub.

1. Öppna en kommandotolk och installera **Azure IoT Hub Service SDK för Python**. Stäng kommandotolken när du har installerat SDK.

    ```
    pip install azure-iothub-service-client
    ```

2. Skapa en Python-fil med namnet **CreateDeviceIdentity.py**. Öppna filen i [valfri Python-redigerare/IDE][lnk-python-ide-list], till exempel [IDLE][lnk-idle].

3. Lägg till följande kod för att importera de moduler som krävs från SDK:n:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceStatus, IoTHubError
    ```
2. Lägg till följande kod och ersätt platshållarvärdet för `[IoTHub Connection String]` med anslutningssträngen för IoT Hub som du skapade i föregående avsnitt. Du kan använda ett valfritt namn som `DEVICE_ID`.
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "MyFirstPythonDevice"
    ```
   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

3. Lägg till följande funktion om du vill skriva ut en del av enhetsinformationen.

    ```python
    def print_device_info(title, iothub_device):
        print ( title + ":" )
        print ( "iothubDevice.deviceId                    = {0}".format(iothub_device.deviceId) )
        print ( "iothubDevice.primaryKey                  = {0}".format(iothub_device.primaryKey) )
        print ( "iothubDevice.secondaryKey                = {0}".format(iothub_device.secondaryKey) )
        print ( "iothubDevice.connectionState             = {0}".format(iothub_device.connectionState) )
        print ( "iothubDevice.status                      = {0}".format(iothub_device.status) )
        print ( "iothubDevice.lastActivityTime            = {0}".format(iothub_device.lastActivityTime) )
        print ( "iothubDevice.cloudToDeviceMessageCount   = {0}".format(iothub_device.cloudToDeviceMessageCount) )
        print ( "iothubDevice.isManaged                   = {0}".format(iothub_device.isManaged) )
        print ( "iothubDevice.authMethod                  = {0}".format(iothub_device.authMethod) )
        print ( "" )
    ```
3. Lägg till följande funktion om du vill skapa enhetsidentifieringen med Registry Manager. 

    ```python
    def iothub_createdevice():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
            auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
            new_device = iothub_registry_manager.create_device(DEVICE_ID, "", "", auth_method)
            print_device_info("CreateDevice", new_device)

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "iothub_createdevice stopped" )
    ```
4. Slutligen lägger du till huvudfunktionen enligt följande och sparar filen.

    ```python
    if __name__ == '__main__':
        print ( "" )
        print ( "Python {0}".format(sys.version) )
        print ( "Creating device using the Azure IoT Hub Service SDK for Python" )
        print ( "" )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_createdevice()
    ```
5. Kör **CreateDeviceIdentity.py** i kommandotolken enligt följande:

    ```python
    python CreateDeviceIdentity.py
    ```
6. Du bör se att den simulerade enheten skapas. Notera **deviceId** och **primaryKey** för enheten. Du behöver dessa värden senare när du skapar ett program som ansluter till IoT Hub som en enhet.

    ![Enheten har skapats][1]

> [!NOTE]
> IoT Hub-identitetsregistret lagrar bara enhetsidentiteter för att skydda åtkomsten till IoT Hub. Registret lagrar enhets-ID:n och enhetsnycklar som ska användas som säkerhetsreferenser och en aktiverad/inaktiverad-flagga som du kan använda för att inaktivera åtkomst för en enskild enhet. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Mer information finns i [utvecklarhandboken för IoT Hub][lnk-devguide-identity].
> 
> 


## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp
I det här avsnittet visas hur du skapar en Python-konsolapp som simulerar en enhet och skickar ”enhet-till-molnet”-meddelanden från enheten till din IoT Hub.

1. Öppna en kommandotolk och installera Azure IoT Hub Device SDK för Python enligt följande. Stäng kommandotolken efter installationen.

    ```
    pip install azure-iothub-device-client
    ```
2. Skapa en fil med namnet **SimulatedDevice.py**. Öppna filen i valfri Python-redigerare/IDE (till exempel IDLE).

3. Lägg till följande kod för att importera de moduler som krävs från enhets-SDK:n.

    ```python
    import random
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError, DeviceMethodReturnValue
    ```
4. Lägg till följande kod och ersätt platshållaren för `[IoTHub Device Connection String]` med anslutningssträngen för din enhet. Anslutningssträngen har vanligtvis formatet `HostName=<hostName>;DeviceId=<deviceId>;SharedAccessKey=<primaryKey>`. Använd **deviceId** och **primaryKey** som du skapade i föregående avsnitt när du ersätter `<deviceId>` respektive `<primaryKey>`. Ersätt `<hostName>` med värdnamnet för IoT Hub, vanligtvis är det `<IoT hub name>.azure-devices.net`.

    ```python
    # String containing Hostname, Device Id & Device Key in the format
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    # choose HTTP, AMQP or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    MESSAGE_TIMEOUT = 10000
    AVG_WIND_SPEED = 10.0
    SEND_CALLBACKS = 0
    MSG_TXT = "{\"deviceId\": \"MyFirstPythonDevice\",\"windSpeed\": %.2f}"    
    ```
5. Lägg till följande kod för att definiera ett återanrop för sändningsbekräftelse. 

    ```python
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        print ( "Confirmation[%d] received for message with result = %s" % (user_context, result) )
        map_properties = message.properties()
        print ( "    message_id: %s" % message.message_id )
        print ( "    correlation_id: %s" % message.correlation_id )
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        SEND_CALLBACKS += 1
        print ( "    Total calls confirmed: %d" % SEND_CALLBACKS )
    ```
6. Lägg till följande kod för att initiera enhetsklienten.

    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
        # set the time until a message times out
        client.set_option("messageTimeout", MESSAGE_TIMEOUT)
        client.set_option("logtrace", 0)
        client.set_option("product_info", "HappyPath_Simulated-Python")
        return client
    ```
7. Lägg till följande funktion för att formatera och skicka ett meddelande från den simulerade enheten till din IoT Hub.

    ```python
    def iothub_client_telemetry_sample_run():

        try:
            client = iothub_client_init()
            print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
            message_counter = 0

            while True:
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                # messages can be encoded as string or bytearray
                if (message_counter & 1) == 1:
                    message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
                else:
                    message = IoTHubMessage(msg_txt_formatted)
                # optional: assign ids
                message.message_id = "message_%d" % message_counter
                message.correlation_id = "correlation_%d" % message_counter
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % message_counter
                prop_map.add("Property", prop_text)

                client.send_event_async(message, send_confirmation_callback, message_counter)
                print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

                status = client.get_send_status()
                print ( "Send status: %s" % status )
                time.sleep(30)

                status = client.get_send_status()
                print ( "Send status: %s" % status )

                message_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```
8. Slutligen lägger du till huvudfunktionen. 

    ```python
    if __name__ == '__main__':
        print ( "Simulating a device using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_telemetry_sample_run()
    ```
9. Spara och stäng filen **SimulatedDevice.py**. Nu är du redo att köra appen.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff), vilket rekommenderas i MSDN-artikeln om [hantering av tillfälliga fel][lnk-transient-faults].
> 
> 

## <a name="receive-messages-from-your-simulated-device"></a>Ta emot meddelanden från den simulerade enheten
För att kunna ta emot telemetrimeddelanden från enheten måste du använda en [Event Hubs][lnk-event-hubs-overview]-kompatibel slutpunkt som exponeras av IoT Hub, som läser ”enhet-till-molnet”-meddelanden. Läs självstudiekursen [Komma igång med Event Hubs][lnk-eventhubs-tutorial] för information om hur du bearbetar meddelanden från Event Hubs för din IoT Hubs Event Hub-kompatibla slutpunkt. Event Hubs har inte stöd för telemetri i Python ännu, så du kan antingen skapa en [Node.js](iot-hub-node-node-getstarted.md#D2C_node)- eller [.NET](iot-hub-csharp-csharp-getstarted.md#D2C_csharp) Event Hubs-baserad konsolapp för att läsa ”enhet-till-molnet”-meddelanden från IoT Hub. Den här kursen visar hur du kan använda [IoT Hub Explorer-verktyget][lnk-iot-hub-explorer] för att läsa dessa enhetsmeddelanden.

1. Öppna en kommandotolk och installera IoT Hub Explorer. 

    ```
    npm install -g iothub-explorer
    ```

2. Kör följande kommando i kommandotolken för att börja övervaka ”enhet-till-molnet”-meddelanden från enheten. Använd din IoT Hubs anslutningssträng i platshållaren efter `--login`.

    ```
    iothub-explorer monitor-events MyFirstPythonDevice --login "[IoTHub connection string]"
    ```

3. Öppna en ny kommandotolk och gå till den katalog som innehåller filen **SimulatedDevice.py**.

4. Kör filen **SimulatedDevice.py** som regelbundet skickar telemetridata till din IoT Hub. 
   
    ```
    python SimulatedDevice.py
    ```
5. Se enhetsmeddelanden på kommandotolken som kör IoT Hub Explorer från det föregående avsnittet. 

    ![Meddelanden från enheten till molnet (Python)][2]

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du använde den här enhetsidentiteten så att den simulerade enhetsappen kunde skicka ”enhet till molnet”-meddelanden till IoT Hub. Du såg meddelandena som mottogs av IoT Hub med hjälp av IoT Hub Explorer-verktyget. 

Om du vill utforska Python SDK för Azure IoT Hub-användning mer i detalj kan du gå till [denna Git Hub-repo][lnk-python-github]. Om du vill granska meddelandefunktionerna i Azure IoT Hub Service SDK för Python kan du ladda ned och köra [iothub_messaging_sample.py][lnk-messaging-sample]. Om du vill använda simulering på enheten med Azure IoT Hub Device SDK för Python kan du ladda ned och köra [iothub_client_sample.py][lnk-client-sample].

Mer information om hur du kan komma igång med IoT Hub och utforska andra IoT-scenarier finns här:

* [Connecting your device][lnk-connect-device] (Ansluta din enhet)
* [Connecting your device][lnk-device-management] (Komma igång med enhetshantering)
* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iot-edge]

Självstudiekursen [Bearbeta meddelanden från enhet till moln][lnk-process-d2c-tutorial] beskriver hur du utökar din IoT-lösning och bearbetar ”enhet till molnet”-meddelanden i hög skala.
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[1]: ./media/iot-hub-python-getstarted/createdevice.png
[2]: ./media/iot-hub-python-getstarted/sendd2cmessage.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-azure-cli-hub]: https://docs.microsoft.com/azure/iot-hub/iot-hub-create-using-cli
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-idle]: https://docs.python.org/3/library/idle.html
[lnk-python-ide-list]: https://wiki.python.org/moin/IntegratedDevelopmentEnvironments
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-python-github]: https://github.com/Azure/azure-iot-sdk-python
[lnk-messaging-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/service/samples/iothub_messaging_sample.py
[lnk-client-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample.py

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-python-devbox]: https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md

[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
