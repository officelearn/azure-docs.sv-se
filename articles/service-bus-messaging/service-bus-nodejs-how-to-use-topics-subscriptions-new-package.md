---
title: Använda azure/service-bus ämnen och prenumerationer med Node.js
description: 'Snabbstart: Lär dig hur du använder Service Bus-ämnen och prenumerationer i Azure från en Node.js-app.'
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: 6088b4c54ed16c5ef46d2c0671e619884cad29d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330625"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Snabbstart: Så här använder du servicebussavsnitt och prenumerationer med Node.js och azure/service-bus-paketet
I den här självstudien får du lära dig hur du skriver ett Node.js-program för att [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) skicka meddelanden till ett Service Bus-ämne och ta emot meddelanden från en Service Bus-prenumeration med det nya paketet. Det här paketet använder det snabbare [AMQP 1.0-protokollet](service-bus-amqp-overview.md) medan det äldre [azure-sb-paketet](https://www.npmjs.com/package/azure-sb) använde [SERVICE Bus REST-körnings-API:er](/rest/api/servicebus/service-bus-runtime-rest). Exemplen är skrivna i JavaScript.

## <a name="prerequisites"></a>Krav
- En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [msdn-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Om du inte har ett ämne och en prenumeration att arbeta med följer du stegen i [Azure-portalen för att skapa en servicebussavsnitt och prenumerationsartikel](service-bus-quickstart-topics-subscriptions-portal.md) för att skapa dem. Anteckna anslutningssträngen för Service Bus-instansen och namnen på det ämne och den prenumeration som du skapade. Vi använder dessa värden i proverna.

> [!NOTE]
> - Den här självstudien fungerar med exempel som du kan kopiera och köra med [Nodejs](https://nodejs.org/). Instruktioner om hur du skapar ett Node.js-program finns i [Skapa och distribuera ett Node.js-program till en Azure-webbplats](../app-service/app-service-web-get-started-nodejs.md)eller [Node.js Cloud Service med Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Det [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) nya paketet stöder inte skapandet av topcis och prenumerationer ännu. Använd paketet [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) om du vill skapa dem programmässigt.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Installera paketet med NPM (Node Package Manager)
Om du vill installera npm-paketet för Service `npm` Bus öppnar du en kommandotolk som har i sökvägen, ändrar katalogen till mappen där du vill ha dina exempel och kör sedan det här kommandot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne
Interagera med en Service Bus ämne börjar med instansiating [klassen ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) och använda den för att instansiera [klassen TopicClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) När du har ämnesklienten kan du skapa en avsändare och använda antingen [send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) eller [sendBatch-metoden](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) på den för att skicka meddelanden.

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en `send.js` fil som heter och klistra in nedanstående kod i den. Denna kod kommer att skicka 10 meddelanden till ditt ämne.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const topicClient = sbClient.createTopicClient(topicName);
      const sender = topicClient.createSender();
      
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

            await topicClient.close();
          } finally {
            await sbClient.close();
          }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Ange anslutningssträngen och namnet på ditt ämne i koden ovan.
4. Kör sedan `node send.js` kommandot i en kommandotolk för att köra den här filen. 

Grattis! Du har precis skickat meddelanden till en servicebusskö.

Meddelanden har vissa standardegenskaper som `label` och `messageId` som du kan ange när du skickar. Om du vill ange några anpassade `userProperties`egenskaper använder du , som är ett json-objekt som kan innehålla nyckelvärdespar av dina anpassade data.

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Det finns ingen gräns för antalet meddelanden som finns i ett ämne, men det finns en gräns för den totala storleken på de meddelanden som innehas av ett ämne. Den här ämnesstorleken definieras när ämnet skapas, med en övre gräns på 5 GB. Mer information om kvoter finns i [Service Bus-kvoter](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
Interagera med en Service Bus-prenumeration börjar med att instansiera [klassen ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) och använda den för att instansiera klassen [SubscriptionClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) När du har prenumerationsklienten kan du skapa en mottagare och använda antingen [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) eller [registerMessageHandler-metoden](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) på den för att ta emot meddelanden.

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en `recieve.js` fil som heter och klistra in nedanstående kod i den. Den här koden försöker ta emot 10 meddelanden från din prenumeration. Det faktiska antalet som du får beror på antalet meddelanden i prenumerationen och nätverksfördröjningen.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.receiveAndDelete);
      
      try {
        const messages = await receiver.receiveMessages(10);
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
        
        await subscriptionClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Ange anslutningssträngen och namnen på ditt ämne och din prenumeration i ovanstående kod.
4. Kör sedan `node receiveMessages.js` kommandot i en kommandotolk för att köra den här filen.

Grattis! Du har precis fått meddelanden från en Service Bus-prenumeration.

Metoden [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) tar `ReceiveMode` in en som är en uppräkning med [värdena ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) och [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Kom ihåg att [lösa dina](message-transfers-locks-settlement.md#settling-receive-operations) `PeekLock` meddelanden om du `complete()` `abandon()`använder `defer()`läget `deadletter()` med någon av , , eller metoder i meddelandet.

## <a name="subscription-filters-and-actions"></a>Prenumerationsfilter och åtgärder
Service Bus stöder [filter och åtgärder på prenumerationer](topic-filters.md), vilket gör att du kan filtrera inkommande meddelanden till en prenumeration och redigera deras egenskaper.

När du har en `SubscriptionClient` instans av en kan du använda nedanstående metoder på den för att hämta, lägga till och ta bort regler för prenumerationen för att styra filter och åtgärder.

- getRules (på andra sätt)
- addRule
- ta bortRegel

Varje prenumeration har en standardregel som använder det sanna filtret för att tillåta alla inkommande meddelanden. När du lägger till en ny regel bör du komma ihåg att ta bort standardfiltret för att filtret i den nya regeln ska fungera. Om en prenumeration inte har några regler får den inga meddelanden.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer tillåter användare att ansluta till ett servicebussnamnområde och administrera meddelandeenheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjligheten att testa ämne, köer, prenumerationer, relätjänster, meddelandehubbar och händelsehubbar. 

## <a name="next-steps"></a>Efterföljande moment
Mer information finns i följande resurser.

- [Köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
- Kassan andra [Nodejs-exempel för Service Bus på GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)


