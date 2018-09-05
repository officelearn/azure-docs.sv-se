---
title: Hur du använder Azure Service Bus-ämnen och prenumerationer med Node.js | Microsoft Docs
description: Lär dig hur du använder Service Bus-ämnen och prenumerationer i Azure från en Node.js-app.
services: service-bus-messaging
documentationcenter: nodejs
author: spelluru
manager: timlt
editor: ''
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.openlocfilehash: fbb43d07296ca573f0c4cb9f1e10e005633ade06
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700104"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs"></a>Använd Service Bus-ämnen och prenumerationer med Node.js

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Den här guiden beskriver hur du använder Service Bus-ämnen och prenumerationer från Node.js-program. Scenarier som omfattas är:

- Skapa ämnen och prenumerationer 
- Skapa prenumerationsfilter 
- Skicka meddelanden till ett ämne 
- Ta emot meddelanden från en prenumeration
- Ta bort ämnen och prenumerationer 

Läs mer om ämnen och prenumerationer, [nästa steg](#next-steps) avsnittet.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program
Skapa en tom Node.js-program. Anvisningar om hur du skapar ett Node.js-program finns i [skapa och distribuera ett Node.js-program till en Azure Web Site], [Node.js molntjänst] [ Node.js Cloud Service] med hjälp av Windows PowerShell eller webbplats med WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera programmet att använda Service Bus
Ladda ned Node.js Azure-paketet om du vill använda Service Bus. Det här paketet innehåller en uppsättning klientbibliotek som kommunicerar med Service Bus REST-tjänster.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Använda Node Package Manager (NPM) för att hämta paketet
1. Öppna ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac), eller **Bash** (Unix).
2. Navigera till mappen där du skapade exempelprogrammet.
3. Typ **npm-installationsprogrammet azure** i kommandofönstret, vilket resulterar i följande utdata:

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
3. Du kan köra manuellt i **ls** kommando för att kontrollera att en **nod\_moduler** mappen har skapats. I den mappen hittar den **azure** paket som innehåller de bibliotek som du behöver komma åt Service Bus-ämnen.

### <a name="import-the-module"></a>Importera modulen
Med hjälp av anteckningar eller något annat textredigeringsprogram, Lägg till följande överst i **server.js** i appen:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Skapa en Service Bus-anslutning
Azure-modulen läser miljövariabeln `AZURE_SERVICEBUS_CONNECTION_STRING` för anslutningssträngen som du fick från det tidigare steget ”hämta i autentiseringsuppgifterna”. Om den här miljövariabeln inte har angetts måste du ange informationen när du anropar `createServiceBusService`.

Ett exempel för att ställa in miljövariabler för ett Azure Cloud Services, finns i [Node.js molntjänst med Storage][Node.js Cloud Service with Storage].



## <a name="create-a-topic"></a>Skapa ett ämne
Den **ServiceBusService** objekt kan du arbeta med ämnen. Följande kod skapar en **ServiceBusService** objekt. Lägga till den i den övre delen av den **server.js** filen efter instruktionen för att importera azure-modulen:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Om du anropar `createTopicIfNotExists` på den **ServiceBusService** objekt kan det angivna ämnet returneras (om det finns) eller en ny artikel med det angivna namnet har skapats. I följande kod används `createTopicIfNotExists` att skapa eller ansluta till ämnet med namnet `MyTopic`:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

Den `createServiceBusService` metoden stöder också ytterligare alternativ som gör att du kan åsidosätta standardinställningarna för ämnet, till exempel tid för TTL-värde eller högsta ämnesstorleken. 

I följande exempel anger den maximala avsnitt storleken till 5 GB med tid TTL-värde på en minut:

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
Valfritt filtrering åtgärder kan användas för åtgärder som utförs med hjälp av **ServiceBusService**. Filtrering av åtgärder kan innehålla loggning, försöker automatiskt igen och så vidare. Filter är objekt som implementerar en metod med signaturen:

```javascript
function handle (requestOptions, next)
```

När du har utfört Förbearbeta på begäran-alternativ, metoden anropar `next`, och skickar en motringning med följande signatur:

```javascript
function (returnObject, finalCallback, next)
```

I den här motringning och efter bearbetning i `returnObject` (svaret från begäran till servern), återanropet som måste antingen anropa bredvid (om det finns) om du vill fortsätta att bearbeta andra filter eller anropa `finalCallback` att avsluta tjänstanrop.

Azure SDK för Node.js innehåller två filter som implementerar logik för omförsök: **ExponentialRetryPolicyFilter** och **LinearRetryPolicyFilter**. Detta skapar en **ServiceBusService** objekt som använder den **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Skapa prenumerationer
Ämnesprenumerationer skapas också med den **ServiceBusService** objekt. Prenumerationer är namngivna och kan ha ett valfritt filter som begränsar uppsättningen av meddelanden som levereras till prenumerationens virtuella kö.

> [!NOTE]
> Prenumerationer är beständiga förrän antingen de eller ämnet som de är associerade med, tas bort. Om programmet innehåller logik för att skapa en prenumeration, det först ska kontrollera om prenumerationen som finns med hjälp av den `getSubscription` metoden.
>
>

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Skapa en prenumeration med standardfiltret (MatchAll)
Den **MatchAll** filtret är det standardfilter som används när en prenumeration skapas. När du använder **MatchAll**-filtret kommer alla meddelanden som publiceras till ämnet att placeras i prenumerationens virtuella kö. I följande exempel skapar en prenumeration med namnet AllMessages och använder förvalet **MatchAll** filter.

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

