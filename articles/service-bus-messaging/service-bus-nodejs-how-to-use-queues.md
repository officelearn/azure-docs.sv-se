---
title: "Hur du använder Service Bus-köer i Node.js | Microsoft Docs"
description: "Lär dig hur du använder Service Bus-köer i Azure från en Node.js-app."
services: service-bus-messaging
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 5b309534f7aef602610cfdb6aa784d180551e1ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-bus-queues-with-nodejs"></a>Hur du använder Service Bus-köer med Node.js

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Den här artikeln beskriver hur du använder Service Bus-köer med Node.js. Exemplen är skrivna i JavaScript och använder Node.js Azure-modulen. Scenarier som tas upp inkluderar **skapa köer**, **skicka och ta emot meddelanden**, och **tar bort köer**. Mer information om köer finns i [nästa steg](#next-steps) avsnitt.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program
Skapa ett tomt Node.js-program. Instruktioner om hur du skapar en Node.js-program finns i [skapa och distribuera en Node.js-program till en Azure-webbplats][Create and deploy a Node.js application to an Azure Website], eller [Node.js Molntjänsten] [ Node.js Cloud Service] med Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera ditt program att använda Service Bus
Om du vill använda Azure Service Bus, hämta och använda Node.js Azure-paketet. Det här paketet innehåller en uppsättning bibliotek som kommunicerar med Service Bus REST-tjänster.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Använd noden Package Manager (NPM) för att hämta paketet
1. Använd den **Windows PowerShell för Node.js** kommandofönstret att navigera till den **c:\\nod\\sbqueues\\WebRole1** mappen där du skapade ditt exempel programmet.
2. Typen **npm installera azure** i Kommandotolken, vilket bör resultera i utdata som liknar följande:

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```
3. Du kan köra manuellt på **ls** kommando för att kontrollera att en **node_modules** mappen har skapats. I mappen hitta den **azure** paket som innehåller de bibliotek som du behöver ha tillgång till Service Bus-köer.

### <a name="import-the-module"></a>Importera modulen
I anteckningar eller något annat textredigeringsprogram, Lägg till följande överst i den **server.js** filen för programmet:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Azure Service Bus-anslutningar
Azure-modulen läser miljövariabeln `AZURE_SERVICEBUS_CONNECTION_STRING` att få information som krävs för att ansluta till Service Bus. Om den här miljövariabeln inte har angetts måste du ange kontoinformationen vid anrop av `createServiceBusService`.

Ett exempel på hur miljövariablerna i en konfigurationsfil för ett Azure Cloud Service, se [Node.js molntjänst med lagring][Node.js Cloud Service with Storage].

Ett exempel på Ange miljövariabler i den [Azure-portalen] [ Azure portal] en Azure-webbplats finns [Node.js-Webbapp med lagring] [ Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Skapa en kö
Den **ServiceBusService** objekt kan du arbeta med Service Bus-köer. Följande kod skapar en **ServiceBusService** objekt. Lägg till den övre delen av den **server.js** filen när instruktionen för att importera modulen för Azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Genom att anropa `createQueueIfNotExists` på den **ServiceBusService** objekt returneras kö som anges (om den finns) eller en ny kö med det angivna namnet har skapats. I följande kod används `createQueueIfNotExists` ska skapa eller Anslut till kön med namnet `myqueue`:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

Den `createServiceBusService` metoden stöder också ytterligare alternativ som gör att du kan åsidosätta standardinställningar för kön, till exempel tid till live eller maximal köstorlek. I följande exempel anger den maximala storleken till 5 GB och en gång till live TTL-värde på 1 minut:

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Filter
Valfria filtrering åtgärder kan användas för åtgärder som utförs med hjälp av **ServiceBusService**. Filtrering operations kan innehålla loggning, försöker automatiskt igen och så vidare. Filtren är objekt som implementerar en metod med signaturen:

```javascript
function handle (requestOptions, next)
```

När du har gjort dess föregående bearbetning på begäran-alternativ måste anropa metoden `next`, skicka ett återanrop med följande signatur:

```javascript
function (returnObject, finalCallback, next)
```

I den här motringning och efter bearbetning av `returnObject` (svaret från begäran till servern), återanropet måste antingen anropa `next` om den finns för att fortsätta att bearbeta filter eller helt enkelt anropa `finalCallback`, som slutar tjänsten anrop.

Två filter som implementerar logik som medföljer Azure SDK för Node.js, `ExponentialRetryPolicyFilter` och `LinearRetryPolicyFilter`. Följande kod skapar en `ServiceBusService` objekt som använder den `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
Att skicka ett meddelande till en Service Bus-kö, program-anrop i `sendQueueMessage` -metoden i den **ServiceBusService** objekt. Meddelanden skickas till (och mottagna från) Service Bus är köer **BrokeredMessage** objekt och har en uppsättning standardegenskaper (t.ex **etikett** och **TimeToLive**), ordlista som används för att lagra anpassade programspecifika egenskaper och en brödtext med godtyckliga programdata. Ett program kan konfigurera meddelandets brödtext för meddelandet genom att skicka en sträng som meddelandet. Alla nödvändiga standardegenskaper fylls i med standardvärden.

I följande exempel visar hur du skickar ett testmeddelande till kön med namnet `myqueue` med `sendQueueMessage`:

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i en kö men det finns ett tak för den totala storleken för de meddelanden som ligger i en kö. Den här köstorleken definieras när kön skapas, med en övre gräns på 5 GB. Mer information om kvoter finns [Service Bus-kvoter][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
Meddelanden tas emot från en kö med hjälp av den `receiveQueueMessage` -metoden i den **ServiceBusService** objekt. Som standard tas meddelanden bort från kön när de läses; men du kan läsa (titt) och låsa meddelandet utan att ta bort det från kön genom att ange den valfria parametern `isPeekLock` till **SANT**.

Standardbeteendet för läsning och ta bort meddelandet som en del av receive-åtgärden är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus kommer att ha markerat meddelandet som Förbrukat, och sedan när programmet startas om och börjar förbruka meddelanden igen, att ha missat meddelandet som förbrukades innan kraschen.

Om den `isPeekLock` parametern anges till **SANT**, inleveransen en åtgärd i två steg, vilket gör det möjligt att stödprogram som inte tolererar att ett meddelande saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har avslutat bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida bearbetning), Slutför det andra steget i processen genom att anropa `deleteMessage` metod och ge meddelandet som ska tas bort som en parameter. Den `deleteMessage` metoden markerar meddelandet som Förbrukat och tas bort från kön.

Exemplet nedan visar hur du tar emot och bearbetar meddelanden med `receiveQueueMessage`. I exempel först tar emot och tar bort ett meddelande och tar emot ett meddelande med hjälp av `isPeekLock` inställd på **SANT**, tar bort meddelande med `deleteMessage`:

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram går inte att bearbeta meddelandet av någon anledning, så kan det anropa den `unlockMessage` -metoden i den **ServiceBusService** objekt. Detta gör att Service Bus låser upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen genom samma användningsprogram eller ett annat användningsprogram.

Det finns också en tidsgräns som är associerad med ett meddelande som ligger låst i kön och om programmet misslyckas med att bearbeta meddelandet innan timeout för lås upphör att gälla (t.ex. Om programmet kraschar), kommer Service Bus att låsa upp meddelandet automatiskt och gör det tillgängligt att tas emot igen.

I händelse av att programmet kraschar efter att meddelandet har bearbetats men innan den `deleteMessage` metoden anropas sedan meddelandet att levereras till programmet när den startas om. Det här kallas ofta *At Least Once Processing*, det vill säga, varje meddelande bearbetas minst en gång men i vissa situationer kan samma meddelande levereras. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Detta uppnås ofta med hjälp av den **MessageId** för meddelandet, som förblir konstant under alla leveransförsök.

## <a name="next-steps"></a>Nästa steg
Mer information om köer finns i följande resurser.

* [Köer, ämnen och prenumerationer][Queues, topics, and subscriptions]
* [Azure SDK för noden] [ Azure SDK for Node] databasen på GitHub
* [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]:../cosmos-db/table-storage-cloud-service-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
