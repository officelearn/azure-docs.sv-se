---
title: "Hur du använder Azure Service Bus-ämnen och prenumerationer med Node.js | Microsoft Docs"
description: "Lär dig hur du använder Service Bus-ämnen och prenumerationer i Azure från en Node.js-app."
services: service-bus-messaging
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: d9e463273fff0ecc198b0574443c4241dde7be79
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs"></a>Använd Service Bus-ämnen och prenumerationer med Node.js

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Den här guiden beskriver hur du använder Service Bus-ämnen och prenumerationer från Node.js-program. Scenarier som tas upp inkluderar **skapa ämnen och prenumerationer**, **skapa prenumerationsfilter**, **skickar meddelanden** till ett ämne **ta emot meddelanden från en prenumeration**, och **ta bort ämnen och prenumerationer**. Mer information om ämnen och prenumerationer, finns i avsnittet [Nästa steg](#next-steps).

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program
Skapa ett tomt Node.js-program. Instruktioner om hur du skapar en Node.js-program finns i [skapa och distribuera ett Node.js-program till en Azure-webbplats], [Node.js Molntjänsten] [ Node.js Cloud Service] med Windows PowerShell eller webbplats med WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera ditt program att använda Service Bus
Hämta Node.js Azure för att använda Service Bus. Det här paketet innehåller en uppsättning bibliotek som kommunicerar med Service Bus REST-tjänster.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Använd noden Package Manager (NPM) för att hämta paketet
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix), navigera till mappen där du skapade exempelprogrammet.
2. Typen **npm installera azure** i Kommandotolken, vilket bör resultera i följande utdata:

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
3. Du kan köra manuellt på **ls** kommando för att kontrollera att en **nod\_moduler** mappen har skapats. I mappen hitta den **azure** paket som innehåller de bibliotek som du behöver ha tillgång till Service Bus-ämnen.

### <a name="import-the-module"></a>Importera modulen
I anteckningar eller något annat textredigeringsprogram, Lägg till följande överst i den **server.js** filen för programmet:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Konfigurera en Service Bus-anslutning
Azure-modulen läser miljövariabeln `AZURE_SERVICEBUS_CONNECTION_STRING` för anslutningssträngen som du fick från det tidigare steget ”skaffa autentiseringsuppgifter”. Om den här miljövariabeln inte har angetts måste du ange kontoinformationen vid anrop av `createServiceBusService`.

Ett exempel på hur miljövariablerna för ett Azure Cloud Service, se [Node.js molntjänst med lagring][Node.js Cloud Service with Storage].

Ett exempel på hur miljövariabler för en Azure-webbplats finns [Node.js-Webbapp med lagring][Node.js Web Application with Storage].

## <a name="create-a-topic"></a>Skapa ett ämne
Den **ServiceBusService** objekt kan du arbeta med ämnen. Följande kod skapar en **ServiceBusService** objekt. Lägg till den övre delen av den **server.js** filen när instruktionen för att importera modulen för azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Genom att anropa `createTopicIfNotExists` på den **ServiceBusService** objekt returneras det angivna avsnittet (om den finns), eller kommer att skapas en ny artikel med det angivna namnet. I följande kod används `createTopicIfNotExists` ska skapa eller Anslut till avsnittet med namnet `MyTopic`:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

Den `createServiceBusService` metoden stöder också ytterligare alternativ som gör att du kan åsidosätta standardinställningar för ämnet, till exempel time to live-meddelande eller avsnittet maximala storlek. I följande exempel anger maximal avsnittet storleken till 5GB med tid att live 1 minut:

