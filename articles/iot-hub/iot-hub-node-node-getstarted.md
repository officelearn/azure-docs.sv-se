---
title: "Komma igång med Azure IoT Hub för Node.js | Microsoft Docs"
description: "Självstudiekurs om hur du kommer igång med Azure IoT Hub med Node.js. Använd Azure IoT Hub och Node.js med Azure IoT-SDK:er för att implementera en IoT-lösning (Internet of Things)."
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
ms.date: 09/12/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: 91794776d0faf9dd5b7385e00ca907f13b493908


---
# <a name="get-started-with-azure-iot-hub-for-nodejs"></a>Komma igång med Azure IoT Hub för Node.js
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

I slutet av de här självstudierna har du tre Node.js-konsolprogram:

* **CreateDeviceIdentity.js**, som skapar en enhetsidentitet och en associerad säkerhetsnyckel för att ansluta din simulerade enhetsapp.
* **ReadDeviceToCloudMessages.js**, som visar telemetri som skickas av din simulerade enhetsapp.
* **SimulatedDevice.js**, som ansluter till din IoT Hub med enhetsidentiteten som skapades tidigare och som skickar ett telemetrimeddelande varje sekund med hjälp av AMQP-protokollet.

> [!NOTE]
> Artikeln om [Azure IoT SDK:er][lnk-hub-sdks] innehåller information om Azure IoT SDK:er som du kan använda för att skapa båda apparna så att de kan köras på enheter och på lösningens backend-servrar.
> 
> 

För att kunna genomföra den här kursen behöver du följande:

* Node.js version 0.10.x eller senare.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Nu har du skapat din IoT-hubb. Du har det IoT Hub-värdnamn och den IoT Hub-anslutningssträng som du behöver för att slutföra resten av de här självstudierna.

## <a name="create-a-device-identity"></a>Skapa en enhetsidentitet
I det här avsnittet skapar du en Node.js-konsolapp som skapar en enhetsidentitet i identitetsregistret i IoT-hubben. En enhet kan inte ansluta till IoT Hub om den inte har en post i identitetsregistret. Mer information finns i avsnittet om **identitetsregistret** i [utvecklarhandboken för IoT Hub][lnk-devguide-identity]. När du kör den här konsolappen genererar det ett unikt enhets-ID och en nyckel som din enhet kan använda för att identifiera sig själv när den skickar ”enhet-till-molnet”-meddelanden till IoT Hub.

1. Skapa en ny tom mapp med namnet **createdeviceidentity**. I mappen **createdeviceidentity** skapar du en package.json-fil med följande kommando i Kommandotolken. Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. I Kommandotolken i mappen **createdeviceidentity** kör du följande kommando för att installera Service SDK-paketet **azure-iothub**:
   
    ```
    npm install azure-iothub --save
    ```
3. Med hjälp av en textredigerare skapar du en **CreateDeviceIdentity.js**-fil i mappen **createdeviceidentity**.
4. Lägg till följande `require`-instruktion i början av filen **CreateDeviceIdentity.js**:
   
    ```
    'use strict';
   
    var iothub = require('azure-iothub');
    ```
5. Lägg till följande kod i filen **CreateDeviceIdentity.js** och ersätt platshållarvärdet med anslutningssträngen för IoT-hubben som du skapade i föregående avsnitt: 
   
    ```
    var connectionString = '{iothub connection string}';
   
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```
6. Lägg till följande kod för att skapa en enhetsdefinition i identitetsregistret i din IoT-hubb. Den här koden skapar en enhet om enhets-ID:t inte finns i registret. Annars returneras nyckeln för den befintliga enheten:
   
    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstNodeDevice';
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
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.symmetricKey.primaryKey);
      }
    }
    ```
7. Spara och stäng filen **CreateDeviceIdentity.js**.
8. Kör programmet **createdeviceidentity** genom att köra följande kommando i Kommandotolken i mappen createdeviceidentity:
   
    ```
    node CreateDeviceIdentity.js 
    ```
9. Skriv ner **Enhets-ID** och **Enhetsnyckel**. Du behöver dessa värden senare när du skapar ett program som ansluter till IoT Hub som en enhet.

> [!NOTE]
> IoT Hub-identitetsregistret lagrar bara enhetsidentiteter för att skydda åtkomsten till IoT-hubben. Registret lagrar enhets-ID:n och enhetsnycklar som ska användas som säkerhetsreferenser och en aktiverad/inaktiverad-flagga som du kan använda för att inaktivera åtkomst för en enskild enhet. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Mer information finns i [utvecklarhandboken för IoT Hub][lnk-devguide-identity].
> 
> 

## <a name="receive-device-to-cloud-messages"></a>Ta emot meddelanden från enheten till molnet
I det här avsnittet skapar du en Node.js-konsolapp som läser ”enhet till molnet”-meddelanden från IoT Hub. En IoT-hubb exponerar en [Event Hubs][lnk-event-hubs-overview]-kompatibel slutpunkt så att du kan läsa meddelanden från enheter till molnet. För att göra det så enkelt som möjligt skapar vi en grundläggande läsare i den härs självstudiekursen som inte passar för distributioner med hög genomströmning. Självstudiekursen [Bearbeta meddelanden från enhet till moln][lnk-process-d2c-tutorial] beskriver hur du bearbetar ”enhet till molnet”-meddelanden i hög skala. Självstudiekursen [Komma igång med Event Hubs][lnk-eventhubs-tutorial] innehåller ytterligare information om hur du bearbetar meddelanden från Event Hubs och gäller för de Event Hubs-kompatibla slutpunkterna i IoT Hub.

> [!NOTE]
> Event Hub-kompatibla slutpunkter för läsning av meddelanden från enheter till molnet använder alltid AMQP-protokollet.
> 
> 

1. Skapa en ny tom mapp med namnet **readdevicetocloudmessages**. I mappen **readdevicetocloudmessages** skapar du en package.json-fil med följande kommando i Kommandotolken. Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. I Kommandotolken i mappen **readdevicetocloudmessages** kör du följande kommando för att installera **azure-event-hubs**-paketet:
   
    ```
    npm install azure-event-hubs --save
    ```
3. Använd en textredigerare och skapa en **ReadDeviceToCloudMessages.js**-fil i mappen **readdevicetocloudmessages**.
4. Lägg till följande `require`-instruktioner i början av filen **ReadDeviceToCloudMessages.js**:
   
    ```
    'use strict';
   
    var EventHubClient = require('azure-event-hubs').Client;
    ```
5. Lägg till följande variabeldeklaration och ersätt platshållarvärdet med anslutningssträngen för din IoT-hubb:
   
    ```
    var connectionString = '{iothub connection string}';
    ```
6. Lägg till följande två funktioner som skriver ut utdata till konsolen:
   
    ```
    var printError = function (err) {
      console.log(err.message);
    };
   
    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```
7. Lägg till följande kod för att skapa **EventHubClient**, öppna anslutningen till din IoT-hubb och skapa en mottagare för varje partition. Det här programmet använder ett filter när det skapar en mottagare så att mottagaren endast läser meddelanden som skickas till IoT Hub när mottagaren har börjat köra. Detta filter är användbart i en testmiljö så att du kan se den aktuella uppsättningen meddelanden. I en produktionsmiljö bör koden se till att alla meddelanden bearbetas. Mer information finns i självstudiekursen [How to process IoT Hub device-to-cloud messages][lnk-process-d2c-tutorial] (Bearbeta ”enhet till molnet”-meddelanden i IoT Hub).
   
    ```
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
I det här avsnittet skapar du en Node.js-konsolapp som simulerar en enhet som skickar ”enhet till molnet”-meddelanden till en IoT-hubb.

