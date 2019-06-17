---
title: Hur du använder Azure Service Bus-köer i Node.js – azure/service bus | Microsoft Docs
description: Lär dig hur du använder Service Bus-köer i Azure från en Node.js-app.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 7aacefde9c037fcce64d9256e35082eb04e0a2f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65988356"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Hur du använder Service Bus-köer med Node.js och azure/service bus-paketet
> [!div class="op_multi_selector" title1="Programmeringsspråk" title2="Node.js för operativsystem"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

I den här självstudien får du lära dig hur du skriver ett Nodejs-program för att skicka meddelanden till och ta emot meddelanden från en Service Bus-kö med hjälp av den nya [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) paketet. Det här paketet används den snabbare [AMQP 1.0-protokollet](service-bus-amqp-overview.md) medan den äldre [azure sb](https://www.npmjs.com/package/azure-sb) paketet som används [Service Bus REST API: er för körning](/rest/api/servicebus/service-bus-runtime-rest). Exemplen är skrivna i JavaScript.

## <a name="prerequisites"></a>Nödvändiga komponenter
- En Azure-prenumeration. Du behöver ett Azure-konto för att slutföra den här självstudien. Du kan aktivera din [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Om du inte har en kö för att arbeta med, Följ stegen i den [Använd Azure portal för att skapa en Service Bus-kö](service-bus-quickstart-portal.md) artikeln om du vill skapa en kö. Anteckna anslutningssträngen för din Service Bus-instans och namnet på kön som du skapade. Vi använder dessa värden i exemplen.

> [!NOTE]
> - Den här självstudien fungerar med exempel som du kan kopiera och köra med [Nodejs](https://nodejs.org/). Anvisningar om hur du skapar ett Node.js-program finns i [skapa och distribuera en Node.js-program till en Azure-webbplats](../app-service/app-service-web-get-started-nodejs.md), eller [Node.js-molntjänst med hjälp av Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Den nya [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) paketet stöder inte skapandet av köer ännu. Använd den [ @azure/arm-servicebus ](https://www.npmjs.com/package/@azure/arm-servicebus) Paketera om du vill skapa dem programmässigt.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Installera paketet med NPM (Node Package Manager)
Om du vill installera npm-paketet för Service Bus, öppna en kommandotolk som har `npm` i sökvägen, ändra katalogen till mappen där du vill ha exempel och kör sedan det här kommandot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
Interagera med en Service Bus-kö som börjar med instansiera den [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) klass och använder det för att skapa en instans av den [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) klass. När du har kö-klienten kan du skapa en avsändare och använda antingen [skicka](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) eller [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) metod på den för att skicka meddelanden.

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en fil med namnet `send.js` och klistra in den nedan kod till den. Den här koden skickar 10 meddelanden till kön.

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
3. Ange anslutningssträngen och namnet på din kö i koden ovan.
4. Kör sedan kommandot `node send.js` i Kommandotolken och kör den här filen.

Grattis! Du har skickat meddelanden till en Service Bus-kö.

Meddelanden har några standardegenskaper som `label` och `messageId` som du kan ange när du skickar. Om du vill ange anpassade egenskaper kan använda den `userProperties`, vilket är ett json-objekt som kan innehålla nyckel / värde-par med dina egna data.

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Det finns ingen gräns för hur många meddelanden som ligger i en kö men det finns ett tak för den totala storleken på de meddelanden som ligger i en kö. Den här köstorleken definieras när kön skapas, med en övre gräns på 5 GB. Mer information om kvoter finns i [Service Bus-kvoter](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
Interagera med en Service Bus-kö som börjar med instansiera den [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) klass och använder det för att skapa en instans av den [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) klass. När du har kö-klienten kan du skapa en mottagare och använda antingen [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) eller [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) metod på den för att ta emot meddelanden.

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en fil med namnet `recieve.js` och klistra in den nedan kod till den. Den här koden kommer att försöka att ta emot 10 meddelanden från kön. Det faktiska antalet som du får beror på hur många meddelanden i kön och nätverksfördröjning.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.ReceiveAndDelete);
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
3. Ange anslutningssträngen och namnet på din kö i koden ovan.
4. Kör sedan kommandot `node receiveMessages.js` i Kommandotolken och kör den här filen.

Grattis! Du har precis fått meddelanden från en Service Bus-kö.

Den [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) metoden tar en `ReceiveMode` som är en uppräkning med värden [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) och [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Kom ihåg att [reglera dina meddelanden](message-transfers-locks-settlement.md#settling-receive-operations) om du använder den `PeekLock` läge med någon av `complete()`, `abandon()`, `defer()`, eller `deadletter()` metoder på meddelandet.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer tillåter användare att ansluta till ett Service Bus-namnområde och administrera meddelandeentiteter på ett enkelt sätt. Verktyget tillhandahåller avancerade funktioner som import/export-funktionalitet eller möjligheten att testa ämne, köer, prenumerationer, relätjänster, meddelandehubbar och händelser hubs. 

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser.
- [Köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
- Se andra [Nodejs-exempel för Service Bus på GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)

