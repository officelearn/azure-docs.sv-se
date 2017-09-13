---
title: "Kom igång med Azure IoT Hub (Node) | Microsoft Docs"
description: "Lär dig hur du skickar ”enhet till molnet”-meddelanden till Azure IoT Hub med IoT SDK:er för Node.js. Skapa appar för simulerade enheter och tjänster för att registrera din enhet, skicka meddelanden och läsa meddelanden från IoT Hub."
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 56618522-9a31-42c6-94bf-55e2233b39ac
ms.service: iot-hub
ms.devlang: javascript
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: ffc6cfaadae07cb019bc64954343dab21ed23638
ms.contentlocale: sv-se
ms.lasthandoff: 08/31/2017

---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-node"></a>Anslut din simulerade enhet till IoT Hub med hjälp av Node

[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

I slutet av de här självstudierna har du tre Node.js-konsolappar:

* **CreateDeviceIdentity.js**, som skapar en enhetsidentitet och en associerad säkerhetsnyckel för att ansluta din simulerade enhetsapp.
* **ReadDeviceToCloudMessages.js**, som visar telemetri som skickas av din simulerade enhetsapp.
* **SimulatedDevice.js**, som ansluter till din IoT Hub med enhetsidentiteten som skapades tidigare och som skickar ett telemetrimeddelande varje sekund med hjälp av MQTT-protokollet.

> [!NOTE]
> Artikeln om [Azure IoT SDK:er][lnk-hub-sdks] innehåller information om Azure IoT SDK:er som du kan använda för att skapa båda apparna så att de kan köras på enheter och på lösningens backend-servrar.

För att kunna genomföra den här kursen behöver du följande:

* Node.js version 0.10.x eller senare.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Nu har du skapat din IoT Hub. Du har det IoT Hub-värdnamn och den IoT Hub-anslutningssträng som du behöver för att slutföra resten av de här självstudierna.

## <a name="create-a-device-identity"></a>Skapa en enhetsidentitet

I det här avsnittet skapar du en Node.js-konsolapp som skapar en enhetsidentitet i identitetsregistret i IoT Hub. En enhet kan endast ansluta till IoT Hub om den har en post i identitetsregistret. Mer information finns i avsnittet om **identitetsregistret** i [utvecklarhandboken för IoT Hub][lnk-devguide-identity]. Kör den här appen för att generera det unika enhets-ID:t och den unika nyckeln som din enhet använder för att identifiera sig när den skickar meddelanden till molnet.

1. Skapa en tom mapp med namnet `createdeviceidentity`. Skapa en package.json-fil i mappen `createdeviceidentity` genom att köra följande kommando i kommandotolken. Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

2. Installera `azure-iothub` Service SDK-paketet genom att köra följande kommando i kommandotolken i mappen `createdeviceidentity`:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Skapa en **CreateDeviceIdentity.js**-fil i mappen `createdeviceidentity` med hjälp av en textredigerare.

4. Lägg till följande `require`-instruktion i början av filen **CreateDeviceIdentity.js**:

    ```nodejs
    'use strict';

    var iothub = require('azure-iothub');
    ```

5. Lägg till följande kod i filen **CreateDeviceIdentity.js**. Ersätt platshållarvärdet med IoT Hub-anslutningssträngen för hubben som du skapade i föregående avsnitt:

    ```nodejs
    var connectionString = '{iothub connection string}';

    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Lägg till följande kod för att skapa en enhetsdefinition i identitetsregistret i din IoT Hub. Den här koden skapar en enhet om enhets-ID:t inte finns i identitetsregistret. Annars returneras nyckeln för den befintliga enheten:

    ```nodejs
    var device = {
      deviceId: 'myFirstNodeDevice'
    }
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device ID: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.symmetricKey.primaryKey);
      }
    }
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

7. Spara och stäng filen **CreateDeviceIdentity.js**.

8. Kör `createdeviceidentity`-programmet genom att köra följande kommando i kommandotolken i mappen `createdeviceidentity`:

    ```cmd/sh
    node CreateDeviceIdentity.js 
    ```

9. Skriv ner **Enhets-ID** och **Enhetsnyckel**. Du behöver dessa värden senare när du skapar ett program som ansluter till IoT Hub som en enhet.

> [!NOTE]
> IoT Hub-identitetsregistret lagrar bara enhetsidentiteter för att skydda åtkomsten till IoT Hub. Registret lagrar enhets-ID:n och enhetsnycklar som ska användas som säkerhetsreferenser och en aktiverad/inaktiverad-flagga som du kan använda för att inaktivera åtkomst för en enskild enhet. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Mer information finns i [utvecklarhandboken för IoT Hub][lnk-devguide-identity].

<a id="D2C_node"></a>
## <a name="receive-device-to-cloud-messages"></a>Ta emot meddelanden från enheten till molnet

I det här avsnittet skapar du en Node.js-konsolapp som läser ”enhet till molnet”-meddelanden från IoT Hub. En IoT Hub exponerar en [Event Hubs][lnk-event-hubs-overview]-kompatibel slutpunkt så att du kan läsa meddelanden från enheter till molnet. För att göra det så enkelt som möjligt skapar vi en grundläggande läsare i den härs självstudiekursen som inte passar för distributioner med hög genomströmning. Självstudiekursen [Bearbeta meddelanden från enhet till moln][lnk-process-d2c-tutorial] beskriver hur du bearbetar ”enhet till molnet”-meddelanden i hög skala. Självstudiekursen [Kom igång med Event Hubs][lnk-eventhubs-tutorial] innehåller mer information om Event Hubs-kompatibla slutpunkter i IoT Hub.

