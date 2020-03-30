---
title: Använda Azure Service Bus-köer i Node.js med azure-sb-paketet
description: Lär dig hur du skapar Node.js-program för att skicka meddelanden till och ta emot meddelanden från en Azure Service Bus-kö med azure-sb-paketet.
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
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 7ee3939c1a1b450f2458267ab0b70e3924a4869b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330608"
---
# <a name="quickstart-use-service-bus-queues-in-azure-with-nodejs-and-the-azure-sb-package"></a>Snabbstart: Använd servicebussköer i Azure med Node.js och azure-sb-paketet
I den här självstudien får du lära dig hur du skapar Node.js-program för att skicka meddelanden till och ta emot meddelanden från en Azure Service [Bus-kö med azure-sb-paketet.](https://www.npmjs.com/package/azure-sb) Exemplen skrivs i JavaScript och använder node.js [Azure-modulen](https://www.npmjs.com/package/azure) som internt använder azure-sb-paketet.

> [!IMPORTANT]
> [Azure-sb-paketet](https://www.npmjs.com/package/azure-sb) använder [API:er för servicebuss REST-körning.](/rest/api/servicebus/service-bus-runtime-rest) Du kan få en snabbare [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) upplevelse med hjälp av det nya som använder det snabbare [AMQP 1.0-protokollet](service-bus-amqp-overview.md). 
> 
> Mer information om det nya paketet finns i [Så här använder du @azure/service-bus Service Bus-köer med Node.js och paket](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues-new-package), annars fortsätt läsa för att se hur du använder [azure-paketet.](https://www.npmjs.com/package/azure)

## <a name="prerequisites"></a>Krav
- En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [msdn-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Om du inte har en kö att arbeta med följer du stegen i [Azure-portalen för att skapa en](service-bus-quickstart-portal.md) köartikel för Service Bus för att skapa en kö.
    1. Läs snabb **översikten över** servicebussköer . **queues** 
    2. Skapa ett **servicebussnamnområde**. 
    3. Hämta **anslutningssträngen**. 

        > [!NOTE]
        > Du skapar en **kö** i namnområdet Service Bus med nod.js i den här självstudien. 
 

## <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program
Skapa ett tomt nod.js-program. Instruktioner om hur du skapar ett Node.js-program finns i [Skapa och distribuera ett Node.js-program till en Azure-webbplats][Create and deploy a Node.js application to an Azure Website]eller [Node.js Cloud Service][Node.js Cloud Service] med Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera ditt program så att det använder Service Bus
Om du vill använda Azure Service Bus hämtar och använder du Node.js Azure-paketet. Det här paketet innehåller en uppsättning bibliotek som kommunicerar med Service Bus REST-tjänsterna.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Använd Nod Package Manager (NPM) för att hämta paketet
1. Använd kommandofönstret **Windows PowerShell för nod.js** för att navigera till mappen **\\c:\\nod sbqueues\\WebRole1** där du skapade exempelprogrammet.
2. Skriv **npm installera azure** i kommandofönstret, vilket bör resultera i utdata som liknar följande exempel:

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
3. Du kan kontrollera att mappen **node_modules** har skapats genom att köra kommandot **ls** manuellt. I den mappen hittar du **azure-paketet,** som innehåller de bibliotek du behöver för att komma åt Service Bus-köer.

### <a name="import-the-module"></a>Importera modulen
Med Anteckningar eller en annan textredigerare lägger du till följande högst upp i filen **server.js** i programmet:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Konfigurera en Azure Service Bus-anslutning
Azure-modulen läser `AZURE_SERVICEBUS_CONNECTION_STRING` av miljövariabeln för att få information som krävs för att ansluta till Service Bus. Om den här miljövariabeln inte är inställd `createServiceBusService`måste du ange kontoinformationen när du ringer .

Ett exempel på hur du anger miljövariabler i [Azure-portalen][Azure portal] för en Azure-webbplats finns i [Node.js Web Application with Storage][Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Skapa en kö
**Med ServiceBusService-objektet** kan du arbeta med servicebussköer. Följande kod skapar ett **ServiceBusService-objekt.** Lägg till den högst upp i **filen server.js,** efter satsen för att importera Azure-modulen:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Genom `createQueueIfNotExists` att anropa **ServiceBusService-objektet** returneras den angivna kön (om den finns) eller en ny kö med det angivna namnet skapas. Följande kod `createQueueIfNotExists` används för att skapa `myqueue`eller ansluta till kön med namnet:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

Metoden `createServiceBusService` stöder också ytterligare alternativ, som gör att du kan åsidosätta standardköinställningar som meddelandetid för att leva eller maximal köstorlek. I följande exempel anges den maximala köstorleken till 5 GB och ett tidsbelevt (TTL) värde på 1 minut:

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
Valfria filtreringsåtgärder kan tillämpas på åtgärder som utförs med **ServiceBusService**. Filtreringsåtgärder kan omfatta loggning, automatiskt återförsök, etc. Filter är objekt som implementerar en metod med signaturen:

```javascript
function handle (requestOptions, next)
```

När du har gjort sin förbearbetning på `next`alternativen för begäran måste metoden anropa , skicka en motringning med följande signatur:

```javascript
function (returnObject, finalCallback, next)
```

I den här motringningen `returnObject` och efter bearbetning av (svaret från begäran till `next` servern) måste motringningen antingen `finalCallback`anropa om det finns för att fortsätta bearbeta andra filter eller anropa , som avslutar tjänstens anrop.

Två filter som implementerar logik för återförsök ingår i Azure `ExponentialRetryPolicyFilter` SDK för Node.js och `LinearRetryPolicyFilter`. I följande kod `ServiceBusService` skapas ett `ExponentialRetryPolicyFilter`objekt som använder:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
Om du vill skicka ett meddelande till `sendQueueMessage` en servicebusskö anropar programmet metoden på **ServiceBusService-objektet.** Meddelanden som skickas till (och tas emot från) Service Bus-köer är **BrokeredMessage-objekt** och har en uppsättning standardegenskaper (till exempel **Etikett** och **TimeToLive**), en ordlista som används för att lagra anpassade programspecifika egenskaper och en samling godtyckliga programdata. Ett program kan ställa in meddelandets brödtext genom att skicka en sträng som meddelande. Alla nödvändiga standardegenskaper fylls i med standardvärden.

I följande exempel visas hur du skickar ett `myqueue` `sendQueueMessage`testmeddelande till kön med namnet:

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

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som finns i en kö, men det finns ett tak för den totala storleken på meddelanden som innehas av en kö. Den här köstorleken definieras när kön skapas, med en övre gräns på 5 GB. Mer information om kvoter finns i [Service Bus-kvoter][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
Meddelanden tas emot från en `receiveQueueMessage` kö med metoden på **ServiceBusService-objektet.** Som standard tas meddelanden bort från kön när de läss. Du kan dock läsa (kika) och låsa meddelandet utan att `isPeekLock` ta bort det från kön genom att ange den valfria parametern till **true**.

Standardbeteendet för att läsa och ta bort meddelandet som en del av mottagningsåtgärden är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera att inte bearbeta ett meddelande när ett fel inträffar. För att förstå detta, överväga ett scenario där konsumenten utfärdar mottagningsbegäran och sedan kraschar innan du bearbetar den. Eftersom Service Bus har markerat meddelandet som förbrukat, då när programmet startas om och börjar konsumera meddelanden igen, kommer det att ha missat meddelandet som förbrukades före kraschen.

Om `isPeekLock` parametern är inställd på **true**blir mottagningen en tvåstegsåtgärd, vilket gör det möjligt att stödja program som inte tål meddelanden som saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt `deleteMessage` för framtida bearbetning) slutförs den andra fasen av mottagningsprocessen genom att anropa metoden och tillhandahålla meddelandet som ska tas bort som en parameter. Metoden `deleteMessage` markerar meddelandet som förbrukat och tar bort det från kön.

I följande exempel visas hur du får `receiveQueueMessage`och bearbetar meddelanden med . Exemplet tar först emot och tar bort ett `isPeekLock` meddelande och tar sedan emot `deleteMessage`ett meddelande med värdet **true**och tar sedan bort meddelandet med:

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
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram inte kan bearbeta meddelandet av någon `unlockMessage` anledning kan det anropa metoden på **ServiceBusService-objektet.** Det kommer att orsaka Service Bus att låsa upp meddelandet i kön och göra det tillgängligt för att tas emot igen, antingen av samma tidskrävande program eller av ett annat tidskrävande program.

Det finns också en timeout som är associerad med ett meddelande låst i kön, och om programmet inte bearbetar meddelandet innan låstidsgränsen går ut (till exempel om programmet kraschar), kommer Service Bus att låsa upp meddelandet automatiskt och göra det tillgängliga för att tas emot igen.

I händelse av att programmet kraschar efter bearbetning `deleteMessage` av meddelandet men innan metoden anropas, kommer meddelandet att levereras till programmet när det startas om. Den här metoden kallas ofta *minst en gång bearbetning*, det vill säga varje meddelande kommer att bearbetas minst en gång men i vissa situationer samma meddelande kan levereras. Om scenariot inte kan tolerera dubblettbearbetning bör programutvecklare lägga till ytterligare logik i sitt program för att hantera leverans av dubblettmeddelanden. Det uppnås ofta med hjälp av **MessageId-egenskapen** för meddelandet, som förblir konstant över leveransförsök.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer tillåter användare att ansluta till ett servicebussnamnområde och administrera meddelandeenheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjligheten att testa ämne, köer, prenumerationer, relätjänster, meddelandehubbar och händelsehubbar. 

## <a name="next-steps"></a>Nästa steg
Mer information om köer finns i följande resurser.

* [Köer, ämnen och prenumerationer][Queues, topics, and subscriptions]
* [Azure SDK för noddatabas][Azure SDK for Node] på GitHub
* [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
