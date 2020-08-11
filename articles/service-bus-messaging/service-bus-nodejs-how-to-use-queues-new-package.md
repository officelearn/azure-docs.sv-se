---
title: Använda Azure/Service-Bus-köer i Node.js
description: Lär dig hur du skriver ett NodeJS-program för att skicka meddelanden till och ta emot meddelanden från en Service Bus kö med hjälp av det nya @azure/service-bus paketet.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-javascript
ms.openlocfilehash: c082eff4f70b0255b7701340ac6e8b377223c0b5
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88076871"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Snabb start: använda Service Bus köer med Node.js och Azure/Service-Bus-paketet
I den här självstudien får du lära dig hur du skriver ett NodeJS-program för att skicka meddelanden till och ta emot meddelanden från en Service Bus kö med hjälp av det nya [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) paketet. Det här paketet använder det snabbare [AMQP 1,0-protokollet](service-bus-amqp-overview.md) medan det äldre [Azure-SB-](https://www.npmjs.com/package/azure-sb) paketet används [Service Bus REST-API: er för körnings tid](/rest/api/servicebus/service-bus-runtime-rest). Exemplen är skrivna i Java Script.

## <a name="prerequisites"></a>Förutsättningar
- En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [förmåner för MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Om du inte har en kö att arbeta med följer du stegen i artikeln [använd Azure Portal för att Service Bus skapa](service-bus-quickstart-portal.md) en kö. Anteckna anslutnings strängen för Service Bus-instansen och namnet på kön som du skapade. Vi kommer att använda dessa värden i exemplen.

> [!NOTE]
> - Den här självstudien fungerar med exempel som du kan kopiera och köra med [NodeJS](https://nodejs.org/). Instruktioner för hur du skapar ett Node.js program finns i [skapa och distribuera ett Node.js program till en Azure-webbplats](../app-service/quickstart-nodejs.md)eller [Node.js moln tjänst med Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Det nya [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) paketet stöder inte skapande av köer än. Använd [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) paketet om du vill skapa dem program mässigt.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Installera paketet med NPM (Node Package Manager)
Om du vill installera NPM-paketet för Service Bus öppnar du en kommando tolk med `npm` sökvägen och ändrar katalogen till den mapp där du vill ha dina exempel och kör sedan det här kommandot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
Att interagera med en Service Bus kö börjar med att instansiera klassen [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) och att använda den för att instansiera [QueueClient](/javascript/api/@azure/service-bus/queueclient) -klassen. När du har en Queue-klient kan du skapa en avsändare och använda antingen [send](/javascript/api/@azure/service-bus/sender#send-sendablemessageinfo-) eller [sendBatch](/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) -metoden på den för att skicka meddelanden.

1. Öppna din favorit redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en fil med namnet `send.js` och klistra in nedanstående kod i den. Den här koden skickar 10 meddelanden till kön.

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
3. Ange anslutnings strängen och namnet för din kö i ovanstående kod.
4. Kör sedan kommandot `node send.js` i en kommando tolk för att köra den här filen.

Grattis! Du skickade bara meddelanden till en Service Bus-kö.

Meddelanden har några standard egenskaper som `label` och `messageId` som du kan ställa in när du skickar. Om du vill ange egna egenskaper använder du `userProperties` , vilket är ett JSON-objekt som kan innehålla nyckel/värde-par av dina anpassade data.

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Det finns ingen gräns för antalet meddelanden som finns i en kö, men det finns ett tak för den totala storleken på de meddelanden som innehas av en kö. Den här köstorleken definieras när kön skapas, med en övre gräns på 5 GB. Mer information om kvoter finns i [Service Bus kvoter](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
Att interagera med en Service Bus kö börjar med att instansiera klassen [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) och att använda den för att instansiera [QueueClient](/javascript/api/@azure/service-bus/queueclient) -klassen. När du har en Queue-klient kan du skapa en mottagare och använda antingen [receiveMessages](/javascript/api/@azure/service-bus/receiver#receivemessages-number--undefined---number-) eller [registerMessageHandler](/javascript/api/@azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) -metoden på den för att ta emot meddelanden.

1. Öppna din favorit redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en fil med namnet `recieve.js` och klistra in nedanstående kod i den. Den här koden kommer att försöka ta emot 10 meddelanden från kön. Det faktiska antalet du får beror på antalet meddelanden i kön och nätverks svars tiden.

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
3. Ange anslutnings strängen och namnet för din kö i ovanstående kod.
4. Kör sedan kommandot `node receiveMessages.js` i en kommando tolk för att köra den här filen.

Grattis! Du har bara tagit emot meddelanden från en Service Bus kö.

Metoden [createReceiver](/javascript/api/@azure/service-bus/queueclient#createreceiver-receivemode-) tar i ett `ReceiveMode` som är en Enum med värdena [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) och [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Kom ihåg att [lösa dina meddelanden](message-transfers-locks-settlement.md#settling-receive-operations) om du använder `PeekLock` läget genom att använda någon av `complete()` metoderna,, `abandon()` `defer()` , eller för att ange ett `deadletter()` meddelande.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer gör det möjligt för användare att ansluta till en Service Bus namnrymd och administrera meddelande enheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjlighet att testa ämnen, köer, prenumerationer, relä tjänster, Notification Hub och Event Hub. 

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser.
- [Köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
- Andra [NodeJS-exempel för Service Bus på GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)