```javascript
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filter
Valfria filtrering åtgärder kan användas för åtgärder som utförs med hjälp av **ServiceBusService**. Filtrering operations kan innehålla loggning, försöker automatiskt igen och så vidare. Filtren är objekt som implementerar en metod med signaturen:

```javascript
function handle (requestOptions, next)
```

När du utför förbearbetning på begäran-alternativ måste metoden anropar `next`, skicka ett återanrop med följande signatur:

```javascript
function (returnObject, finalCallback, next)
```

I den här motringning och efter bearbetning av `returnObject` (svaret från begäran till servern), återanropet måste anropa sedan om den finns för att fortsätta att bearbeta filter eller anropa `finalCallback` annars att avsluta tjänsten-anrop.

Två filter som implementerar logik som medföljer Azure SDK för Node.js, **ExponentialRetryPolicyFilter** och **LinearRetryPolicyFilter**. Följande kod skapar en **ServiceBusService** objekt som använder den **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Skapa prenumerationer
Ämnesprenumerationer skapas också med den **ServiceBusService** objekt. Prenumerationer är namngivna och kan ha ett valfritt filter som begränsar uppsättningen av meddelanden som skickas till prenumerationens virtuella kö.

> [!NOTE]
> Prenumerationer är beständiga och fortsätter att finns förrän antingen de eller avsnittet som de är kopplade till, tas bort. Om ditt program innehåller logik för att skapa en prenumeration, det först ska kontrollera om den redan finns med hjälp av den `getSubscription` metoden.
>
>

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Skapa en prenumeration med standardfiltret (MatchAll)
**MatchAll**-filtret är det standardfilter som används om inget filter anges när en ny prenumeration skapas. När den **MatchAll** filter används, alla meddelanden som publiceras till ämnet placeras i prenumerationens virtuella kö. I följande exempel skapar en prenumeration med namnet ”AllMessages” och använder förvalet **MatchAll** filter.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Skapa prenumerationer med filter
Du kan även skapa filter som gör att du omfattning som meddelanden skickas till ett ämne visas inom en viss ämnesprenumeration.

Den mest flexibla typen av filter som stöds av prenumerationerna är den **SqlFilter**, som implementerar en deluppsättning av SQL92. SQL-filter tillämpas på egenskaperna i de meddelanden som publiceras till ämnet. Mer information om uttryck som kan användas med ett SQL-filter granskar den [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] syntax.

Filter kan läggas till en prenumeration med hjälp av den `createRule` metod för den **ServiceBusService** objekt. Den här metoden kan du lägga till nya filter till en befintlig prenumeration.

> [!NOTE]
> Eftersom standardfiltret används automatiskt för alla nya prenumerationer, måste du först ta bort standardfiltret eller **MatchAll** åsidosätter eventuella filter som du kan ange. Du kan ta bort Standardregeln med hjälp av den `deleteRule` metod för den **ServiceBusService** objekt.
>
>

I följande exempel skapas en prenumeration med namnet `HighMessages` med en **SqlFilter** som endast väljer meddelanden som har en anpassad `messagenumber` egenskap som är större än 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'HighMessages',
            'HighMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

På samma sätt i följande exempel skapas en prenumeration med namnet `LowMessages` med en **SqlFilter** som endast väljer meddelanden som har en `messagenumber` egenskap som är mindre än eller lika med 3:

```javascript
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'LowMessages',
            'LowMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

När ett meddelande skickas nu till `MyTopic`, kommer alltid levereras till mottagare som prenumererar på den `AllMessages` avsnittet prenumeration, och levereras selektivt till mottagare som prenumererar på den `HighMessages` och `LowMessages` ämnesprenumerationer (beroende på innehållet i meddelandet).

## <a name="how-to-send-messages-to-a-topic"></a>Hur du skickar meddelanden till ett ämne
Om du vill skicka ett meddelande till en Service Bus-ämne, ditt program måste använda den `sendTopicMessage` metod för den **ServiceBusService** objekt.
Meddelanden som skickas till Service Bus-ämnen är **BrokeredMessage** objekt.
**BrokeredMessage** objekt har en uppsättning standardegenskaper (t.ex `Label` och `TimeToLive`), en ordlista som används för att lagra anpassade programspecifika egenskaper och en brödtext med strängdata. Ett program kan konfigurera meddelandets brödtext för meddelandet genom att skicka ett strängvärde till den `sendTopicMessage` och alla obligatoriska standardegenskaper fylls i med standardvärden.

