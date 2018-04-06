---
title: Vidarebefordra meddelanden med Azure IoT Hub (nod) | Microsoft Docs
description: Så här bearbeta meddelanden för Azure IoT Hub-enhet till moln genom att använda regler för Routning och anpassade slutpunkter för att skicka meddelanden till andra backend-tjänster.
services: iot-hub
documentationcenter: node
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: v-masebo;dobett
ms.openlocfilehash: 02edb06d2d871cffac717358e33a6720c444a9b3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="routing-messages-with-iot-hub-node"></a>Vidarebefordra meddelanden med IoT-hubben (nod)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Den här kursen bygger på den [Kom igång med IoT-hubb] kursen.  Självstudier:

* Visar hur du använder regler för routning för att skicka meddelanden från enhet till moln i ett enkelt och konfigurationsbaserade sätt.
* Visar hur du isolera interaktiva meddelanden som kräver omedelbar åtgärd från lösningens serverdel för vidare bearbetning.  En enhet kan till exempel skicka larmmeddelandet som utlöser lägga till ett ärende i ett CRM-system.  Däremot-datapunkt meddelanden, till exempel temperatur telemetri, mata in en analytics-motorn.

I slutet av den här kursen kan köra du tre Node.js-konsolappar:

* **SimulatedDevice.js**, en modifierad version av app som skapats i den [Kom igång med IoT-hubb] självstudiekursen skickar datapunkt meddelanden från enhet till moln alla andra och interaktiva enhet till moln meddelanden per slumpmässiga intervall. Den här appen använder protokollet MQTT kommunicerar med IoT-hubben.
* **ReadDeviceToCloudMessages.js** visar telemetri som skickats av din enhetsapp.
* **ReadCriticalQueue.js** tar bort viktiga meddelanden från Service Bus-kö som är kopplade till IoT-hubben.

> [!NOTE]
> IoT-hubben har SDK stöd för många vilka plattformar och språk, inklusive C, Java och JavaScript. Instruktioner om hur du ersätter enheten i den här självstudiekursen med en fysisk enhet och ansluta enheter till en IoT-hubb finns i [Azure IoT Developer Center].

För att kunna genomföra den här kursen behöver du följande:

* En fullständig fungerande version av den [Kom igång med IoT-hubb] kursen.
* Node.js version 4.0.x eller senare.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

Vi rekommenderar också läsa mer om [Azure Storage] och [Azure Service Bus].

## <a name="send-interactive-messages-from-a-device-app"></a>Skicka interaktiva meddelanden från en enhetsapp
I det här avsnittet kan du ändra appen för enheter som du skapade i den [Kom igång med IoT-hubb] kursen att ibland skicka meddelanden som kräver omedelbar bearbetning.

1. Använd en textredigerare för att öppna den **simulateddevice\SimulatedDevice.js** fil. Den här filen innehåller koden för den **SimulatedDevice** app som du skapade i den [Kom igång med IoT-hubb] kursen.

2. Ersätt den **connectCallback** funktionen med följande kod:

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
    
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
        var data, message;
        if (Math.random() > 0.7) {
            if (Math.random() > 0.5) {
                data = "This is a critical message.";
                message = new Message(data);
                message.properties.add("level", "critical");
            } else {
                data = "This is a storage message.";
                message = new Message(data);
                message.properties.add("level", "storage");
            }
            console.log("Sending message: " + message.getData());
        }
        else {
                var temperature = 20 + (Math.random() * 15);
                var humidity = 60 + (Math.random() * 20);            
                data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
                message = new Message(data);
                message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
                console.log("Sending message: " + message.getData());
            }
        client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
   
    Den här metoden lägger slumpmässigt till egenskapen `"level": "critical"` och `"level": "storage"` på meddelanden som skickas av enheten, som simulerar ett meddelande som kräver omedelbar åtgärd som programmet backend- eller som behöver lagras permanent. Programmet stöder vidarebefordra meddelanden utifrån meddelandetexten.
   
   > [!NOTE]
   > Du kan använda meddelandeegenskaper skicka meddelanden för olika scenarier, inklusive kall sökväg bearbetning, förutom varm sökväg exemplet som visas här.