> [!NOTE]
> Event Hub-kompatibla slutpunkter för läsning av meddelanden från enheter till molnet använder alltid AMQP-protokollet.

1. Skapa en tom mapp med namnet `readdevicetocloudmessages`. Skapa en package.json-fil i mappen `readdevicetocloudmessages` genom att köra följande kommando i kommandotolken. Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

2. Installera **azure-event-hubs**-paketet genom att köra följande kommando i kommandotolken i mappen `readdevicetocloudmessages`:

    ```cmd/sh
    npm install azure-event-hubs --save
    ```

3. Skapa en **ReadDeviceToCloudMessages.js**-fil i mappen `readdevicetocloudmessages` med hjälp av en textredigerare.

4. Lägg till följande `require`-instruktioner i början av filen **ReadDeviceToCloudMessages.js**:

    ```nodejs
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. Lägg till följande variabeldeklaration och ersätt platshållarvärdet med IoT Hub-anslutningssträngen för din hubb:

    ```nodejs
    var connectionString = '{iothub connection string}';
    ```

6. Lägg till följande två funktioner som skriver ut utdata till konsolen:

    ```nodejs
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. Lägg till följande kod för att skapa **EventHubClient**, öppna anslutningen till din IoT Hub och skapa en mottagare för varje partition. Det här programmet använder ett filter när det skapar en mottagare så att mottagaren endast läser meddelanden som skickas till IoT Hub när mottagaren har börjat köra. Detta filter är användbart i en testmiljö så att du kan se den aktuella uppsättningen meddelanden. I en produktionsmiljö ska din kod kontrollera att den bearbetar alla meddelanden. Mer information finns i självstudiekursen [Behandla meddelanden från enheten till molnet i IoT Hub][lnk-process-d2c-tutorial]:

    ```nodejs
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```

8. Spara och stäng filen **ReadDeviceToCloudMessages.js**.

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du en Node.js-konsolapp som simulerar en enhet som skickar ”enhet till molnet”-meddelanden till en IoT Hub.

1. Skapa en tom mapp med namnet `simulateddevice`. Skapa en package.json-fil i mappen `simulateddevice` genom att köra följande kommando i kommandotolken. Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

2. Installera **azure-iot-device** Device SDK-paketet och **azure-iot-device-mqtt**-paketet genom att köra följande kommando i kommandotolken i mappen `simulateddevice`:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Skapa en **SimulatedDevice.js**-fil i mappen `simulateddevice` med hjälp av en textredigerare.

4. Lägg till följande `require`-instruktioner i början av **SimulatedDevice.js**-filen:

    ```nodejs
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Lägg till en `connectionString`-variabel och använd den för att skapa en **Client**-instans. Ersätt `{youriothostname}` med namnet på IoT-hubben som du skapade i avsnittet *Skapa en IoT Hub*. Ersätt `{yourdevicekey}` med värdet för enhetsnyckeln som du skapade i avsnittet *Skapa en enhetsidentitet*:

    ```nodejs
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';

    var client = clientFromConnectionString(connectionString);
    ```

6. Lägg till följande funktion för att visa utdata från programmet:

    ```nodejs
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Skapa ett motanrop och använd funktionen **setInterval** för att skicka ett meddelande till IoT Hub varje sekund:

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);            
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

8. Öppna anslutningen till din IoT Hub och börja skicka meddelanden:

    ```nodejs
    client.open(connectCallback);
    ```

9. Spara och stäng filen **SimulatedDevice.js**.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff), vilket rekommenderas i MSDN-artikeln om [hantering av tillfälliga fel][lnk-transient-faults].

## <a name="run-the-apps"></a>Kör apparna

Nu är det dags att köra apparna.

1. Börja övervaka IoT-hubben genom att köra följande kommando i kommandotolken i mappen `readdevicetocloudmessages`:

    ```cmd/sh
    node ReadDeviceToCloudMessages.js 
    ```

    ![Node.js IoT Hub-tjänstapp för att övervaka meddelanden från enheten till molnet][7]

2. Börja skicka telemetridata till IoT-hubben genom att köra följande kommando i kommandotolken i mappen `simulateddevice`:

    ```cmd/sh
    node SimulatedDevice.js
    ```

    ![Node.js IoT Hub-enhetsapp för att skicka meddelanden från enheten till molnet][8]

3. På panelen **Användning** på [Azure Portal][lnk-portal] kan du se hur många meddelanden som har skickats till IoT Hub:

    ![Azure-portal Användningspanel som visar antalet meddelanden som har skickats till IoT Hub][43]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du använde den här enhetsidentiteten så att den simulerade enhetsappen kunde skicka ”enhet till molnet”-meddelanden till IoT Hub. Du skapade också en app som visar meddelandena som tagits emot av IoT Hub.

Mer information om hur du kan komma igång med IoT Hub och utforska andra IoT-scenarier finns här:

* [Connecting your device][lnk-connect-device] (Ansluta din enhet)
* [Connecting your device][lnk-device-management] (Komma igång med enhetshantering)
* [Komma igång med Azure IoT Edge][lnk-iot-edge]

Självstudiekursen [Bearbeta meddelanden från enhet till moln][lnk-process-d2c-tutorial] beskriver hur du utökar din IoT-lösning och bearbetar ”enhet till molnet”-meddelanden i hög skala.
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

