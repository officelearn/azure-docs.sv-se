---
title: Meddelanden från molnet till enheten med Azure IoT Hub (Node) | Microsoft Docs
description: Hur du skickar meddelanden från moln till enhet till en enhet från Azure IoT hub med Azure IoT SDK för Node.js. Du kan ändra en simulerad enhetsapp för att ta emot meddelanden från moln till enhet och ändra en backend-app för att skicka meddelanden från moln-till-enhet.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: baefd05b562d688b662bf988c7b36a0e9cd154b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61441856"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Skicka meddelanden från moln till enhet med IoT Hub (Node)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduktion
Azure IoT Hub är en helt hanterad tjänst som hjälper dig att aktivera pålitlig och säker dubbelriktad kommunikation mellan miljontals enheter och tillhandahåller serverdelen. Den [Kom igång med IoT Hub](quickstart-send-telemetry-node.md) kursen visar hur du skapar en IoT-hubb, etablera en enhetsidentitet i den och koda en simulerad enhetsapp som skickar meddelanden från enheten till molnet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här självstudien bygger på [Kom igång med IoT Hub](quickstart-send-telemetry-node.md). Den visar hur du:

* Skicka meddelanden från moln till enhet på en enhet via IoT Hub från lösningens backend-servrar.
* Ta emot meddelanden från molnet till enheten på en enhet.
* Från lösningens backend-servrar, begära leverans bekräftelse (*feedback*) för meddelanden som skickas till en enhet från IoT Hub.

Du hittar mer information om meddelanden från molnet till enheten i den [utvecklarhandboken för IoT Hub](iot-hub-devguide-messaging.md).

I slutet av den här kursen kan du köra två Node.js-konsolappar:

* **SimulatedDevice**, en modifierad version av appen som skapats i [Kom igång med IoT Hub](quickstart-send-telemetry-node.md), som ansluter till din IoT hub och tar emot meddelanden från molnet till enheten.

* **SendCloudToDeviceMessage**, som skickar ett moln-till-enhet-meddelande till den simulerade enhetsappen via IoT Hub och sedan ta emot dess leverans bekräftelse.

> [!NOTE]
> IoT Hub har SDK-stöd för många enhetsplattformar och språk (inklusive C, Java och Javascript) via SDK: er för Azure IoT-enheter. Stegvisa instruktioner om hur du ansluter enheten till den här självstudien kod och vanligen på Azure IoT Hub finns i den [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot).
>

För att kunna genomföra den här kursen behöver du följande:

* Node.js version 4.0.x eller senare.
* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial) på bara några minuter.)

## <a name="receive-messages-in-the-simulated-device-app"></a>Ta emot meddelanden i den simulerade enhetsappen

I det här avsnittet ska du ändra den simulerade enhetsappen som du skapade i [Kom igång med IoT Hub](quickstart-send-telemetry-node.md) att ta emot meddelanden från moln till enhet från IoT hub.

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
   > Om du använder HTTPS i stället för MQTT eller AMQP som transport, den **DeviceClient** instans söker efter meddelanden från IoT Hub sällan (mindre än var 25: e minut). Mer information om skillnaderna mellan MQTT-, AMQP- och HTTPS-stöd och IoT Hub-begränsning finns i den [utvecklarhandboken för IoT Hub](iot-hub-devguide-messaging.md).
   >

## <a name="send-a-cloud-to-device-message"></a>Skicka ett moln-till-enhet-meddelande

I det här avsnittet skapar du en Node.js-konsolapp som skickar meddelanden från molnet till enheten till den simulerade enhetsappen. Du behöver enhets-ID för enheten som du lade till i den [Kom igång med IoT Hub](quickstart-send-telemetry-node.md) självstudien. Du måste också IoT Hub-anslutningssträngen för hubben som du hittar i den [Azure-portalen](https://portal.azure.com).

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

5. Lägg till följande kod i **SendCloudToDeviceMessage.js** fil. Ersätt ”{iot hub-anslutningssträngen}” platshållarvärdet med IoT Hub-anslutningssträngen för hubben som du skapade i den [Kom igång med IoT Hub](quickstart-send-telemetry-node.md) självstudien. Ersätt platshållaren ”{enhets-id}” med enhets-ID för enheten som du lade till i den [Kom igång med IoT Hub](quickstart-send-telemetry-node.md) självstudien:
   
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
   
    ![Kör den simulerade enhetsappen](./media/iot-hub-node-node-c2d/receivec2d.png)

2. I en kommandotolk i den **sendcloudtodevicemessage** mapp, kör följande kommando för att skicka ett moln-till-enhet-meddelande och vänta på bekräftelse-feedback:
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Kör appen för att skicka kommandot moln till enhet](./media/iot-hub-node-node-c2d/sendc2d.png)
   
   > [!NOTE]
   > Den här självstudien implementerar inte någon återförsöksprincip sätt. I produktionskoden bör du implementera principer för omförsök (till exempel exponentiell backoff) vilket rekommenderas i artikeln, [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Nästa steg

I de här självstudierna lärde du dig att skicka och ta emot meddelanden från moln till enhet. 

Exempel på fullständiga lösningar för slutpunkt till slutpunkt som använder IoT Hub finns i [Azure IoT lösningsacceleratorn för fjärrövervakning](https://azure.microsoft.com/documentation/suites/iot-suite/).

Mer information om hur du utvecklar lösningar med IoT Hub finns det [utvecklarhandboken för IoT Hub](iot-hub-devguide.md).