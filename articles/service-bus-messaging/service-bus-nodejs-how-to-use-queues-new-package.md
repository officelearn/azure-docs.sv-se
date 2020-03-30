---
title: Så här använder du azure/service-bus-köer i Node.js
description: Läs om hur du skriver ett Nodejs-program för att skicka meddelanden @azure/service-bus till och ta emot meddelanden från en Service Bus-kö med det nya paketet.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: c2e24e9dea2c8463294c85f04c9e4d7d2da17261
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330659"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Snabbstart: Så här använder du Service Bus-köer med Node.js och azure/service-bus-paketet
I den här självstudien får du lära dig hur du skriver ett Nodejs-program [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) för att skicka meddelanden till och ta emot meddelanden från en Service Bus-kö med det nya paketet. Det här paketet använder det snabbare [AMQP 1.0-protokollet](service-bus-amqp-overview.md) medan det äldre [azure-sb-paketet](https://www.npmjs.com/package/azure-sb) använde [SERVICE Bus REST-körnings-API:er](/rest/api/servicebus/service-bus-runtime-rest). Exemplen är skrivna i JavaScript.

## <a name="prerequisites"></a>Krav
- En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [msdn-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Om du inte har en kö att arbeta med följer du stegen i [Azure-portalen för att skapa en](service-bus-quickstart-portal.md) köartikel för Service Bus för att skapa en kö. Anteckna anslutningssträngen för Service Bus-instansen och namnet på kön som du skapade. Vi använder dessa värden i proverna.

> [!NOTE]
> - Den här självstudien fungerar med exempel som du kan kopiera och köra med [Nodejs](https://nodejs.org/). Instruktioner om hur du skapar ett Node.js-program finns i [Skapa och distribuera ett Node.js-program till en Azure-webbplats](../app-service/app-service-web-get-started-nodejs.md)eller [node.js molntjänst med Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Det [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) nya paketet stöder inte skapandet av köer ännu. Använd paketet [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) om du vill skapa dem programmässigt.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Installera paketet med NPM (Node Package Manager)
Om du vill installera npm-paketet för Service `npm` Bus öppnar du en kommandotolk som har i sökvägen, ändrar katalogen till mappen där du vill ha dina exempel och kör sedan det här kommandot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
Interagera med en Service Bus kö börjar med att instansiera [klassen ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) och använda den för att instansiera [klassen QueueClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) När du har köklienten kan du skapa en avsändare och använda antingen [send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) eller [sendBatch-metoden](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) på den för att skicka meddelanden.

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en `send.js` fil som heter och klistra in nedanstående kod i den. Den här koden skickar 10 meddelanden till din kö.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
      try {
        for (let i = 0; i < 10; i++) {
          const message= {
            body: `Hello world! ${i}`,
            label: `test`,
            userProperties: {
                myCustomPropertyName: `my custom property value ${i}`
           }
          };
          console.log(`Sending message: ${message.body}`);
          await sender.send(message);
        }
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Ange anslutningssträngen och namnet på din kö i ovanstående kod.
4. Kör sedan `node send.js` kommandot i en kommandotolk för att köra den här filen.

Grattis! Du har precis skickat meddelanden till en servicebusskö.

Meddelanden har vissa standardegenskaper som `label` och `messageId` som du kan ange när du skickar. Om du vill ange några anpassade `userProperties`egenskaper använder du , som är ett json-objekt som kan innehålla nyckelvärdespar av dina anpassade data.

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Det finns ingen gräns för antalet meddelanden som finns i en kö, men det finns ett tak för den totala storleken på meddelanden som innehas av en kö. Den här köstorleken definieras när kön skapas, med en övre gräns på 5 GB. Mer information om kvoter finns i [Service Bus-kvoter](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
Interagera med en Service Bus kö börjar med att instansiera [klassen ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) och använda den för att instansiera [klassen QueueClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) När du har köklienten kan du skapa en mottagare och använda antingen [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) eller [registerMessageHandler-metoden](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) på den för att ta emot meddelanden.

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en `recieve.js` fil som heter och klistra in nedanstående kod i den. Den här koden försöker ta emot 10 meddelanden från kön. Det faktiska antalet som du får beror på antalet meddelanden i kön och nätverksfördröjningen.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.receiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Ange anslutningssträngen och namnet på din kö i ovanstående kod.
4. Kör sedan `node receiveMessages.js` kommandot i en kommandotolk för att köra den här filen.

Grattis! Du har precis fått meddelanden från en servicebusskö.

Metoden [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) tar `ReceiveMode` in en som är en uppräkning med [värdena ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) och [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Kom ihåg att [lösa dina](message-transfers-locks-settlement.md#settling-receive-operations) `PeekLock` meddelanden om du `complete()` `abandon()`använder `defer()`läget `deadletter()` med någon av , , eller metoder i meddelandet.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer tillåter användare att ansluta till ett servicebussnamnområde och administrera meddelandeenheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjligheten att testa ämne, köer, prenumerationer, relätjänster, meddelandehubbar och händelsehubbar. 

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser.
- [Köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
- Kassan andra [Nodejs-exempel för Service Bus på GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)