2. Spara och Stäng den **simulateddevice\SimulatedDevice.js** fil.

    > [!NOTE]
    > Vi rekommenderar starkt att du använder en återförsöksprincip som exponentiell backoff enligt förslaget i MSDN-artikel [hantering av tillfälliga fel].

## <a name="add-service-bus-queue-to-your-iot-hub-and-route-messages-to-it"></a>Lägg till Service Bus-kö i IoT-hubb och flöde meddelanden till den

I det här avsnittet skapar du en Service Bus-kö, ansluta till din IoT-hubb och konfigurera din IoT-hubb för att skicka meddelanden till kön baserat på förekomsten av en egenskap i meddelandet. Mer information om hur du bearbeta meddelanden från Service Bus-köer finns [Kom igång med köer][lnk-sb-queues-node].

1. Skapa en Service Bus-kö som beskrivs i [Kom igång med köer][lnk-sb-queues-node]. Anteckna namnet på namnområdet och kön.

    > [!NOTE]
    > Service Bus-köer och ämnen som används som IoT-hubbslutpunkter inte får ha **sessioner** eller **dubblettidentifiering** aktiverat. Om något av dessa alternativ är aktiverade ändpunkt som **inte åtkomlig** i Azure-portalen.

2. Öppna din IoT-hubb i Azure-portalen och klicka på **slutpunkter**.

    ![Slutpunkter i IoT-hubb][30]

3. I den **slutpunkter** bladet, klickar du på **Lägg till** längst upp för att lägga till kön till din IoT-hubb. Namnet på slutpunkten **CriticalQueue** och markera med hjälp av listrutorna **Service Bus-kö**, Service Bus-namnrymd som kön finns och namnet på din kö. När du är klar klickar du på **OK** längst ned.  

    ![Lägga till en slutpunkt][31]

4. Klicka på **vägar** i din IoT-hubb. Klicka på **Lägg till** längst upp på bladet för att skapa en regel för vidarebefordran som skickar meddelanden till kön du just lagt till. Välj **meddelanden** som datakällan. Ange `level="critical"` som villkor och välj **CriticalQueue** som en anpassad slutpunkt som routning regeln slutpunkt. Klicka på **spara** längst ned.  

    ![Lägga till en väg][32]

    Kontrollera återställningsplats vägen är inställd på **på**. Den här inställningen är standardkonfigurationen för en IoT-hubb.

    ![Fallback-väg][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Valfritt) Läsa från kön slutpunkten

I det här avsnittet skapar du en Node.js-konsolprogram som läser kritiska meddelanden från IoT Service Bus. Se ytterligare information i [Kom igång med köer][lnk-sb-queues-node]. 

1. Skapa en tom mapp med namnet `readcriticalqueue`. Skapa en package.json-fil i mappen `readcriticalqueue` genom att köra följande kommando i kommandotolken. Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

2. Vid en kommandotolk i den `readcriticalqueue` mapp, kör följande kommando för att installera den **azure** paketet:

    ```cmd/sh
    npm install azure --save
    ```

3. Med hjälp av en textredigerare, skapa en **ReadCriticalQueue.js** filen i den `readcriticalqueue` mapp.

4. Lägg till följande `require` instruktioner i början av den **ReadCriticalQueue.js** fil:

    ```nodejs
    'use strict';

    var azure = require('azure');
    ```

5. Lägg till följande variabeldeklarationen och ersätta platshållarvärdena med IoT Service Bus sträng och kön anslutningsnamnet:

    ```nodejs
    var connectionString = '{iotservicebus connection string}';
    var queueName = '{iotservice bus queue name}';
    ```

6. Lägg till följande två funktioner som skriver ut utdata till konsolen:

    ```nodejs
    var serviceBusService = azure.createServiceBusService(connectionString);

    setInterval(function() {serviceBusService.receiveQueueMessage(queueName, function(error, receivedMessage) {
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        } else { console.log(error); }
        });
    }, 3000);
    ```

7. Spara och Stäng den **ReadCriticalQueue.js** fil.

## <a name="run-the-applications"></a>Köra programmen

Nu är du redo att köra tre program.

