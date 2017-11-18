---
title: Moln till enhet meddelanden med Azure IoT Hub (nod) | Microsoft Docs
description: "Hur du skickar meddelanden moln till enhet till en enhet från en Azure IoT-hubb med hjälp av Azure IoT-SDK för Node.js. Du kan ändra en simulerad enhetsapp för att ta emot meddelanden moln till enhet och ändra en backend-app för att skicka meddelanden moln till enhet."
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3ca8a78f-ade2-46e8-8a49-d5d599cdf1f1
ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: 80f65e8e7fe562030c1e39787b910e2564969882
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Skicka meddelanden moln till enhet med IoT-hubb (nod)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduktion
Azure IoT Hub är en helt hanterad tjänst som hjälper till att aktivera tillförlitlig och säker dubbelriktad kommunikation mellan miljoner enheter och en lösning tillbaka avslutas. Den [Kom igång med IoT-hubb] kursen visar hur du skapar en IoT-hubb, etablera en enhetsidentitet i den och code en simulerad enhetsapp som skickar meddelanden från enhet till moln.

Den här kursen bygger på [Kom igång med IoT-hubb]. Den visar hur till:

* Skicka meddelanden moln till enhet på en enhet till IoT-hubb från din lösningens serverdel.
* Ta emot meddelanden moln till enhet på en enhet.
* Begära leverans bekräftelse från din lösningens serverdel (*feedback*) för meddelanden som skickas till en enhet från IoT-hubb.

Du hittar mer information om moln till enhet meddelanden i den [IoT-hubb Utvecklarhandbok][IoT Hub developer guide - C2D].

I slutet av den här kursen kan köra du två Node.js-konsolappar:

* **SimulatedDevice**, en modifierad version av app som skapats i [Kom igång med IoT-hubb], som ansluter till din IoT-hubb och tar emot meddelanden moln till enhet.
* **SendCloudToDeviceMessage**, som skickar ett moln till enhet-meddelande till appen simulerade enheten via IoT-hubb och tar emot dess leverans bekräftelse.

> [!NOTE]
> IoT-hubben har SDK stöd för många enhetsplattformar och språk (inklusive C, Java och Javascript) via SDK för Azure IoT-enhet. Stegvisa instruktioner om hur du ansluter enheten till den här självstudiekursen kod och vanligtvis Azure IoT Hub finns i [Azure IoT Developer Center].
> 
> 

För att kunna genomföra den här kursen behöver du följande:

* Node.js version 4.0.x eller senare.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

## <a name="receive-messages-in-the-simulated-device-app"></a>Ta emot meddelanden i appen simulerade enheten
I det här avsnittet kan du ändra den simulerade enhetsapp som du skapade i [Kom igång med IoT-hubb] ta emot meddelanden moln till enhet från IoT-hubben.

1. Använd en textredigerare och öppna filen SimulatedDevice.js.
2. Ändra den **connectCallback** funktion för att hantera meddelanden som skickats från IoT-hubb. I det här exemplet enheten alltid anropar den **fullständig** funktion för att meddela IoT-hubb att meddelandet har bearbetats. Den nya versionen av den **connectCallback** funktionen ser ut som följande utdrag:
   
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
   > Om du använder HTTPS i stället för MQTT eller AMQP som transport, den **DeviceClient** instans söker efter meddelanden från IoT-hubb sällan (mindre än var 25: e minut). Mer information om skillnaderna mellan MQTT, AMQP och HTTPS-stöd och IoT-hubb begränsning finns på [IoT-hubb Utvecklarhandbok][IoT Hub developer guide - C2D].
   > 
   > 

## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande moln till enhet
I det här avsnittet skapar du en Node.js-konsolapp som skickar moln till enhet meddelanden i appen simulerade enheten. Du behöver enhets-ID på den enhet som du lade till i den [Kom igång med IoT-hubb] kursen. Du måste också anslutningssträngen IoT-hubb för din hubb hittar du i den [Azure-portalen].

1. Skapa en tom mapp som kallas **sendcloudtodevicemessage**. I den **sendcloudtodevicemessage** mapp, skapa en package.json-fil med följande kommando vid en kommandotolk. Acceptera alla standardvärden:
   
    ```shell
    npm init
    ```
2. Vid en kommandotolk i den **sendcloudtodevicemessage** mapp, kör följande kommando för att installera den **azure iothub** paketet:
   
    ```shell
    npm install azure-iothub --save
    ```
3. Med hjälp av en textredigerare, skapa en **SendCloudToDeviceMessage.js** filen i den **sendcloudtodevicemessage** mapp.
4. Lägg till följande `require` instruktioner i början av den **SendCloudToDeviceMessage.js** fil:
   
    ```javascript
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```
5. Lägg till följande kod i **SendCloudToDeviceMessage.js** fil. Ersätt värdet för ”{iot-hubb anslutningssträngen}” platshållaren med IoT-hubb anslutningssträngen för hubben som du skapade i den [Kom igång med IoT-hubb] kursen. Ersätt platshållaren ”{enhets-id}” med enhets-ID på den enhet som du lade till i den [Kom igång med IoT-hubb] kursen:
   
    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';
   
    var serviceClient = Client.fromConnectionString(connectionString);
    ```
6. Lägg till följande funktion om du vill skriva ut åtgärden resulterar i konsolen:
   
    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. Lägg till följande funktion om du vill skriva ut leverans feedback meddelanden till konsolen:
   
    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```
8. Lägg till följande kod för att skicka ett meddelande till din enhet och hantera meddelandet feedback när enheten har bekräftat meddelandet moln till enhet:
   
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

1. I Kommandotolken i den **simulateddevice** mapp, kör följande kommando för att skicka telemetri till IoT-hubb och lyssnar efter meddelanden moln till enhet:
   
    ```shell
    node SimulatedDevice.js 
    ```
   
    ![Kör appen simulerade enheten][img-simulated-device]
2. Vid en kommandotolk i den **sendcloudtodevicemessage** mapp, kör följande kommando för att skicka ett meddelande moln till enhet och vänta tills bekräftelse feedback:
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Kör appen för att skicka kommandot moln till enhet][img-send-command]
   
   > [!NOTE]
   > Den här självstudiekursen implementerar inte några återförsöksprincip sätt. I produktionskod, bör du implementera försök principer (till exempel exponentiell backoff), enligt förslaget i MSDN-artikel [hantering av tillfälliga fel].
   > 
   > 

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen beskrivs hur du skickar och tar emot meddelanden moln till enhet. 

Exempel på fullständiga lösningar för slutpunkt till slutpunkt med IoT-hubb finns [Azure IoT Suite].

Mer information om hur du utvecklar lösningar med IoT-hubb finns i [IoT-hubb Utvecklarhandbok].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Kom igång med IoT-hubb]: iot-hub-node-node-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[IoT-hubb Utvecklarhandbok]: iot-hub-devguide.md
[Azure IoT Developer Center]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[hantering av tillfälliga fel]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure-portalen]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
