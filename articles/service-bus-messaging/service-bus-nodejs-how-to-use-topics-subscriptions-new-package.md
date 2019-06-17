---
title: Hur du använder Azure Service Bus-ämnen och prenumerationer med Node.js | Microsoft Docs
description: Lär dig hur du använder Service Bus-ämnen och prenumerationer i Azure från en Node.js-app.
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
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: 7686014adb989494e6df277de4137b76c3125696
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65992140"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Hur du använder Service Bus-ämnen och prenumerationer med Node.js och azure/service bus-paketet
> [!div class="op_multi_selector" title1="Programmeringsspråk" title2="Node.js för operativsystem"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

I den här självstudien får du lära dig hur du skriver ett Node.js-program för att skicka meddelanden till en Service Bus-ämne och ta emot meddelanden från en Service Bus-prenumeration med hjälp av den nya [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) paketet. Det här paketet används den snabbare [AMQP 1.0-protokollet](service-bus-amqp-overview.md) medan den äldre [azure sb](https://www.npmjs.com/package/azure-sb) paketet som används [Service Bus REST API: er för körning](/rest/api/servicebus/service-bus-runtime-rest). Exemplen är skrivna i JavaScript.

## <a name="prerequisites"></a>Nödvändiga komponenter
- En Azure-prenumeration. Du behöver ett Azure-konto för att slutföra den här självstudien. Du kan aktivera din [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Om du inte har ett ämne och en prenumeration för att arbeta med, Följ stegen i den [Använd Azure-portalen för att skapa ett Service Bus-ämnen och prenumerationer](service-bus-quickstart-topics-subscriptions-portal.md) artikeln för att skapa dem. Anteckna anslutningssträngen för din Service Bus-instans och namnen på ämnet och prenumerationen du skapade. Vi använder dessa värden i exemplen.

> [!NOTE]
> - Den här självstudien fungerar med exempel som du kan kopiera och köra med [Nodejs](https://nodejs.org/). Anvisningar om hur du skapar ett Node.js-program finns i [skapa och distribuera en Node.js-program till en Azure-webbplats](../app-service/app-service-web-get-started-nodejs.md), eller [Node.js-molntjänst med hjälp av Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Den nya [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) paketet stöder inte skapandet av topcis och prenumerationer ännu. Använd den [ @azure/arm-servicebus ](https://www.npmjs.com/package/@azure/arm-servicebus) Paketera om du vill skapa dem programmässigt.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Installera paketet med NPM (Node Package Manager)
Om du vill installera npm-paketet för Service Bus, öppna en kommandotolk som har `npm` i sökvägen, ändra katalogen till mappen där du vill ha exempel och kör sedan det här kommandot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne
Interagera med en Service Bus avsnittet börjar med instansiera den [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) klass och använder det för att skapa en instans av den [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) klass. När du har klienten avsnittet kan du skapa en avsändare och använda antingen [skicka](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) eller [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) metod på den för att skicka meddelanden.

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en fil med namnet `send.js` och klistra in den nedan kod till den. Den här koden skickar 10 meddelandena till ditt ämne.

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
4. Kör sedan kommandot `node send.js` i Kommandotolken och kör den här filen. 

Grattis! Du har skickat meddelanden till en Service Bus-kö.

Meddelanden har några standardegenskaper som `label` och `messageId` som du kan ange när du skickar. Om du vill ange anpassade egenskaper kan använda den `userProperties`, vilket är ett json-objekt som kan innehålla nyckel / värde-par med dina egna data.

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Det finns ingen gräns för hur många meddelanden som ligger i ett avsnitt, men det finns en gräns för den totala storleken på de meddelanden som ligger i ett ämne. Den här ämnesstorleken definieras när ämnet skapas, med en övre gräns på 5 GB. Mer information om kvoter finns i [Service Bus-kvoter](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
Interagera med en Service Bus-prenumeration som börjar med instansiera den [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) klass och använder det för att skapa en instans av den [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) klass. När du har klienten prenumeration kan du skapa en mottagare och använda antingen [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) eller [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) metod på den för att ta emot meddelanden.

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en fil med namnet `recieve.js` och klistra in den nedan kod till den. Den här koden kommer att försöka ta emot 10 meddelanden från prenumerationen. Det faktiska antalet som du får beror på hur många meddelanden i prenumerationen och nätverksfördröjning.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.ReceiveAndDelete);
      
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
3. Ange anslutningssträngen och namnen på ditt ämne och en prenumeration i koden ovan.
4. Kör sedan kommandot `node receiveMessages.js` i Kommandotolken och kör den här filen.

Grattis! Du har precis fått meddelanden från en Service Bus-prenumeration.

Den [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) metoden tar en `ReceiveMode` som är en uppräkning med värden [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) och [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Kom ihåg att [reglera dina meddelanden](message-transfers-locks-settlement.md#settling-receive-operations) om du använder den `PeekLock` läge med någon av `complete()`, `abandon()`, `defer()`, eller `deadletter()` metoder på meddelandet.

## <a name="subscription-filters-and-actions"></a>Prenumerationsfilter och åtgärder
Service Bus stöder [filter och åtgärder på prenumerationer](topic-filters.md), vilket gör att du kan filtrera inkommande meddelanden till en prenumeration och redigera deras egenskaper.

När du har en instans av en `SubscriptionClient` du kan använda den nedan metoder på den för att hämta, lägga till och ta bort regler för prenumerationen kan styra filter och åtgärder.

- getRules
- addRule
- removeRule

Varje prenumeration har en standardregel som använder SANT filter för att tillåta alla inkommande meddelanden. Kom ihåg att ta bort standardfiltret för filtret i din nya regel ska fungera när du lägger till en ny regel. Om en prenumeration har inga regler, visas inga meddelanden.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer tillåter användare att ansluta till ett Service Bus-namnområde och administrera meddelandeentiteter på ett enkelt sätt. Verktyget tillhandahåller avancerade funktioner som import/export-funktionalitet eller möjligheten att testa ämne, köer, prenumerationer, relätjänster, meddelandehubbar och händelser hubs. 

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser.

- [Köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
- Se andra [Nodejs-exempel för Service Bus på GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)


