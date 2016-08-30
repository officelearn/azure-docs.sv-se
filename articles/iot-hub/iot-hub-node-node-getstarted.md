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
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Komma igång med Azure IoT Hub för Node.js

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Introduktion

Azure IoT Hub är en helt hanterad tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan flera miljoner IoT-enheter och som tillhandahåller serverdelen för lösningar av den här typen. En av de största utmaningarna i ett IoT-projekt är att på ett tillförlitligt och säkert sätt kunna ansluta enheter till lösningens serverdel. IoT Hub löser detta problem genom att:

- Erbjuda tillförlitlig meddelandehantering i hög skala från enhet till moln och från moln till enhet.
- Skydda kommunikationen med autentiseringsuppgifter och åtkomstkontroll för varje enhet.
- Tillhandahålla enhetsbibliotek för de mest populära språken och plattformarna.

I den här självstudiekursen lär du dig att:

- Använda Azure-portalen för att skapa en IoT-hubb.
- Skapa en enhetsidentitet i din IoT-hubb.
- Skapa en simulerad enhet som skickar telemetri till serverdelen i molnet.

I slutet av den här självstudiekursen har du tre Node.js-konsolprogram:

* **CreateDeviceIdentity.js**, som skapar en enhetsidentitet och en associerad säkerhetsnyckel för att ansluta dina simulerade enheter.
* **ReadDeviceToCloudMessages.js**, som visar telemetri som skickas av den simulerade enheten.
* **SimulatedDevice.js**, som ansluter till din IoT Hub med enhetsidentiteten som skapades tidigare och som skickar ett telemetrimeddelande varje sekund med hjälp av AMQPS-protokollet.

> [AZURE.NOTE] Artikeln [IoT Hub SDK][lnk-hub-sdks] innehåller information om de olika SDK:er som du kan använda för att skapa båda programmen för körning på enheter och serverdelen i din lösning.

För att kunna genomföra den här kursen behöver du följande:

+ Node.js version 0.12.x eller senare. <br/> [Förbereda utvecklingsmiljön][lnk-dev-setup] beskriver hur du installerar Node.js för den här självstudiekursen i Windows eller Linux.

