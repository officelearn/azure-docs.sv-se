---
title: Moln-till-enhet-meddelanden med Azure IoT Hub (nod) | Microsoft-dokument
description: Så här skickar du meddelanden från molnet till enheten till en enhet från en Azure IoT-hubb med Azure IoT SDK:er för Node.js. Du ändrar en simulerad enhetsapp för att ta emot meddelanden från molnet till enheten och ändrar en backend-app för att skicka meddelanden från molnet till enheten.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: a1e0e3623692321e5c69e4b9c5a26ff82a1c47a0
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732342"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Skicka meddelanden från molnet till enheten med IoT Hub (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub är en fullständigt hanterad tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan miljontals enheter och en lösningsbakdel. [Snabbstarten Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md) visar hur du skapar en IoT-hubb, etablerar en enhetsidentitet i den och kodar en simulerad enhetsapp som skickar meddelanden från enhet till moln.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Den här självstudien bygger på [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md). Det visar dig hur du:

* Från din lösningsbaksida skickar du meddelanden från molnet till enheten till en enda enhet via IoT Hub.
* Ta emot meddelanden från molnet till enheten på en enhet.
* Från din lösningsbaksida, begär leveransbekräftelse *(feedback)* för meddelanden som skickas till en enhet från IoT Hub.

Du hittar mer information om meddelanden från molnet till enheten i [utvecklarhandboken för IoT Hub](iot-hub-devguide-messaging.md).

I slutet av den här självstudien kör du två Node.js-konsolappar:

* **SimulatedDevice**, en modifierad version av appen som skapats i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md), som ansluter till din IoT-hubb och tar emot meddelanden från molnet till enheten.

* **SendCloudToDeviceMessage**, som skickar ett meddelande från molnet till enheten till den simulerade enhetsappen via IoT Hub, och sedan får sin leveransbekräftelse.

> [!NOTE]
> IoT Hub har SDK-stöd för många enhetsplattformar och -språk (inklusive C, Java, Python och Javascript) via Azure IoT-enhetSDK:er. Stegvisa instruktioner om hur du ansluter enheten till den här självstudiens kod och i allmänhet till Azure IoT Hub finns i [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot).
>

## <a name="prerequisites"></a>Krav

* Node.js version 10.0.x eller senare. [Förbered utvecklingsmiljön](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) beskriver hur du installerar Node.js för den här självstudien på antingen Windows eller Linux.

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa ett [kostnadsfritt konto på](https://azure.microsoft.com/pricing/free-trial) bara några minuter.)

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här artikeln använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Ta emot meddelanden i den simulerade enhetsappen

I det här avsnittet ändrar du den simulerade enhetsappen som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md) för att ta emot meddelanden från molnet till enheten från IoT-hubben.

1. Öppna filen **SimuleradDevice.js** med hjälp av en textredigerare. Den här filen finns i mappen **iot-hub\Quickstarts\simulated-device** bort från rotmappen för exempelkoden Node.js som du hämtade i [skicka telemetrin från en enhet till en snabbstart för IoT-hubb.](quickstart-send-telemetry-node.md)

2. Registrera en hanterare hos enhetsklienten för att ta emot meddelanden som skickats från IoT Hub. Lägg till `client.on` anropet i strax efter raden som skapar enhetsklienten som i följande utdrag:

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

    I det här exemplet anropar enheten den **fullständiga** funktionen för att meddela IoT Hub att den har bearbetat meddelandet. Samtalet som **ska slutföras** krävs inte om du använder MQTT-transport och kan utelämnas. Det krävs för HTTPS och AMQP.
  
   > [!NOTE]
   > Om du använder HTTPS i stället för MQTT eller AMQP som transport söker **DeviceClient-instansen** sällan efter meddelanden från IoT Hub (mindre än var 25:e minut). Mer information om skillnaderna mellan stöd för MQTT, AMQP och HTTPS och Begränsning av IoT Hub finns i [utvecklarhandboken för IoT Hub](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Hämta anslutningssträngen för IoT-hubb

I den här artikeln skapar du en serverd-tjänst för att skicka meddelanden från molnet till enheten via IoT-hubben som du skapade i [Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-node.md). Om du vill skicka meddelanden från molnet till enheten behöver tjänsten behörigheten **för tjänsten ansluta.** Som standard skapas varje IoT-hubb med en princip med namnet delad åtkomst med namnet **tjänst** som ger den här behörigheten.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Skicka ett meddelande från molnet till enheten

I det här avsnittet skapar du en Node.js-konsolapp som skickar meddelanden från molnet till enheten till den simulerade enhetsappen. Du behöver enhets-ID:t för den enhet som du lade till i [skicka telemetrin från en enhet till en snabbstart för IoT-hubb.](quickstart-send-telemetry-node.md) Du behöver också anslutningssträngen för IoT-hubben som du kopierade tidigare i [Hämta anslutningssträngen för IoT-hubben](#get-the-iot-hub-connection-string).

1. Skapa en tom mapp med namnet **sendcloudtodevicemessage**. Skapa en package.json-fil med följande kommando i kommandotolken i mappen **sendcloudtodevicemessage.** Acceptera alla standardvärden:

    ```shell
    npm init
    ```

2. Kör följande kommando i kommandotolken i mappen **sendcloudtodevicemessage** för att installera **azure-iothub-paketet:**

    ```shell
    npm install azure-iothub --save
    ```

3. Skapa en **SendCloudToDeviceMessage.js-fil** med hjälp av en textredigerare i mappen **sendcloudtodevicemessage.**

4. Lägg till `require` följande satser i början av filen **SendCloudToDeviceMessage.js:**

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Lägg till följande kod i **filen SendCloudToDeviceMessage.js.** Ersätt platshållarvärdena {iot hub connection string}" och "{device id}" med ioT-hubbanslutningssträngen och enhets-ID:et som du noterade tidigare:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Lägg till följande funktion för att skriva ut driftresultat i konsolen:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Lägg till följande funktion för att skriva ut meddelanden om leveransfeedback till konsolen:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Lägg till följande kod för att skicka ett meddelande till enheten och hantera feedbackmeddelandet när enheten bekräftar meddelandet från molnet till enheten:

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

9. Spara och stäng **Filen SendCloudToDeviceMessage.js.**

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. I kommandotolken i mappen **simulerad enhet** kör du följande kommando för att skicka telemetri till IoT Hub och för att lyssna efter meddelanden från molnet till enheten:

    ```shell
    node SimulatedDevice.js
    ```

    ![Köra den simulerade enhetsappen](./media/iot-hub-node-node-c2d/receivec2d.png)

2. Kör följande kommando vid en kommandotolk i mappen **sendcloudtodevicemessage** för att skicka ett meddelande från molnet till enheten och vänta på bekräftelsefeedback:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Kör appen för att skicka kommandot moln-till-enhet](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > För enkelhetens skull implementerar den här självstudien inte någon princip för återförsök. I produktionskoden bör du implementera principer för återförsök (till exempel exponentiell backoff), som föreslås i artikeln [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skicka och ta emot meddelanden från molnet till enheten.

Information om hur du ser exempel på kompletta heltäckande lösningar som använder IoT Hub finns i [Azure IoT Remote Monitoring solution accelerator](https://azure.microsoft.com/documentation/suites/iot-suite/).

Mer information om hur du utvecklar lösningar med IoT Hub finns i [utvecklarhandboken för IoT Hub](iot-hub-devguide.md).