1. Att köra den **ReadDeviceToCloudMessages.js** program i en kommandotolk eller shell navigera till mappen readdevicetocloudmessages och kör följande kommando:

   ```cmd/sh
   node ReadDeviceToCloudMessages.js
   ```

   ![Kör Läs-d2c-meddelanden][readd2c]

2. Att köra den **ReadCriticalQueue.js** program i en kommandotolk eller shell navigera till mappen readcriticalqueue och kör följande kommando:

   ```cmd/sh
   node ReadCriticalQueue.js
   ```
   
   ![Kör Läs kritiska meddelanden][readqueue]

3. Att köra den **SimulatedDevice.js** i en kommandotolk eller shell appen, navigera till mappen simulateddevice och kör följande kommando:

   ```cmd/sh
   node SimulatedDevice.js
   ```
   
   ![Kör simulerade enheten][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Valfritt) Lägg till lagringsbehållaren i IoT-hubb och flöde meddelanden till den

I det här avsnittet, skapa ett lagringskonto, ansluta till din IoT-hubb och konfigurera din IoT-hubb för att skicka meddelanden till kontot utifrån förekomsten av en egenskap i meddelandet. Mer information om hur du hanterar lagring finns [komma igång med Azure Storage][Azure Storage].

 > [!NOTE]
   > Om du inte är begränsad till en **Endpoint**, du kan konfigurera den **StorageContainer** förutom den **CriticalQueue** och kör både simulatneously.

1. Skapa ett lagringskonto som beskrivs i [Azure Storage-dokumentation][lnk-storage]. Anteckna namnet på kontot.

2. Öppna din IoT-hubb i Azure-portalen och klicka på **slutpunkter**.

3. I den **slutpunkter** bladet väljer den **CriticalQueue** slutpunkt och klicka på **ta bort**. Klicka på **Ja**, och klicka sedan på **Lägg till**. Namnet på slutpunkten **StorageContainer** och markera med hjälp av listrutorna **Azure Storage-behållare**, och skapa en **lagringskonto** och en **lagring behållaren**.  Anteckna namnen.  När du är klar klickar du på **OK** längst ned. 

 > [!NOTE]
   > Om du inte är begränsad till en **Endpoint**, du behöver inte ta bort den **CriticalQueue**.

4. Klicka på **vägar** i din IoT-hubb. Klicka på **Lägg till** längst upp på bladet för att skapa en regel för vidarebefordran som skickar meddelanden till kön du just lagt till. Välj **meddelanden** som datakällan. Ange `level="storage"` som villkor och välj **StorageContainer** som en anpassad slutpunkt som routning regeln slutpunkt. Klicka på **spara** längst ned.  

    Kontrollera återställningsplats vägen är inställd på **på**. Den här inställningen är standardkonfigurationen för en IoT-hubb.

1. Kontrollera att tillämpningsprogrammet tidigare **SimulatedDevice.js** fortfarande körs. 

1. I Azure-portalen går du till ditt lagringskonto under **Blob-tjänsten**, klicka på **Bläddra blobbar...** .  Välj din behållare, navigera till och klicka JSON-filen och på **hämta** att visa data.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen beskrivs hur du ska skicka meddelanden från enhet till moln med hjälp av meddelandet routningsfunktionen för IoT-hubb.

Den [hur du skickar meddelanden moln till enhet med IoT-hubben] [ lnk-c2d] visar hur du kan skicka meddelanden till dina enheter från din lösningens serverdel.

Exempel på fullständiga lösningar för slutpunkt till slutpunkt med IoT-hubb finns [Azure IoT Suite][lnk-suite].

Mer information om hur du utvecklar lösningar med IoT-hubb finns i [IoT-hubb Utvecklarhandbok].

Läs mer om meddelanderoutning i IoT-hubb i [skicka och ta emot meddelanden med IoT-hubben][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-node-node-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-node-node-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-node-node-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-node-node-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-node-node-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-node-node-process-d2c/route-creation.png
[33]: ./media/iot-hub-node-node-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-nodejs-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT-hubb Utvecklarhandbok]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Kom igång med IoT-hubb]: iot-hub-node-node-getstarted.md
[Azure IoT Developer Center]: https://azure.microsoft.com/develop/iot
[hantering av tillfälliga fel]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Hantering av tillfälligt fel]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-node-node-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/azure/storage/