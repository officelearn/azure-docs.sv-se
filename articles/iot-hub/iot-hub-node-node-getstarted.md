<properties
    pageTitle="Komma igång med Azure IoT Hub för Node.js | Microsoft Azure"
    description="Självstudiekurs om hur du kommer igång med Azure IoT Hub med Node.js. Använd Azure IoT Hub och Node.js med Microsoft Azure IoT-SDK:er för att implementera en IoT-lösning (Internet of Things)."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>


# Komma igång med Azure IoT Hub för Node.js

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

I slutet av de här självstudierna har du tre Node.js-konsolprogram:

* **CreateDeviceIdentity.js**, som skapar en enhetsidentitet och en associerad säkerhetsnyckel för att ansluta dina simulerade enheter.
* **ReadDeviceToCloudMessages.js**, som visar telemetri som skickas av den simulerade enheten.
* **SimulatedDevice.js**, som ansluter till din IoT Hub med enhetsidentiteten som skapades tidigare och som skickar ett telemetrimeddelande varje sekund med hjälp av AMQPS-protokollet.

> [AZURE.NOTE] Artikeln [IoT Hub SDK][lnk-hub-sdks] innehåller information om de olika SDK:er som du kan använda för att skapa båda programmen för körning på enheter och serverdelen i din lösning.

För att kunna genomföra den här kursen behöver du följande:

+ Node.js version 0.12.x eller senare. <br/> [Förbereda utvecklingsmiljön][lnk-dev-setup] beskriver hur du installerar Node.js för den här självstudiekursen i Windows eller Linux.

+ Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [Kostnadsfri utvärderingsversion av Azure][lnk-free-trial].)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Nu har du skapat din IoT-hubb. Du har det IoT Hub-värdnamn och den IoT Hub-anslutningssträng som du behöver för att slutföra resten av de här självstudierna.

## Skapa en enhetsidentitet

I det här avsnittet skapar du en Node.js-konsolapp som skapar en enhetsidentitet i identitetsregistret i IoT-hubben. En enhet kan inte ansluta till IoT-hubbar om den inte har en post i enhetsidentitetsregistret. Mer information finns i avsnittet om **enhetsidentitetsregistret** i [IoT Hub Developer Guide][lnk-devguide-identity]. När du kör den här konsolappen genererar det ett unikt enhets-ID och en nyckel som din enhet kan använda för att identifiera sig själv när den skickar ”enhet-till-molnet”-meddelanden till IoT Hub.

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

6. Lägg till följande kod för att skapa en enhetsdefinition i enhetsidentitetsregistret i din IoT-hubb. Den här koden skapar en enhet om enhets-ID:t inte finns i registret. Annars returneras nyckeln för den befintliga enheten:

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
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. Spara och stäng filen **CreateDeviceIdentity.js**.

8. Kör programmet **createdeviceidentity** genom att köra följande kommando i Kommandotolken i mappen createdeviceidentity:

    ```
    node CreateDeviceIdentity.js 
    ```

9. Skriv ner **Enhets-ID** och **Enhetsnyckel**. Du behöver dessa värden senare när du skapar ett program som ansluter till IoT Hub som en enhet.

> [AZURE.NOTE] IoT Hub-identitetsregistret lagrar bara enhetsidentiteter för att skydda åtkomsten till hubben. Registret lagrar enhets-ID:n och enhetsnycklar som ska användas som säkerhetsreferenser och en aktiverad/inaktiverad-flagga som du kan använda för att inaktivera åtkomst för en enskild enhet. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Mer information finns i [IoT Hub Developer Guide][lnk-devguide-identity].

## Ta emot meddelanden från enheten till molnet

I det här avsnittet skapar du en Node.js-konsolapp som läser ”enhet till molnet”-meddelanden från IoT Hub. En IoT-hubb exponerar en [Event Hubs][lnk-event-hubs-overview]-kompatibel slutpunkt så att du kan läsa meddelanden från enheten till molnet. För att göra det så enkelt som möjligt skapar vi en grundläggande läsare i den härs självstudiekursen som inte passar för distributioner med hög genomströmning. I självstudiekursen [Behandla meddelanden från enheten till molnet][lnk-process-d2c-tutorial] lär du dig hur du bearbetar ”enhet till molnet”-meddelanden i hög skala. Självstudierna [Komma igång med Event Hubs][lnk-eventhubs-tutorial] innehåller ytterligare information om hur du bearbetar meddelanden från Event Hubs och gäller Event Hubs-kompatibla slutpunkter i IoT Hub.

> [AZURE.NOTE] Event Hubs-kompatibla slutpunkter för läsning av meddelanden från enheter till molnet använder alltid AMQPS-protokollet.

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

7. Lägg till följande kod för att skapa **EventHubClient**, öppna anslutningen till din IoT-hubb och skapa en mottagare för varje partition. Det här programmet använder ett filter när det skapar en mottagare så att mottagaren endast läser meddelanden som skickas till IoT Hub när mottagaren har börjat köra. Detta filter är användbart i en testmiljö så att du kan se den aktuella uppsättningen meddelanden. I en produktionsmiljö bör koden se till att alla meddelanden bearbetas. Mer information finns i självstudierna [Behandla meddelanden från enheten till molnet i IoT Hub][lnk-process-d2c-tutorial]:

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

## Skapa en simulerad enhetsapp

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

> [AZURE.NOTE] För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff) vilket rekommenderas i MSDN-artikeln om [hantering av tillfälliga fel][lnk-transient-faults].


## Köra programmen

Nu är det dags att köra programmen.

1. I Kommandotolken i mappen **readdevicetocloudmessages** kör du följande kommando för att börja övervaka IoT-hubben:

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![][7]

2. Kör följande kommando i Kommandotolken i mappen **simulateddevice** för att börja skicka telemetridata till din IoT-hubb:

    ```
    node SimulatedDevice.js
    ```

    ![][8]

3. På panelen **Användning** på [Azure-portalen][lnk-portal] kan du se hur många meddelanden som skickats till hubben:

    ![][43]

## Nästa steg

I den här självstudiekursen konfigurerade du en ny IoT-hubb på portalen och skapade sedan en enhetsidentitet i hubbens identitetsregister. Du använde den här enhetsidentiteten så att den simulerade enhetsappen kunde skicka ”enhet till molnet”-meddelanden till hubben. Du skapade också en app som visar meddelandena som tagits emot av hubben. 

För att fortsätta komma igång med IoT-hubb och utforska andra IoT-scenarier, se:

- [Ansluta din enhet][lnk-connect-device]
- [Komma igång med enhetshantering][lnk-device-management]
- [Komma igång med Gateway-SDK][lnk-gateway-SDK]

I självstudien [Behandla meddelanden från enheten till molnet][lnk-process-d2c-tutorial] lär du dig hur du utökar din IoT-lösning och behandlar ”enhet till molnet”-meddelanden i hög skala.

<!-- Images. -->
[6]: ./media/iot-hub-node-node-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/



<!--HONumber=Sep16_HO3-->