1. Skapa en ny tom mapp med namnet **simulateddevice**. I mappen **simulateddevice** skapar du en package.json-fil med hjälp av följande kommando i Kommandotolken. Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. I Kommandotolken i mappen **simulateddevice** kör du följande kommando för att installera SDK-paketet **azure-iot-device** och paketet **azure-iot-device-amqp**:
   
    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```
3. Med hjälp av en textredigerare skapar du en ny **SimulatedDevice.js**-fil i mappen **simulateddevice**.
4. Lägg till följande `require`-instruktioner i början av **SimulatedDevice.js**-filen:
   
    ```
    'use strict';
   
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```
5. Lägg till en **connectionString**-variabel och använd den för att skapa en klientenhet. Ersätt **{youriothostname}** med namnet på den IoT-hubb som du skapade i avsnittet *Skapa en IoT Hub*. Ersätt **{yourdevicekey}** med det enhetsnyckelvärde som du genererade i avsnittet *Skapa en enhetsidentitet*:
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';
   
    var client = clientFromConnectionString(connectionString);
    ```
6. Lägg till följande funktion för att visa utdata från programmet:
   
    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. Skapa ett motanrop och använd funktionen **setInterval** för att skicka ett nytt meddelande till IoT-hubben varje sekund:
   
    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
   
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
8. Öppna anslutningen till din IoT-hubb och börja skicka meddelanden:
   
    ```
    client.open(connectCallback);
    ```
9. Spara och stäng filen **SimulatedDevice.js**.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff), vilket rekommenderas i MSDN-artikeln om [hantering av tillfälliga fel][lnk-transient-faults].
> 
> 

## <a name="run-the-apps"></a>Kör apparna
Nu är det dags att köra apparna.

1. I Kommandotolken i mappen **readdevicetocloudmessages** kör du följande kommando för att börja övervaka IoT-hubben:
   
    ```
    node ReadDeviceToCloudMessages.js 
    ```
   
    ![Node.js IoT Hub-tjänstens klientprogram för att övervaka meddelanden från enheten till molnet][7]
2. Kör följande kommando i Kommandotolken i mappen **simulateddevice** för att börja skicka telemetridata till din IoT-hubb:
   
    ```
    node SimulatedDevice.js
    ```
   
    ![Node.js IoT Hub-tjänstens klientprogram för att skicka meddelanden från enheten till molnet][8]
3. På panelen **Användning** på [Azure Portal][lnk-portal] kan du se hur många meddelanden som har skickats till IoT-hubben:
   
    ![Azure-portal Användningspanel som visar antalet meddelanden som har skickats till IoT Hub][43]

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen konfigurerade du en ny IoT-hubb på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Du använde den här enhetsidentiteten så att den simulerade enhetsappen kunde skicka ”enhet till molnet”-meddelanden till IoT-hubben. Du skapade också en app som visar meddelandena som tagits emot av IoT-hubben. 

För att fortsätta komma igång med IoT-hubb och utforska andra IoT-scenarier, se:

* [Connecting your device][lnk-connect-device] (Ansluta din enhet)
* [Connecting your device][lnk-device-management] (Komma igång med enhetshantering)
* [Getting started with the IoT Gateway SDK][lnk-gateway-SDK] (Komma igång med IoT Gateway SDK)

Självstudiekursen [Bearbeta meddelanden från enhet till moln][lnk-process-d2c-tutorial] beskriver hur du utökar din IoT-lösning och bearbetar ”enhet till molnet”-meddelanden i hög skala.

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/



<!--HONumber=Nov16_HO5-->