I följande exempel visar hur du skickar fem testmeddelanden till `MyTopic`. Observera att den `messagenumber` -värdet för varje meddelande varierar på upprepning av loopen (detta avgör vilka prenumerationer som får det):

```javascript
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i ett ämne men det finns ett tak för den totala storleken för de meddelanden som ligger i ett ämne. Den här ämnesstorleken definieras när ämnet skapas, med en övre gräns på 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
Meddelanden tas emot från en prenumeration med hjälp av den `receiveSubscriptionMessage` -metoden i den **ServiceBusService** objekt. Som standard tas meddelanden bort från prenumerationen som de läses; men du kan läsa (titt) och låsa meddelandet utan att ta bort den från prenumerationen genom att ange den valfria parametern `isPeekLock` till **SANT**.

Standardbeteendet för läsning och ta bort meddelandet som en del av receive-åtgärden är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus kommer att ha markerat meddelandet som Förbrukat, och sedan när programmet startas om och börjar förbruka meddelanden igen, att ha missat meddelandet som förbrukades innan kraschen.

Om den `isPeekLock` parametern anges till **SANT**, inleveransen en åtgärd i två steg, vilket gör det möjligt att stödprogram som inte tolererar att ett meddelande saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet.
När programmet har avslutat bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida bearbetning), Slutför det andra steget i processen genom att anropa **deleteMessage** metod och ge meddelandet som ska tas bort som en parameter. Den **deleteMessage** metoden att markera meddelandet som Förbrukat och ta bort den från prenumerationen.

Exemplet nedan visar hur meddelanden kan tas emot och bearbetat använder `receiveSubscriptionMessage`. I exempel först tar emot och tar bort meddelandet från prenumerationen 'Highmessages' och får ett meddelande från en 'HighMessages' prenumeration med hjälp av `isPeekLock` inställd på true. Meddelande med raderas sedan `deleteMessage`:

```javascript
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        })
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram går inte att bearbeta meddelandet av någon anledning, så kan det anropa den `unlockMessage` -metoden i den **ServiceBusService** objekt. Detta gör att Service Bus låser upp meddelandet i prenumerationen och göra den tillgänglig för tas emot igen, antingen genom samma användningsprogram eller ett annat användningsprogram.

Det finns också en tidsgräns som är associerad med ett meddelande som ligger låst i prenumerationen om programmet misslyckas med att bearbeta meddelandet innan timeout för lås går ut (till exempel om programmet kraschar), kommer Service Bus låser upp meddelandet automatiskt och gör det tillgängligt att tas emot igen.

I händelse av att programmet kraschar efter att meddelandet har bearbetats men innan den `deleteMessage` metoden anropas sedan meddelandet att levereras till programmet när den startas om. Det här kallas ofta *At Least Once Processing*, det vill säga, varje meddelande bearbetas minst en gång men i vissa situationer kan samma meddelande levereras. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Detta uppnås ofta med hjälp av den **MessageId** för meddelandet, som förblir konstant under alla leveransförsök.

## <a name="delete-topics-and-subscriptions"></a>Ta bort ämnen och prenumerationer
Ämnen och prenumerationer är beständiga och måste vara explicit bort antingen via den [Azure-portalen] [ Azure portal] eller programmässigt.
I följande exempel visar hur du tar bort ämnet med namnet `MyTopic`:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Om du tar bort ett ämne tas även bort alla prenumerationer som är registrerade med ämnet. Prenumerationer kan även tas bort separat. I följande exempel visas hur du tar bort en prenumeration med namnet `HighMessages` från den `MyTopic` avsnittet:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Service Bus-ämnen kan du följa dessa länkar om du vill veta mer.

* Se [köer, ämnen och prenumerationer][Queues, topics, and subscriptions].
* API-referens för [SqlFilter][SqlFilter].
* Besök den [Azure SDK för noden] [ Azure SDK for Node] databasen på GitHub.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[skapa och distribuera ett Node.js-program till en Azure-webbplats]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-cloud-service-nodejs.md
