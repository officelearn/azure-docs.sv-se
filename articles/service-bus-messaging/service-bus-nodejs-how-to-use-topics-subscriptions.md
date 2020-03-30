---
title: Använd Azure Service Bus-ämnen med azure/service-bus Node.js-paketet
description: Lär dig hur du använder servicebussavsnitt och prenumerationer i Azure från en Node.js-app med hjälp av azure/service-bus-paketet.
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
ms.openlocfilehash: c85b63b4a56e74b0fef9a122ec995b4106496cbe
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330454"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azure-sb-package"></a>Snabbstart: Så här använder du servicebussavsnitt och prenumerationer med Node.js och azure-sb-paketet
I den här självstudien får du lära dig hur du skapar Node.js-program för att skicka meddelanden till ett Service Bus-ämne och ta emot meddelanden från en Service [Bus-prenumeration med azure-sb-paketet.](https://www.npmjs.com/package/azure-sb) Exemplen skrivs i JavaScript och använder node.js [Azure-modulen](https://www.npmjs.com/package/azure) som internt använder `azure-sb` paketet.

> [!IMPORTANT]
> [Azure-sb-paketet](https://www.npmjs.com/package/azure-sb) använder [API:er för servicebuss REST-körning.](/rest/api/servicebus/service-bus-runtime-rest) Du kan få en snabbare [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) upplevelse med det nya paketet som använder det snabbare [AMQP 1.0-protokollet](service-bus-amqp-overview.md). 
> 
> Mer information om det nya paketet finns i [Så här använder du Service @azure/service-bus Bus-ämnen och prenumerationer med Node.js och paket](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-topics-subscriptions-new-package), annars fortsätt läsa för att se hur du använder [azure-paketet.](https://www.npmjs.com/package/azure)

De scenarier som behandlas här inkluderar:

- Skapa ämnen och prenumerationer 
- Skapa prenumerationsfilter 
- Skicka meddelanden till ett ämne 
- Ta emot meddelanden från en prenumeration
- Ta bort ämnen och prenumerationer 

Mer information om ämnen och prenumerationer finns i [avsnittet Nästa steg.](#next-steps)

## <a name="prerequisites"></a>Krav
- En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina fördelar för [Visual Studio- eller MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Följ stegen i [snabbstart: Använd Azure-portalen för att skapa ett Service Bus-ämne och prenumerationer på ämnet](service-bus-quickstart-topics-subscriptions-portal.md) för att skapa ett **servicebussnamnområde** och hämta **anslutningssträngen**.

    > [!NOTE]
    > Du skapar ett **ämne** och en **prenumeration** på ämnet med **nod.js** i den här snabbstarten. 

## <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program
Skapa ett tomt nod.js-program. Instruktioner om hur du skapar ett Node.js-program finns i [Skapa och distribuera ett Node.js-program till en Azure-webbplats], [Node.js Cloud Service][Node.js Cloud Service] med Windows PowerShell eller Webbplats med WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera ditt program så att det använder Service Bus
Om du vill använda Service Bus laddar du ned Node.js Azure-paketet. Det här paketet innehåller en uppsättning bibliotek som kommunicerar med Service Bus REST-tjänsterna.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Använd Nod Package Manager (NPM) för att hämta paketet
1. Öppna ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix).
2. Navigera till mappen där du skapade exempelprogrammet.
3. Skriv **npm installera azure** i kommandofönstret, vilket bör resultera i följande utdata:

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
3. Du kan köra **kommandot ls** manuellt för att kontrollera att en mapp för **\_nodmoduler har skapats.** I den mappen hittar du **azure-paketet,** som innehåller de bibliotek du behöver för att komma åt Service Bus-ämnen.

### <a name="import-the-module"></a>Importera modulen
Med Anteckningar eller en annan textredigerare lägger du till följande högst upp i filen **server.js** i programmet:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Konfigurera en servicebussanslutning
Azure-modulen läser `AZURE_SERVICEBUS_CONNECTION_STRING` av miljövariabeln för anslutningssträngen som du fick från det tidigare steget,"Hämta autentiseringsuppgifterna". Om den här miljövariabeln inte har angetts måste du ange kontoinformationen när du ringer `createServiceBusService`.

Ett exempel på hur du anger miljövariabler för en Azure Cloud Service finns i [Ange miljövariabler](../container-instances/container-instances-environment-variables.md#azure-cli-example).



## <a name="create-a-topic"></a>Skapa ett ämne
**Med ServiceBusService-objektet** kan du arbeta med ämnen. Följande kod skapar ett **ServiceBusService-objekt.** Lägg till den högst upp i **filen server.js,** efter satsen för att importera azure-modulen:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Om du `createTopicIfNotExists` anropar **ServiceBusService-objektet** returneras det angivna avsnittet (om det finns) eller så skapas ett nytt ämne med det angivna namnet. Följande kod `createTopicIfNotExists` används för att skapa `MyTopic`eller ansluta till ämnet:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

Metoden `createTopicIfNotExists` stöder också ytterligare alternativ, som gör att du kan åsidosätta standardavsnittsinställningar som meddelandetid för att leva eller maximal ämnesstorlek. 

I följande exempel anges den maximala ämnesstorleken till 5 GB med en tid att leva på en minut:

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
Valfria filtreringsåtgärder kan tillämpas på åtgärder som utförs med **ServiceBusService**. Filtreringsåtgärder kan omfatta loggning, automatiskt återförsök, etc. Filter är objekt som implementerar en metod med signaturen:

```javascript
function handle (requestOptions, next)
```

När du har utfört förbearbetning på `next`alternativen för begäran anropar metoden och skickar en motringning med följande signatur:

```javascript
function (returnObject, finalCallback, next)
```

I den här motringningen `returnObject` och efter bearbetning av (svaret från begäran till servern) måste motringningen antingen anropa nästa `finalCallback` (om det finns) för att fortsätta bearbeta andra filter eller anropa för att avsluta tjänstanropet.

Azure SDK för Node.js innehåller två filter som implementerar logik för omförsök: **ExponentialRetryPolicyFilter** och **LinearRetryPolicyFilter**. Följande kod skapar ett **ServiceBusService-objekt** som använder **ExponentialRetryPolicyFilter:**

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Skapa prenumerationer
Ämnesprenumerationer skapas också med **ServiceBusService-objektet.** Prenumerationer namnges och kan ha ett valfritt filter som begränsar uppsättningen meddelanden som levereras till prenumerationens virtuella kö.

> [!NOTE]
> Som standard är prenumerationer beständiga tills antingen de, eller det ämne de är associerade med, tas bort. Om ditt program innehåller logik för att skapa en prenumeration bör `getSubscription` det först kontrollera om prenumerationen finns med hjälp av metoden.
>
> Du kan ta bort prenumerationerna automatiskt genom att ange [egenskapen AutoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Skapa en prenumeration med standardfiltret (MatchAll)
**Filtret MatchAll** är standardfiltret som används när en prenumeration skapas. När du använder **MatchAll**-filtret kommer alla meddelanden som publiceras till ämnet att placeras i prenumerationens virtuella kö. I följande exempel skapas en prenumeration med namnet AllMessages och standardfiltret **MatchAll** används.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Skapa prenumerationer med filter
Du kan också skapa filter som gör att du kan begränsa vilka meddelanden som skickas till ett ämne som ska visas i en viss ämnesprenumeration.

Den mest flexibla typen av filter som stöds av prenumerationer är **SqlFilter**, som implementerar en delmängd av SQL92. SQL-filter tillämpas på egenskaperna i de meddelanden som publiceras till ämnet. Mer information om de uttryck som kan användas med ett SQL-filter finns i syntaxen [SqlFilter.SqlExpression.][SqlFilter.SqlExpression]

Filter kan läggas till i `createRule` en prenumeration med hjälp av metoden för **ServiceBusService-objektet.** Med den här metoden kan du lägga till nya filter i en befintlig prenumeration.

> [!NOTE]
> Eftersom standardfiltret tillämpas automatiskt på alla nya prenumerationer måste du först ta bort standardfiltret, annars åsidosätter **MatchAll** alla andra filter som du kan ange. Du kan ta bort standardregeln med hjälp av `deleteRule` metoden för **ServiceBusService-objektet.**
>
>

I följande exempel skapas `HighMessages` en prenumeration med namnet **SqlFilter** som `messagenumber` bara väljer meddelanden som har en anpassad egenskap som är större än 3:

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

I följande exempel skapas en `LowMessages` prenumeration med namnet **SqlFilter** som bara `messagenumber` väljer meddelanden som har en egenskap som är mindre än eller lika med 3:

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

När ett meddelande nu `MyTopic`skickas till levereras det till `AllMessages` mottagare som prenumererar på ämnesprenumerationen och levereras selektivt till mottagare `HighMessages` som prenumererar på och `LowMessages` ämnesprenumerationer (beroende på meddelandeinnehållet).

## <a name="how-to-send-messages-to-a-topic"></a>Så här skickar du meddelanden till ett ämne
Om du vill skicka ett meddelande till ett `sendTopicMessage` Service Bus-ämne måste programmet använda metoden för **ServiceBusService-objektet.**
Meddelanden som skickas till Service Bus-ämnen är **BrokeredMessage-objekt.**
**BrokeredMessage-objekt** har en uppsättning standardegenskaper (till exempel `Label` och `TimeToLive`), en ordlista som används för att lagra anpassade programspecifika egenskaper och en samling strängdata. Ett program kan ange meddelandets brödtext genom `sendTopicMessage` att skicka ett strängvärde till de och alla nödvändiga standardegenskaper fylls i som standardvärden.

I följande exempel visas hur du skickar `MyTopic`fem testmeddelanden till . Egenskapsvärdet `messagenumber` för varje meddelande varierar beroende på hur loopen är med (den här egenskapen avgör vilka prenumerationer som får det):

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

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som finns i ett ämne, men det finns en gräns för den totala storleken på de meddelanden som innehas av ett ämne. Den här ämnesstorleken definieras när ämnet skapas, med en övre gräns på 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
Meddelanden tas emot från en `receiveSubscriptionMessage` prenumeration med metoden på **ServiceBusService-objektet.** Som standard tas meddelanden bort från prenumerationen när de läs.As default, messages are deleted from the subscription as they are read. Du kan dock ange `isPeekLock` den valfria parametern till **true** to read (peek) och låsa meddelandet utan att ta bort det från prenumerationen.

Standardbeteendet för att läsa och ta bort meddelandet som en del av mottagningsåtgärden är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera att inte bearbeta ett meddelande när det finns ett fel. För att förstå detta, överväga ett scenario där konsumenten utfärdar mottagningsbegäran och sedan kraschar innan du bearbetar den. Eftersom Service Bus har markerat meddelandet som förbrukat, då när programmet startas om och börjar konsumera meddelanden igen, har det missat meddelandet som förbrukades före kraschen.

Om `isPeekLock` parametern är inställd på **true**blir mottagningen en tvåstegsåtgärd, vilket gör det möjligt att stödja program som inte tål missade meddelanden. När Service Bus tar emot en begäran hittar den nästa meddelande att använda, låser det för att förhindra att andra konsumenter tar emot den och returnerar det till programmet.
När programmet bearbetar meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida bearbetning) slutförs den andra fasen av mottagningsprocessen genom att anropa **metoden deleteMessage** och skickar meddelandet att ta bort som en parameter. Metoden **deleteMessage** markerar meddelandet som förbrukat och tar bort det från prenumerationen.

Följande exempel visar hur meddelanden kan tas emot `receiveSubscriptionMessage`och bearbetas med . Exemplet tar först emot och tar bort ett meddelande från prenumerationen "LowMessages" och tar sedan `isPeekLock` emot ett meddelande från prenumerationen "HighMessages" med värdet true. Meddelandet tas sedan bort `deleteMessage`med:

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
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram inte kan bearbeta meddelandet av någon `unlockMessage` anledning kan det anropa metoden på **ServiceBusService-objektet.** Den här metoden gör att Service Bus låser upp meddelandet i prenumerationen och gör det tillgängligt för att tas emot igen. I det här fallet, antingen av samma tidskrävande program eller av ett annat tidskrävande program.

Det finns också en timeout som är associerad med ett meddelande som är låst i prenumerationen. Om programmet inte bearbetar meddelandet innan låstidsgränsen upphör att gälla (till exempel om programmet kraschar) låser Service Bus upp meddelandet automatiskt och gör det tillgängligt för att tas emot igen.

I händelse av att programmet kraschar efter `deleteMessage` bearbetning av meddelandet men innan metoden anropas, levereras meddelandet till programmet när det startas om. Detta kallas ofta *minst en gång bearbetning*. Det vill säga varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras på nytt. Om scenariot inte kan tolerera dubblettbearbetning bör du lägga till logik i ditt program för att hantera leverans av dubblettmeddelanden. Du kan använda egenskapen **MessageId** för meddelandet, som förblir konstant över leveransförsök.

## <a name="delete-topics-and-subscriptions"></a>Ta bort ämnen och prenumerationer
Ämnen och prenumerationer är beständiga om inte [egenskapen autoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle) har angetts och måste uttryckligen tas bort antingen via [Azure-portalen][Azure portal] eller programmässigt.
I följande exempel visas hur du `MyTopic`tar bort ämnet med namnet :

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Om du tar bort ett ämne så tar du även bort alla prenumerationer som är registrerade på det ämnet. Prenumerationer kan även tas bort separat. I följande exempel visas hur `HighMessages` du `MyTopic` tar bort en prenumeration som heter ämnet:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer tillåter användare att ansluta till ett servicebussnamnområde och administrera meddelandeenheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjligheten att testa ämne, köer, prenumerationer, relätjänster, meddelandehubbar och händelsehubbar. 

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Service Bus ämnen, följ dessa länkar för att lära dig mer.

* Se [Köer, ämnen och prenumerationer][Queues, topics, and subscriptions].
* API-referens för [SqlFilter][SqlFilter].
* Besök [Azure SDK för nod-databasen][Azure SDK for Node] på GitHub.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /javascript/api/@azure/arm-servicebus/sqlfilter?view=azure-node-latest
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Skapa och distribuera ett Node.js-program till en Azure-webbplats]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

