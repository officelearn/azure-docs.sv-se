---
title: Använd Azure/Service-Bus-ämnen och-prenumerationer med Node.js
description: 'Snabb start: Lär dig hur du använder Service Bus ämnen och prenumerationer i Azure från en Node.js app.'
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-javascript
ms.openlocfilehash: 531322c49a772eaf416fadf1bb4f9a5fb6bf1ff6
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87430606"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Snabb start: använda Service Bus ämnen och prenumerationer med Node.js och Azure/Service-Bus-paketet
I den här självstudien får du lära dig hur du skriver ett Node.js program för att skicka meddelanden till ett Service Bus ämne och ta emot meddelanden från en Service Bus prenumeration med det nya [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) paketet. Det här paketet använder det snabbare [AMQP 1,0-protokollet](service-bus-amqp-overview.md) medan det äldre [Azure-SB-](https://www.npmjs.com/package/azure-sb) paketet används [Service Bus REST-API: er för körnings tid](/rest/api/servicebus/service-bus-runtime-rest). Exemplen är skrivna i Java Script.

## <a name="prerequisites"></a>Förutsättningar
- En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [förmåner för MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Om du inte har ett ämne och en prenumeration som fungerar med följer du stegen i artikeln [använd Azure Portal för att skapa ett Service Bus ämnen och prenumerationer](service-bus-quickstart-topics-subscriptions-portal.md) för att skapa dem. Anteckna anslutnings strängen för din Service Bus-instans och namnen på avsnittet och den prenumeration som du har skapat. Vi kommer att använda dessa värden i exemplen.

> [!NOTE]
> - Den här självstudien fungerar med exempel som du kan kopiera och köra med [NodeJS](https://nodejs.org/). Instruktioner för hur du skapar ett Node.js program finns i [skapa och distribuera ett Node.js program till en Azure-webbplats](../app-service/app-service-web-get-started-nodejs.md)eller [Node.js moln tjänst med Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Det nya [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) paketet stöder inte skapande av topcis och prenumerationer än. Använd [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) paketet om du vill skapa dem program mässigt.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Installera paketet med NPM (Node Package Manager)
Om du vill installera NPM-paketet för Service Bus öppnar du en kommando tolk med `npm` sökvägen och ändrar katalogen till den mapp där du vill ha dina exempel och kör sedan det här kommandot.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne
Att interagera med ett Service Bus ämne börjar med att instansiera klassen [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) och att använda den för att instansiera [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) -klassen. När du har ämnes klienten kan du skapa en avsändare och använda antingen [Skicka](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) eller [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) -metoden på den för att skicka meddelanden.

1. Öppna din favorit redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en fil med namnet `send.js` och klistra in nedanstående kod i den. Den här koden skickar 10 meddelanden till ditt ämne.

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
3. Ange anslutnings strängen och namnet för ditt ämne i ovanstående kod.
4. Kör sedan kommandot `node send.js` i en kommando tolk för att köra den här filen. 

Grattis! Du skickade bara meddelanden till en Service Bus-kö.

Meddelanden har några standard egenskaper som `label` och `messageId` som du kan ställa in när du skickar. Om du vill ange egna egenskaper använder du `userProperties` , vilket är ett JSON-objekt som kan innehålla nyckel/värde-par av dina anpassade data.

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Det finns ingen gräns för antalet meddelanden som lagras i ett ämne, men det finns en gräns för den totala storleken på de meddelanden som innehas av ett ämne. Den här ämnesstorleken definieras när ämnet skapas, med en övre gräns på 5 GB. Mer information om kvoter finns i [Service Bus kvoter](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
Att interagera med en Service Bus prenumeration börjar med att instansiera klassen [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) och använda den för att instansiera [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) -klassen. När du har prenumerations klienten kan du skapa en mottagare och använda antingen [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) eller [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) -metoden på den för att ta emot meddelanden.

1. Öppna din favorit redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/)
2. Skapa en fil med namnet `recieve.js` och klistra in nedanstående kod i den. Den här koden kommer att försöka ta emot 10 meddelanden från din prenumeration. Det faktiska antalet du får beror på antalet meddelanden i prenumerationen och nätverks fördröjningen.

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
3. Ange anslutnings strängen och namnen för ditt ämne och din prenumeration i ovanstående kod.
4. Kör sedan kommandot `node receiveMessages.js` i en kommando tolk för att köra den här filen.

Grattis! Du har bara tagit emot meddelanden från en Service Bus prenumeration.

Metoden [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) tar i ett `ReceiveMode` som är en Enum med värdena [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) och [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Kom ihåg att [lösa dina meddelanden](message-transfers-locks-settlement.md#settling-receive-operations) om du använder `PeekLock` läget genom att använda någon av `complete()` metoderna,, `abandon()` `defer()` , eller för att ange ett `deadletter()` meddelande.

## <a name="subscription-filters-and-actions"></a>Prenumerations filter och åtgärder
Service Bus stöder [filter och åtgärder för prenumerationer](topic-filters.md), vilket gör att du kan filtrera inkommande meddelanden till en prenumeration och redigera deras egenskaper.

När du har en instans av en `SubscriptionClient` kan du använda metoderna nedan för att hämta, lägga till och ta bort regler för prenumerationen för att kontrol lera filter och åtgärder.

- getRules
- addRule
- removeRule

Varje prenumeration har en standard regel som använder det sanna filtret för att tillåta alla inkommande meddelanden. Kom ihåg att ta bort standard filtret när du lägger till en ny regel för att filtret i den nya regeln ska fungera. Om en prenumeration inte har några regler visas inga meddelanden.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer gör det möjligt för användare att ansluta till en Service Bus namnrymd och administrera meddelande enheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjlighet att testa ämnen, köer, prenumerationer, relä tjänster, Notification Hub och Event Hub. 

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser.

- [Köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
- Andra [NodeJS-exempel för Service Bus på GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)