+ Ett aktivt Azure-konto. <br/>Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [Kostnadsfri utvärderingsversion av Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Som ett sista steg klickar du på **Inställningar** på bladet IoT-hubb och sedan på **Meddelanden** på bladet **Inställningar**. På bladet **Meddelanden** noterar du **Händelsehubb-kompatibelt namn** och **Händelsehubb-kompatibel slutpunkt**. Du behöver dessa värden när du skapar **read-d2c-messages**-programmet.

![][6]

Nu har du skapat IoT-hubben och har IoT Hub-värdnamnet, IoT Hub-anslutningssträngen, det Event Hubs-kompatibla namnet och de Event Hubs-kompatibla slutpunktsvärdena som du behöver för att slutföra resten av kursen.

## Skapa en enhetsidentitet

I det här avsnittet ska du skapa en Node.js-konsolapp som skapar en ny enhetsidentitet i identitetsregistret i IoT-hubben. En enhet kan inte ansluta till IoT-hubbar om den inte har en post i enhetsidentitetsregistret. Mer information finns i avsnittet om **enhetsidentitetsregistret** i [IoT Hub Developer Guide][lnk-devguide-identity]. När du kör det här konsolprogrammet genererar det ett unikt enhets-ID och en nyckel som din enhet kan identifiera sig själv med när den skickar ”enhet till molnet”-meddelanden till IoT Hub.

1. Skapa en ny tom mapp med namnet **createdeviceidentity**. I mappen **createdeviceidentity** skapar du en ny package.json-fil med följande kommando i Kommandotolken. Acceptera alla standardvärden:

    ```
    npm init
    ```

2. I Kommandotolken i mappen **createdeviceidentity** kör du följande kommando för att installera paketet **azure-iothub**:

    ```
    npm install azure-iothub --save
    ```

3. Med hjälp av en textredigerare skapar du en ny **CreateDeviceIdentity.js**-fil i mappen **createdeviceidentity**.

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

6. Lägg till följande kod för att skapa en ny enhetsdefinition i enhetsidentitetsregistret i din IoT-hubb. Den här koden skapar en ny enhet om enhets-ID:t inte finns i registret. Annars returneras nyckeln för den befintliga enheten:

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstDevice';
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

9. Skriv ner **Enhets-ID** och **Enhetsnyckel**. Du behöver dessa senare när du skapar ett program som ansluter till IoT Hub som en enhet.

> [AZURE.NOTE] IoT Hub-identitetsregistret lagrar bara enhetsidentiteter för att skydda åtkomsten till hubben. Registret lagrar enhets-ID:n och enhetsnycklar som ska användas som säkerhetsreferenser och en aktiverad/inaktiverad-flagga som gör att du kan inaktivera åtkomst för en enskild enhet. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Mer information finns i [IoT Hub Developer Guide][lnk-devguide-identity].

## Ta emot meddelanden från enheten till molnet

I det här avsnittet ska du skapa en Node.js-konsolapp som läser ”enhet till molnet”-meddelanden från IoT Hub. En IoT-hubb exponerar en [Event Hubs][lnk-event-hubs-overview]-kompatibel slutpunkt så att du kan läsa meddelanden från enheten till molnet. För att göra det så enkelt som möjligt skapar vi en grundläggande läsare i den härs självstudiekursen som inte passar för distributioner med hög genomströmning. I självstudiekursen [Behandla meddelanden från enheten till molnet][lnk-process-d2c-tutorial] lär du dig hur du bearbetar ”enhet till molnet”-meddelanden i hög skala. Självstudiekursen [Komma igång med Event Hubs][lnk-eventhubs-tutorial] innehåller ytterligare information om hur du bearbetar meddelanden från Event Hubs och gäller Event Hubs-kompatibla slutpunkter i IoT Hub.

> [AZURE.NOTE] Event Hubs-kompatibla slutpunkter för läsning av meddelanden från enheter till molnet använder alltid AMQPS-protokollet.

1. Skapa en ny tom mapp med namnet **readdevicetocloudmessages**. I mappen **readdevicetocloudmessages** skapar du en ny package.json-fil med följande kommando i Kommandotolken. Acceptera alla standardvärden:

    ```
    npm init
    ```

2. I Kommandotolken i mappen **readdevicetocloudmessages** kör du följande kommando för att installera **amqp10**- och **bluebird**-paketen:

    ```
    npm install amqp10 bluebird --save
    ```

3. Använd en textredigerare och skapa en ny **ReadDeviceToCloudMessages.js**-fil i mappen **readdevicetocloudmessages**.

4. Lägg till följande `require`-instruktioner i början av filen **ReadDeviceToCloudMessages.js**:

    ```
    'use strict';

    var AMQPClient = require('amqp10').Client;
    var Policy = require('amqp10').Policy;
    var translator = require('amqp10').translator;
    var Promise = require('bluebird');
    ```

5. Lägg till följande variabeldeklarationer och ersätt platshållarna med de värden som du antecknade tidigare. Värdet för platshållaren **{your event hub-compatible namespace}** kommer från fältet för den **Event Hub-kompatibla slutpunkten** på portalen och har följande format **namespace.servicebus.windows.net** (utan prefixet *sb://*).

    ```
    var protocol = 'amqps';
    var eventHubHost = '{your event hub-compatible namespace}';
    var sasName = 'iothubowner';
    var sasKey = '{your iot hub key}';
    var eventHubName = '{your event hub-compatible name}';
    var numPartitions = 2;
    ```

    > [AZURE.NOTE] Den här koden förutsätter att du skapade IoT-hubben på F1-nivån (gratis). En kostnadsfri IoT-hubb har två partitioner med namnen ”0” och ”1”. Om du skapade din IoT-hubb med någon av de andra prisnivåerna ändrar du koden för att skapa en **MessageReceiver** för varje partition.

6. Lägg till följande filterdefinition. Det här programmet använder ett filter när det skapar en mottagare så att mottagaren endast läser meddelanden som skickas till IoT Hub när mottagaren har börjat köra. Detta är användbart i en testmiljö så att du kan se den aktuella uppsättningen meddelanden, men i en produktionsmiljö bör koden se till att alla meddelanden bearbetas. Mer information finns i självstudiekursen [Behandla meddelanden från enheten till molnet i IoT Hub][lnk-process-d2c-tutorial].

    ```
    var filterOffset = new Date().getTime();
    var filterOption;
    if (filterOffset) {
      filterOption = {
      attach: { source: { filter: {
      'apache.org:selector-filter:string': translator(
        ['described', ['symbol', 'apache.org:selector-filter:string'], ['string', "amqp.annotation.x-opt-enqueuedtimeutc > " + filterOffset + ""]])
        } } }
      };
    }
    ```

7. Lägg till följande kod för att skapa mottagaradressen och en AMQP-klient:

    ```
    var uri = protocol + '://' + encodeURIComponent(sasName) + ':' + encodeURIComponent(sasKey) + '@' + eventHubHost;
    var recvAddr = eventHubName + '/ConsumerGroups/$default/Partitions/';
    
    var client = new AMQPClient(Policy.EventHub);
    ```

8. Lägg till följande två funktioner som skriver ut utdata till konsolen:

    ```
    var messageHandler = function (partitionId, message) {
      console.log('Received(' + partitionId + '): ', message.body);
    };
    
    var errorHandler = function(partitionId, err) {
      console.warn('** Receive error: ', err);
    };
    ```

9. Lägg till följande funktion som fungerar som en mottagare för en given partition med hjälp av filtret:

    ```
    var createPartitionReceiver = function(partitionId, receiveAddress, filterOption) {
      return client.createReceiver(receiveAddress, filterOption)
        .then(function (receiver) {
          console.log('Listening on partition: ' + partitionId);
          receiver.on('message', messageHandler.bind(null, partitionId));
          receiver.on('errorReceived', errorHandler.bind(null, partitionId));
        });
    };
    ```

10. Lägg till följande kod för att ansluta till den Event Hubs-kompatibla slutpunkten och starta mottagarna:

    ```
    client.connect(uri)
      .then(function () {
        var partitions = [];
        for (var i = 0; i < numPartitions; ++i) {
          partitions.push(createPartitionReceiver(i, recvAddr + i, filterOption));
        }
        return Promise.all(partitions);
    })
    .error(function (e) {
        console.warn('Connection error: ', e);
    });
    ```

11. Spara och stäng filen **ReadDeviceToCloudMessages.js**.

## Skapa en simulerad enhetsapp

I det här avsnittet ska du skapa en Node.js-konsolapp som simulerar en enhet som skickar ”enhet till molnet”-meddelanden till en IoT-hubb.

1. Skapa en ny tom mapp med namnet **simulateddevice**. I mappen **simulateddevice** skapar du en ny package.json-fil med hjälp av följande kommando i Kommandotolken. Acceptera alla standardvärden:

    ```
    npm init
    ```

2. I Kommandotolken i mappen **simulateddevice** kör du följande kommando för att installera paketet **azure-iot-device-amqp**:

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

5. Lägg till en **connectionString**-variabel och använd den för att skapa en klientenhet. Ersätt **{youriothubname}** med ditt IoT-hubbnamn och **{yourdeviceid}** och **{yourdevicekey}** med enhetsvärdena som du genererade i avsnittet *Skapa en enhetsidentitet*:

    ```
    var connectionString = 'HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    
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
            var data = JSON.stringify({ deviceId: 'mydevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 2000);
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

I den här självstudiekursen konfigurerade du en ny IoT-hubb på portalen och skapade sedan en enhetsidentitet i hubbens identitetsregister. Du använde den här enhetsidentiteten för att göra så att den simulerade enhetsappen kunde skicka ”enhet till molnet”-meddelanden till hubben och du skapade en app som visar meddelandena som tagits emot av hubben. Du kan fortsätta att utforska IoT Hub-funktionerna och andra IoT-scenarier i följande självstudiekurser:

- [Skicka meddelanden från molnet till enheten med IoT Hub][lnk-c2d-tutorial] beskriver hur du skickar meddelanden till enheter och hur du bearbetar leveransfeedback från IoT Hub.
- [Behandla meddelanden från enheten till molnet][lnk-process-d2c-tutorial] beskriver hur du med hög tillförlitlighet bearbetar telemetri och interaktiva meddelanden som kommer från enheter.
- [Överföra filer från enheter][lnk-upload-tutorial] beskriver ett mönster som använder ”molnet till enhet”-meddelanden för att underlätta filöverföringar från enheter.

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

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/



<!--HONumber=jun16_HO2-->