Filter kan läggas till en prenumeration med hjälp av den `createRule` -metoden för den **ServiceBusService** objekt. Den här metoden kan du lägga till nya filter i en befintlig prenumeration.

> [!NOTE]
> Eftersom standardfiltret används automatiskt på alla nya prenumerationer, måste du först ta bort standardfiltret eller **MatchAll** åsidosätter eventuella filter som du kan ange. Du kan ta bort Standardregeln med hjälp av den `deleteRule` -metoden för den **ServiceBusService** objekt.
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

När ett meddelande skickas nu till `MyTopic`, kommer meddelandet att skickas till mottagare som prenumererar på den `AllMessages` ämnesprenumerationen levereras selektivt till mottagare som prenumererar på den `HighMessages` och `LowMessages` ämnesprenumerationer (beroende på meddelandeinnehåll).

## <a name="how-to-send-messages-to-a-topic"></a>Hur du skickar meddelanden till ett ämne
Om du vill skicka ett meddelande till en Service Bus-ämne, ditt program måste använda den `sendTopicMessage` -metoden för den **ServiceBusService** objekt.
Meddelanden som skickas till Service Bus-ämnen är **BrokeredMessage** objekt.
**BrokeredMessage** objekt har en uppsättning standardegenskaper (t.ex `Label` och `TimeToLive`), en ordlista som används för att lagra anpassade egenskaper för programspecifika och en brödtext med strängdata. Ett program kan konfigurera meddelandets brödtext genom att skicka ett strängvärde till den `sendTopicMessage` och alla obligatoriska standardegenskaper fylls med standardvärden.

I följande exempel visar hur du skickar fem testmeddelanden till `MyTopic`. Den `messagenumber` egenskapsvärdet för varje meddelande varierar på upprepning av loopen (detta avgör vilka prenumerationer som får det):

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

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för hur många meddelanden som ligger i ett avsnitt, men det finns en gräns för den totala storleken på de meddelanden som ligger i ett ämne. Den här ämnesstorleken definieras när ämnet skapas, med en övre gräns på 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
Meddelanden tas emot från en prenumeration med hjälp av den `receiveSubscriptionMessage` metoden på den **ServiceBusService** objekt. Som standard tas meddelanden bort från prenumerationen eftersom de är skrivskyddade. Du kan dock ange den valfria parametern `isPeekLock` till **SANT** att läsa (peek) och låsa meddelandet utan att ta bort den från prenumerationen.

Standardbeteendet för att läsa och radera meddelandet som en del av åtgärden ta emot är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. Tänk dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan bearbetningen för att förstå det här beteendet. Eftersom Service Bus kommer att ha markerat meddelandet som Förbrukat, att sedan när programmet startas om och börjar förbruka meddelanden igen, ha missat meddelandet som förbrukades innan kraschen.

Om den `isPeekLock` parametern är inställd på **SANT**, ta emot blir en åtgärd i två steg, vilket gör det möjligt att stödprogram som inte tolererar att missade. När Service Bus tar emot en begäran, det upp nästa meddelande att använda låser det för att förhindra att andra användare från att ta emot den och tillbaka till programmet.
När programmet kan bearbeta meddelandet (eller lagrar den på ett tillförlitligt sätt för framtida bearbetning), den är klar det andra steget i processen genom att anropa **deleteMessage** metoden och skickar meddelandet att ta bort som en parameter. Den **deleteMessage** metoden markerar meddelandet som Förbrukat och tas bort från prenumerationen.

Följande exempel visar hur meddelanden kan tas emot och bearbetat använder `receiveSubscriptionMessage`. I exempel först tar emot och tar bort meddelandet från prenumerationen 'LowMessages' och får ett meddelande från prenumerationen 'HighMessages' med `isPeekLock` inställd på Sant. Sedan tar bort meddelandet med hjälp av `deleteMessage`:

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
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram är det går inte att bearbeta meddelandet av någon anledning så kan det anropa den `unlockMessage` metoden på den **ServiceBusService** objekt. Den här metoden gör att Service Bus att låsa upp meddelandet i prenumerationen och göra den tillgänglig att tas emot igen. I den här instansen, antingen genom samma användningsprogram eller ett annat användningsprogram.

Det finns också en tidsgräns som är associerade med ett meddelande som ligger låst i prenumerationen. Om programmet inte kan bearbeta meddelandet innan timeout för lås upphör att gälla (till exempel om programmet kraschar), Service Bus låser upp meddelandet automatiskt och gör det tillgängligt att tas emot igen.

I den händelse att programmet kraschar efter behandlingen av meddelandet men innan den `deleteMessage` metoden anropas, meddelandet once till programmet när den startas om. Det här beteendet kallas ofta *bearbetning minst en gång*. Det vill säga att varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande kan levereras. Om scenariot inte tolererar duplicerad bearbetning, bör du lägga till logik i ditt program för att hantera duplicerad meddelandeleverans. Du kan använda den **MessageId** för meddelandet, förblir konstant under alla leveransförsök.

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

Om du tar bort ett ämne så tar du även bort alla prenumerationer som är registrerade på det ämnet. Prenumerationer kan även tas bort separat. I följande exempel visas hur du tar bort en prenumeration med namnet `HighMessages` från den `MyTopic` avsnittet:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Service Bus-ämnen kan du följa dessa länkar om du vill veta mer.

* Se [köer, ämnen och prenumerationer][Queues, topics, and subscriptions].
* API-referens för [SqlFilter][SqlFilter].
* Gå till den [Azure SDK för Node] [ Azure SDK for Node] arkivet på GitHub.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Skapa och distribuera ett Node.js-program till en Azure Web Site]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

