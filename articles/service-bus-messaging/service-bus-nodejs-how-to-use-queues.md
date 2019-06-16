---
title: Hur du använder Azure Service Bus-köer i Node.js | Microsoft Docs
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
ms.openlocfilehash: 1426b3d31159280ad9aac2dd240a5f083c40752d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65988296"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azure-sb-package"></a>Hur du använder Service Bus-köer med Node.js och azure-sb-paketet
> [!div class="op_multi_selector" title1="Programmeringsspråk" title2="Node.js för operativsystem"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

I den här självstudien får du lära dig hur du skapar en Node.js-program att skicka meddelanden till och ta emot meddelanden från en Service Bus-kö med den [azure sb](https://www.npmjs.com/package/azure-sb) paketet. Exemplen är skrivna i JavaScript och använder Node.js [Azure-modulen](https://www.npmjs.com/package/azure) som internt använder den `azure-sb` paketet.

Den [azure sb](https://www.npmjs.com/package/azure-sb) paketera använder [Service Bus REST API: er för körning](/rest/api/servicebus/service-bus-runtime-rest). Du kan få en snabbare upplevelse med hjälp av den nya [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) paket som använder den snabbare [AMQP 1.0-protokollet](service-bus-amqp-overview.md). Läs mer om det nya paketet i [hur du använder Service Bus-köer med Node.js och @azure/service-bus paketet](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues-new-package), annars Läs vidare om du vill se hur du använder den [azure](https://www.npmjs.com/package/azure) paketet.

## <a name="prerequisites"></a>Nödvändiga komponenter
- En Azure-prenumeration. Du behöver ett Azure-konto för att slutföra den här självstudien. Du kan aktivera din [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Om du inte har en kö för att arbeta med, Följ stegen i den [Använd Azure portal för att skapa en Service Bus-kö](service-bus-quickstart-portal.md) artikeln om du vill skapa en kö.
    1. Läsa snabbstartsidan **översikt** i Service Bus **köer**. 
    2. Skapa ett Service Bus **namnområde**. 
    3. Hämta den **anslutningssträngen**. 

        > [!NOTE]
        > Skapar du en **kö** i Service Bus-namnområde med hjälp av Node.js i den här självstudien. 
 

## <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program
Skapa en tom Node.js-program. Anvisningar om hur du skapar ett Node.js-program finns i [skapa och distribuera en Node.js-program till en Azure-webbplats][Create and deploy a Node.js application to an Azure Website], eller [Node.js molntjänst] [ Node.js Cloud Service] med hjälp av Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera programmet att använda Service Bus
Om du vill använda Azure Service Bus, hämta och använda Node.js Azure-paketet. Det här paketet innehåller en uppsättning klientbibliotek som kommunicerar med Service Bus REST-tjänster.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Använda Node Package Manager (NPM) för att hämta paketet
1. Använd den **Windows PowerShell för Node.js** kommandofönstret att navigera till den **c:\\nod\\sbqueues\\WebRole1** mappen där du skapade ditt exempel programmet.
2. Typ **npm-installationsprogrammet azure** i kommandofönstret, vilket resulterar i utdata som liknar följande exempel:

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
3. Du kan kontrollera att mappen **node_modules** har skapats genom att köra kommandot **ls** manuellt. I den mappen, hitta den **azure** paket som innehåller de bibliotek som du behöver komma åt Service Bus-köer.

### <a name="import-the-module"></a>Importera modulen
Med hjälp av anteckningar eller något annat textredigeringsprogram, Lägg till följande överst i **server.js** i appen:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Skapa en Azure Service Bus-anslutning
Azure-modulen läser miljövariabeln `AZURE_SERVICEBUS_CONNECTION_STRING` att hämta information som krävs för att ansluta till Service Bus. Om den här miljövariabeln inte anges måste du ange informationen när du anropar `createServiceBusService`.

Ett exempel på att ställa in miljövariablerna den [Azure-portalen] [ Azure portal] en Azure-webbplats finns [Node.js-webbprogram med Storage] [ Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Skapa en kö
Den **ServiceBusService** objekt kan du arbeta med Service Bus-köer. Följande kod skapar en **ServiceBusService** objekt. Lägga till den i den övre delen av den **server.js** filen efter instruktionen för att importera Azure-modulen:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Genom att anropa `createQueueIfNotExists` på den **ServiceBusService** objekt, den angivna kön returneras (om det finns) eller en ny kö med det angivna namnet har skapats. I följande kod används `createQueueIfNotExists` att skapa eller ansluta till kön med namnet `myqueue`:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

Den `createServiceBusService` metoden har stöd för ytterligare alternativ som gör att du kan åsidosätta standardinställningarna för kö, till exempel tid till live eller maximal köstorlek också. I följande exempel anger den största köstorleken till 5 GB och en tid till live (TTL)-värde på 1 minut:

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
Valfritt filtrering åtgärder kan användas för åtgärder som utförs med hjälp av **ServiceBusService**. Filtrering av åtgärder kan innehålla loggning, försöker automatiskt igen och så vidare. Filter är objekt som implementerar en metod med signaturen:

```javascript
function handle (requestOptions, next)
```

När du har gjort dess förbearbetning på begäran-alternativ, metoden måste anropa `next`, skicka en motringning med följande signatur:

```javascript
function (returnObject, finalCallback, next)
```

I den här motringning och efter bearbetning i `returnObject` (svaret från begäran till servern), återanropet som måste antingen anropa `next` om det finns för att fortsätta att bearbeta andra filter eller anropa `finalCallback`, som slutar tjänstanrop .

Två filter som implementerar logik för omprövning ingår i Azure SDK för Node.js, `ExponentialRetryPolicyFilter` och `LinearRetryPolicyFilter`. Följande kod skapar en `ServiceBusService` objekt som använder den `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
Att skicka ett meddelande till en Service Bus-kö, program-anrop i `sendQueueMessage` metoden på den **ServiceBusService** objekt. Meddelanden skickas till (och tas emot från) Service Bus köer är **BrokeredMessage** objekt och har en uppsättning standardegenskaper (t.ex **etikett** och **TimeToLive**), ett ordlista som används för att lagra anpassade programspecifika egenskaper och en brödtext med godtyckliga programdata. Ett program kan konfigurera meddelandets brödtext genom att skicka en sträng som meddelandet. Alla nödvändiga standardegenskaper fylls med standardvärden.

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

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för hur många meddelanden som ligger i en kö men det finns ett tak för den totala storleken på de meddelanden som ligger i en kö. Den här köstorleken definieras när kön skapas, med en övre gräns på 5 GB. Mer information om kvoter finns i [Service Bus-kvoter][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
Meddelanden tas emot från en kö med hjälp av den `receiveQueueMessage` metoden på den **ServiceBusService** objekt. Som standard tas meddelanden bort från kön som de är skrivskyddade; men du kan läsa (peek) och låsa meddelandet utan att ta bort det från kön genom att ange den valfria parametern `isPeekLock` till **SANT**.

Standardbeteendet för att läsa och radera meddelandet som en del av åtgärden ta emot är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande när ett fel uppstår. Tänk dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan bearbetningen för att förstå det här beteendet. Eftersom Service Bus kommer att ha markerat meddelandet som Förbrukat, att sedan när programmet startas om och börjar förbruka meddelanden igen, ha missat meddelandet som förbrukades innan kraschen.

Om den `isPeekLock` parametern är inställd på **SANT**, ta emot blir en åtgärd i två steg, vilket gör det möjligt att stödprogram som inte tolererar att saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört behandlingen av meddelandet (eller lagrar den på ett tillförlitligt sätt för framtida bearbetning), den är klar det andra steget i processen genom att anropa `deleteMessage` metod och ge meddelanden som ska tas bort som en parameter. Den `deleteMessage` metoden markerar meddelandet som Förbrukat och tas bort från kön.

I följande exempel visar hur du tar emot och bearbetar meddelanden med `receiveQueueMessage`. I exempel först tar emot och tar bort ett meddelande och sedan ta emot ett meddelande med hjälp av `isPeekLock` inställd **SANT**, tar bort meddelandet med hjälp av `deleteMessage`:

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
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram är det går inte att bearbeta meddelandet av någon anledning så kan det anropa den `unlockMessage` metoden på den **ServiceBusService** objekt. Det leder till Service Bus låser upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen genom samma användningsprogram eller ett annat användningsprogram.

Det finns också en tidsgräns som är associerade med ett meddelande som ligger låst i kön, och om programmet misslyckas med att bearbeta meddelandet innan timeout för lås upphör att gälla (till exempel om programmet kraschar), och sedan Service Bus så låser upp meddelandet automatiskt och göra det tillgängligt att tas emot igen.

I händelse av att programmet kraschar efter behandlingen av meddelandet men innan den `deleteMessage` metoden anropas sedan meddelandet att levereras till programmet när den startas om. Den här metoden kallas ofta *minst Processing*, det vill säga varje meddelande bearbetas minst en gång men i vissa situationer kan samma meddelande kan levereras. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Uppnås ofta med hjälp av den **MessageId** egenskapen för meddelandet som förblir konstant under alla leveransförsök.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer tillåter användare att ansluta till ett Service Bus-namnområde och administrera meddelandeentiteter på ett enkelt sätt. Verktyget tillhandahåller avancerade funktioner som import/export-funktionalitet eller möjligheten att testa ämne, köer, prenumerationer, relätjänster, meddelandehubbar och händelser hubs. 

## <a name="next-steps"></a>Nästa steg
Mer information om köer finns i följande resurser.

* [Köer, ämnen och prenumerationer][Queues, topics, and subscriptions]
* [Azure SDK för Node] [ Azure SDK for Node] arkivet på GitHub
* [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
