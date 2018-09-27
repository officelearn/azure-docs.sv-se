---
title: Meddelanden från molnet till enheten med Azure IoT Hub (Node) | Microsoft Docs
description: Hur du skickar meddelanden från moln till enhet till en enhet från Azure IoT hub med Azure IoT SDK för Node.js. Du kan ändra en simulerad enhetsapp för att ta emot meddelanden från moln till enhet och ändra en backend-app för att skicka meddelanden från moln-till-enhet.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: c2b8df708a568fa995cf3af783c112127726811b
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47221816"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Skicka meddelanden från moln till enhet med IoT Hub (Node)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduktion
Azure IoT Hub är en helt hanterad tjänst som hjälper dig att aktivera pålitlig och säker dubbelriktad kommunikation mellan miljontals enheter och tillhandahåller serverdelen. Den [Kom igång med IoT Hub] kursen visar hur du skapar en IoT-hubb, etablera en enhetsidentitet i den och koda en simulerad enhetsapp som skickar meddelanden från enheten till molnet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här självstudien bygger på [Kom igång med IoT Hub]. Den visar hur du:

* Skicka meddelanden från moln till enhet på en enhet via IoT Hub från lösningens backend-servrar.
* Ta emot meddelanden från molnet till enheten på en enhet.
* Från lösningens backend-servrar, begära leverans bekräftelse (*feedback*) för meddelanden som skickas till en enhet från IoT Hub.

Du hittar mer information om meddelanden från molnet till enheten i den [utvecklarhandboken för IoT Hub][IoT Hub developer guide - C2D].

I slutet av den här kursen kan du köra två Node.js-konsolappar:

* **SimulatedDevice**, en modifierad version av appen som skapats i [Kom igång med IoT Hub], som ansluter till din IoT hub och tar emot meddelanden från molnet till enheten.
* **SendCloudToDeviceMessage**, som skickar ett moln-till-enhet-meddelande till den simulerade enhetsappen via IoT Hub och sedan ta emot dess leverans bekräftelse.

> [!NOTE]
> IoT Hub har SDK-stöd för många enhetsplattformar och språk (inklusive C, Java och Javascript) via SDK: er för Azure IoT-enheter. Stegvisa instruktioner om hur du ansluter enheten till den här självstudien kod och vanligen på Azure IoT Hub finns i den [Azure IoT Developer Center].
> 
> 

För att kunna genomföra den här kursen behöver du följande:

* Node.js version 4.0.x eller senare.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

## <a name="receive-messages-in-the-simulated-device-app"></a>Ta emot meddelanden i den simulerade enhetsappen
I det här avsnittet ska du ändra den simulerade enhetsappen som du skapade i [Kom igång med IoT Hub] att ta emot meddelanden från moln till enhet från IoT hub.

1. Använd en textredigerare och öppna filen SimulatedDevice.js.
2. Ändra den **connectCallback** funktion för att hantera meddelanden som skickas från IoT Hub. I det här exemplet enheten alltid anropar den **fullständig** funktionen för att meddela IoT Hub att meddelandet har bearbetats. Den nya versionen av den **connectCallback** funktionen ser ut som följande kodavsnitt:
   
    ```javascript
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
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
   
   > [!NOTE]
   > Om du använder HTTPS i stället för MQTT eller AMQP som transport, den **DeviceClient** instans söker efter meddelanden från IoT Hub sällan (mindre än var 25: e minut). Mer information om skillnaderna mellan MQTT-, AMQP- och HTTPS-stöd och IoT Hub-begränsning finns i den [utvecklarhandboken för IoT Hub][IoT Hub developer guide - C2D].
   > 
   > 

## <a name="send-a-cloud-to-device-message"></a>Skicka ett moln-till-enhet-meddelande
I det här avsnittet skapar du en Node.js-konsolapp som skickar meddelanden från molnet till enheten till den simulerade enhetsappen. Du behöver enhets-ID för enheten som du lade till i den [Kom igång med IoT Hub] självstudien. Du måste också IoT Hub-anslutningssträngen för hubben som du hittar i den [Azure Portal].

1. Skapa en tom mapp med namnet **sendcloudtodevicemessage**. I den **sendcloudtodevicemessage** mappen skapar du en package.json-fil med följande kommando i Kommandotolken. Acceptera alla standardvärden:
   
    ```shell
    npm init
    ```
2. I Kommandotolken i den **sendcloudtodevicemessage** mapp, kör följande kommando för att installera den **azure-iothub** paketet:
   
    ```shell
    npm install azure-iothub --save
    ```
3. Använd en textredigerare och skapa en **SendCloudToDeviceMessage.js** fil i den **sendcloudtodevicemessage** mapp.
4. Lägg till följande `require` instruktioner i början av den **SendCloudToDeviceMessage.js** fil:
   
    ```javascript
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```
5. Lägg till följande kod i **SendCloudToDeviceMessage.js** fil. Ersätt ”{iot hub-anslutningssträngen}” platshållarvärdet med IoT Hub-anslutningssträngen för hubben som du skapade i den [Kom igång med IoT Hub] självstudien. Ersätt platshållaren ”{enhets-id}” med enhets-ID för enheten som du lade till i den [Kom igång med IoT Hub] självstudien:
   
    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';
   
    var serviceClient = Client.fromConnectionString(connectionString);
    ```
6. Lägg till följande funktion om du vill skriva ut Åtgärdsresultat till konsolen:
   
    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. Lägg till följande funktion om du vill skriva ut leverans av meddelanden till konsolen:
   
    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```
8. Lägg till följande kod för att skicka ett meddelande till din enhet och hantera feedback-meddelande när enheten bekräftar moln-till-enhet-meddelande:
   
    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```
9. Spara och Stäng **SendCloudToDeviceMessage.js** fil.

## <a name="run-the-applications"></a>Köra programmen
Nu är det dags att köra programmen.

1. I Kommandotolken i den **simulateddevice** mapp, kör följande kommando för att skicka telemetri till IoT Hub och för att lyssna efter meddelanden från moln till enhet:
   
    ```shell
    node SimulatedDevice.js 
    ```
   
    ![Kör den simulerade enhetsappen][img-simulated-device]
2. I en kommandotolk i den **sendcloudtodevicemessage** mapp, kör följande kommando för att skicka ett moln-till-enhet-meddelande och vänta på bekräftelse-feedback:
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Kör appen för att skicka kommandot moln till enhet][img-send-command]
   
   > [!NOTE]
   > Den här självstudien implementerar inte någon återförsöksprincip sätt. I produktionskoden bör du implementera principer för omförsök (till exempel exponentiell backoff) vilket rekommenderas i artikeln, [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).
   > 
   > 

## <a name="next-steps"></a>Nästa steg
I de här självstudierna lärde du dig att skicka och ta emot meddelanden från moln till enhet. 

Exempel på fullständiga lösningar för slutpunkt till slutpunkt som använder IoT Hub finns i [Azure IoT-Remote Monitoring solution accelerator].

Mer information om hur du utvecklar lösningar med IoT Hub finns det [utvecklarhandboken för IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Kom igång med IoT Hub]: quickstart-send-telemetry-node.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Utvecklarhandboken för IoT Hub]: iot-hub-devguide.md
[Azure IoT Developer Center]: http://azure.microsoft.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Azure Portal]: https://portal.azure.com
[Azure IoT-Remote Monitoring solution accelerator]: https://azure.microsoft.com/documentation/suites/iot-suite/
