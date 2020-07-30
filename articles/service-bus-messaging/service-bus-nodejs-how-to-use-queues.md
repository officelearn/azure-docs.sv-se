---
title: Använda Azure Service Bus köer i Node.js med Azure-SB-paket
description: Lär dig hur du skapar Node.js program för att skicka meddelanden till och ta emot meddelanden från en Azure Service Bus kö med Azure-SB-paketet.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-javascript
ms.openlocfilehash: 076233c52074bca646c072e9a1abe63b15412c1b
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87430641"
---
# <a name="quickstart-use-service-bus-queues-in-azure-with-nodejs-and-the-azure-sb-package"></a>Snabb start: Använd Service Bus köer i Azure med Node.js och Azure-SB-paketet
I den här självstudien får du lära dig hur du skapar Node.js program för att skicka meddelanden till och ta emot meddelanden från en Azure Service Bus kö med [Azure-SB-](https://www.npmjs.com/package/azure-sb) paketet. Exemplen är skrivna i Java Script och använder Azure- [modulen](https://www.npmjs.com/package/azure) Node.js som internt använder Azure-SB-paketet.

> [!IMPORTANT]
> [Azure-SB-](https://www.npmjs.com/package/azure-sb) paketet använder [Service Bus REST-API: er för körnings tid](/rest/api/servicebus/service-bus-runtime-rest). Du kan få en snabbare upplevelse med hjälp av den nya [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) som använder det snabbare [AMQP 1,0-protokollet](service-bus-amqp-overview.md). 
> 
> Mer information om det nya paketet finns i [så här använder du Service Bus köer med Node.js och @azure/service-bus paket](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues-new-package), annars fortsätter att läsa för att se hur du använder [Azure](https://www.npmjs.com/package/azure) -paketet.

## <a name="prerequisites"></a>Förutsättningar
- En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [förmåner för MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Om du inte har en kö att arbeta med följer du stegen i artikeln [använd Azure Portal för att Service Bus skapa](service-bus-quickstart-portal.md) en kö.
    1. Läs snabb **översikten** över Service Bus **köer**. 
    2. Skapa ett Service Bus- **namnområde**. 
    3. Hämta **anslutnings strängen**. 

        > [!NOTE]
        > Du skapar en **kö** i Service Bus namn området genom att använda Node.js i den här självstudien. 
 

## <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program
Skapa ett tomt Node.js-program. Instruktioner för hur du skapar ett Node.js program finns i [skapa och distribuera ett Node.js program till en Azure-webbplats][Create and deploy a Node.js application to an Azure Website]eller [Node.js moln tjänst][Node.js Cloud Service] med Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera programmet så att det använder Service Bus
Om du vill använda Azure Service Bus hämtar och använder du Node.js Azure-paketet. Det här paketet innehåller en uppsättning bibliotek som kommunicerar med Service Bus REST-tjänsterna.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Hämta paketet med hjälp av Node Pack Manager (NPM)
1. Använd **Windows PowerShell för att Node.js** kommando fönstret för att navigera till mappen **c: \\ Node \\ sbqueues \\ WebRole1** där du skapade exempel programmet.
2. Skriv **NPM installera Azure** i kommando fönstret, vilket bör resultera i utdata som liknar följande exempel:

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
3. Du kan kontrollera att mappen **node_modules** har skapats genom att köra kommandot **ls** manuellt. I den mappen hittar du **Azure** -paketet som innehåller de bibliotek som du behöver för att komma åt Service Bus köer.

### <a name="import-the-module"></a>Importera modulen
Använd anteckningar eller något annat text redigerings program och Lägg till följande överst i **server.js** -filen för programmet:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Konfigurera en Azure Service Bus anslutning
Azure-modulen läser miljö variabeln `AZURE_SERVICEBUS_CONNECTION_STRING` för att få information som krävs för att ansluta till Service Bus. Om den här miljövariabeln inte har angetts måste du ange konto informationen när du anropar `createServiceBusService` .

Ett exempel på att ställa in miljövariabler i [Azure Portal][Azure portal] för en Azure-webbplats finns i [Node.js webb program med lagring][Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Skapa en kö
Med **ServiceBusService** -objektet kan du arbeta med Service Bus köer. Följande kod skapar ett **ServiceBusService** -objekt. Lägg till den högst upp i **server.js** -filen, efter att instruktionen importera Azure-modulen:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Genom att anropa `createQueueIfNotExists` **ServiceBusService** -objektet returneras den angivna kön (om den finns), eller så skapas en ny kö med det angivna namnet. Följande kod används `createQueueIfNotExists` för att skapa eller ansluta till kön med namnet `myqueue` :

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

`createServiceBusService`Metoden stöder också ytterligare alternativ som gör att du kan åsidosätta standardinställningar för kön, till exempel meddelande tid till Live eller maximal kös Tor lek. I följande exempel anges maximal kös Tor lek till 5 GB och ett TTL-värde (Time to Live) på 1 minut:

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
Valfria filtrerings åtgärder kan tillämpas på åtgärder som utförs med hjälp av **ServiceBusService**. Filtrerings åtgärder kan omfatta loggning, automatiskt försök igen osv. Filter är objekt som implementerar en metod med signaturen:

```javascript
function handle (requestOptions, next)
```

Efter bearbetningen av begär ande alternativen måste metoden anropa `next` och skicka ett motanrop med följande signatur:

```javascript
function (returnObject, finalCallback, next)
```

I det här återanropet, och efter bearbetning av `returnObject` (svaret från begäran till servern), måste återanropet antingen anropa `next` om det finns för att fortsätta bearbeta andra filter, eller anropa `finalCallback` , vilket avslutar tjänst anropet.

Två filter som implementerar logik för omprövning ingår i Azure SDK för Node.js `ExponentialRetryPolicyFilter` och `LinearRetryPolicyFilter` . Följande kod skapar ett- `ServiceBusService` objekt som använder `ExponentialRetryPolicyFilter` :

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
Om du vill skicka ett meddelande till en Service Bus kö anropar programmet `sendQueueMessage` metoden på **ServiceBusService** -objektet. Meddelanden som skickas till (och tas emot från) Service Bus köer är **BrokeredMessage** -objekt och har en uppsättning standard egenskaper (t. ex. **etikett** och **TimeToLive**), en ord lista som används för att lagra anpassade programspecifika egenskaper och en brödtext av godtycklig program data. Ett program kan ange meddelandets brödtext genom att skicka en sträng som meddelande. Alla obligatoriska standard egenskaper fylls i med standardvärden.

Följande exempel visar hur du skickar ett test meddelande till kön med namnet `myqueue` med `sendQueueMessage` :

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

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som finns i en kö, men det finns ett tak för den totala storleken på de meddelanden som innehas av en kö. Den här köstorleken definieras när kön skapas, med en övre gräns på 5 GB. Mer information om kvoter finns i [Service Bus kvoter][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
Meddelanden tas emot från en kö med hjälp av `receiveQueueMessage` metoden på **ServiceBusService** -objektet. Som standard tas meddelanden bort från kön när de läses. Du kan dock läsa (granska) och låsa meddelandet utan att ta bort det från kön genom att ange den valfria parametern `isPeekLock` till **True**.

Standard beteendet för att läsa och ta bort meddelandet som en del av Receive-åtgärden är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera att inte bearbeta ett meddelande när ett fel uppstår. För att förstå det här beteendet bör du överväga ett scenario där klienten utfärdar Receive-begäran och sedan kraschar innan den bearbetas. Eftersom Service Bus har markerat meddelandet som förbrukat, och när programmet startas om och börjar förbruka meddelanden igen, kommer det att ha missat meddelandet som förbrukades före kraschen.

Om `isPeekLock` parametern är inställd på **Sant**blir mottagningen en åtgärd i två steg, vilket gör det möjligt att stödja program som inte kan tolerera meddelanden som saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört bearbetningen av meddelandet (eller lagrar det tillförlitligt för framtida bearbetning) slutförs det andra steget i Receive-processen genom att anropa `deleteMessage` metoden och tillhandahålla meddelandet som ska tas bort som en parameter. `deleteMessage`Metoden markerar meddelandet som förbrukat och tar bort det från kön.

Följande exempel visar hur du tar emot och bearbetar meddelanden med hjälp av `receiveQueueMessage` . Exemplet tar först emot och tar bort ett meddelande och tar emot ett meddelande med hjälp av `isPeekLock` Ange som **Sant**och tar sedan bort meddelandet med hjälp av `deleteMessage` :

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
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagar program inte kan bearbeta meddelandet av någon anledning, kan det anropa `unlockMessage` metoden i **ServiceBusService** -objektet. Det kommer att orsaka att Service Bus låser upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen genom samma användnings program eller ett annat användnings program.

Det finns också en tids gräns som är kopplad till ett meddelande som är låst i kön och om programmet inte kan bearbeta meddelandet innan tids gränsen för låsning går ut (till exempel om programmet kraschar), kommer Service Bus att låsa upp meddelandet automatiskt och göra det tillgängligt för att tas emot igen.

I händelse av att programmet kraschar när meddelandet har bearbetats men innan `deleteMessage` metoden anropas, kommer meddelandet att skickas vidare till programmet när det startas om. Den här metoden anropas ofta *minst en gång*, det vill säga att varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras igen. Om scenariot inte kan tolerera dubbel bearbetning bör programutvecklare lägga till ytterligare logik till sitt program för att hantera dubbla meddelande leveranser. Den uppnås ofta med hjälp av meddelandets **messageid** -egenskap, som förblir konstant mellan leverans försök.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer gör det möjligt för användare att ansluta till en Service Bus namnrymd och administrera meddelande enheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjlighet att testa ämnen, köer, prenumerationer, relä tjänster, Notification Hub och Event Hub. 

## <a name="next-steps"></a>Nästa steg
Mer information om köer finns i följande resurser.

* [Köer, ämnen och prenumerationer][Queues, topics, and subscriptions]
* [Azure SDK för Node][Azure SDK for Node] -lagringsplats på GitHub
* [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
