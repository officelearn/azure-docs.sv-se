---
title: 'Snabb start: använda Azure Service Bus ämnen och prenumerationer med Node. js'
description: 'Snabb start: Lär dig hur du använder Service Bus ämnen och prenumerationer i Azure från en Node. js-app.'
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
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 80ca9589e7181518874dc26a9a42b57774f60498
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561582"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azure-sb-package"></a>Snabb start: använda Service Bus ämnen och prenumerationer med Node. js och Azure-SB-paketet
> [!div class="op_multi_selector" title1="Programmeringsspråk" title2="Node. js-paket"]
> - [(Node. js | Azure-SB)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node. js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

I den här självstudien får du lära dig hur du skapar Node. js-program för att skicka meddelanden till ett Service Bus ämne och ta emot meddelanden från en Service Bus-prenumeration med [Azure-SB-](https://www.npmjs.com/package/azure-sb) paketet. Exemplen är skrivna i Java Script och använder [Azure-modulen](https://www.npmjs.com/package/azure) Node. js som internt använder `azure-sb`-paketet.

[Azure-SB-](https://www.npmjs.com/package/azure-sb) paketet använder [Service Bus REST-API: er för körnings tid](/rest/api/servicebus/service-bus-runtime-rest). Du kan få en snabbare upplevelse med hjälp av det nya [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) -paketet som använder det snabbare [AMQP 1,0-protokollet](service-bus-amqp-overview.md). Mer information om det nya paketet finns i [så här använder du Service Bus ämnen och prenumerationer med Node. js och @azure/service-bus paket](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-topics-subscriptions-new-package), annars fortsätter läsa för att se hur du använder [Azure](https://www.npmjs.com/package/azure) -paketet.

De scenarier som beskrivs här är:

- Skapa ämnen och prenumerationer 
- Skapa prenumerations filter 
- Skicka meddelanden till ett ämne 
- Ta emot meddelanden från en prenumeration
- Ta bort ämnen och prenumerationer 

Mer information om ämnen och prenumerationer finns i avsnittet [Nästa steg](#next-steps) .

## <a name="prerequisites"></a>Krav
- En Azure-prenumeration. Du behöver ett Azure-konto för att slutföra den här självstudien. Du kan aktivera dina [förmåner för Visual Studio eller MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Följ stegen i [snabb starten: använd Azure Portal för att skapa ett Service Bus ämne och prenumerationer på avsnittet](service-bus-quickstart-topics-subscriptions-portal.md) för att skapa ett Service Bus- **namnområde** och hämta **anslutnings strängen**.

    > [!NOTE]
    > Du kommer att skapa ett **ämne** och en **prenumeration** på avsnittet med hjälp av **Node. js** i den här snabb starten. 

## <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program
Skapa ett tomt Node. js-program. Instruktioner för hur du skapar ett Node. js-program finns i [skapa och distribuera ett Node. js-program till en Azure-webbplats], [Node. js-moln tjänst][Node.js Cloud Service] med Windows PowerShell eller webbplats med WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera programmet så att det använder Service Bus
Om du vill använda Service Bus hämtar du Node. js Azure-paketet. Det här paketet innehåller en uppsättning bibliotek som kommunicerar med Service Bus REST-tjänsterna.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Hämta paketet med hjälp av Node Pack Manager (NPM)
1. Öppna ett kommando rads gränssnitt, till exempel **PowerShell** (Windows), **Terminal** (Mac) eller **bash** (UNIX).
2. Navigera till mappen där du skapade exempel programmet.
3. Skriv **NPM installera Azure** i kommando fönstret, vilket bör resultera i följande utdata:

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
3. Du kan köra **ls** -kommandot manuellt för att verifiera att en **nod\_modules** -mapp har skapats. I den mappen hittar du **Azure** -paketet som innehåller de bibliotek som du behöver för att komma åt Service Bus ämnen.

### <a name="import-the-module"></a>Importera modulen
Använd anteckningar eller något annat text redigerings program och Lägg till följande överst i filen **Server. js** i programmet:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Konfigurera en Service Bus anslutning
Azure-modulen läser miljövariabeln `AZURE_SERVICEBUS_CONNECTION_STRING` för anslutnings strängen som du hämtade från föregående steg, "hämta autentiseringsuppgifterna". Om miljövariabeln inte har angetts måste du ange konto informationen när du anropar `createServiceBusService`.

Ett exempel på hur du anger miljövariabler för en Azure Cloud service finns i [Ange miljövariabler](../container-instances/container-instances-environment-variables.md#azure-cli-example).



## <a name="create-a-topic"></a>Skapa ett ämne
Med **ServiceBusService** -objektet kan du arbeta med ämnen. Följande kod skapar ett **ServiceBusService** -objekt. Lägg till det nära överst i filen **Server. js** , efter instruktionen att importera Azure-modulen:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Om du anropar `createTopicIfNotExists` på **ServiceBusService** -objektet returneras det angivna avsnittet (om det finns), eller så skapas ett nytt avsnitt med det angivna namnet. Följande kod använder `createTopicIfNotExists` för att skapa eller ansluta till ämnet med namnet `MyTopic`:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

Metoden `createTopicIfNotExists` stöder också ytterligare alternativ, vilket gör att du kan åsidosätta standard ämnes inställningar, till exempel meddelande tid till Live eller maximal ämnes storlek. 

I följande exempel anges den maximala ämnes storleken till 5 GB med en tid på en minut:

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
Valfria filtrerings åtgärder kan tillämpas på åtgärder som utförs med hjälp av **ServiceBusService**. Filtrerings åtgärder kan omfatta loggning, automatiskt försök igen osv. Filter är objekt som implementerar en metod med signaturen:

```javascript
function handle (requestOptions, next)
```

Efter att du utfört förbearbetningen av begär ande alternativen anropar metoden `next`och skickar ett motanrop med följande signatur:

```javascript
function (returnObject, finalCallback, next)
```

I det här återanropet, och efter bearbetning av `returnObject` (svaret från begäran till servern), måste återanropet antingen anropa Next (om det finns) för att fortsätta att bearbeta andra filter, eller anropa `finalCallback` för att avsluta tjänst anropet.

Azure SDK för Node.js innehåller två filter som implementerar logik för omförsök: **ExponentialRetryPolicyFilter** och **LinearRetryPolicyFilter**. Följande kod skapar ett **ServiceBusService** -objekt som använder **ExponentialRetryPolicyFilter**:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Skapa prenumerationer
Ämnes prenumerationer skapas också med **ServiceBusService** -objektet. Prenumerationer namnges och kan ha ett valfritt filter som begränsar den uppsättning meddelanden som skickas till prenumerationens virtuella kö.

> [!NOTE]
> Som standard är prenumerationerna beständiga tills de eller de avsnitt som de är kopplade till tas bort. Om programmet innehåller logik för att skapa en prenumeration bör du först kontrol lera om prenumerationen finns med hjälp av metoden `getSubscription`.
>
> Du kan ta bort prenumerationerna automatiskt genom att ange [egenskapen AutoDeleteOnIdle](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Skapa en prenumeration med standardfiltret (MatchAll)
**MatchAll** -filtret är det standard filter som används när en prenumeration skapas. När du använder **MatchAll**-filtret kommer alla meddelanden som publiceras till ämnet att placeras i prenumerationens virtuella kö. I följande exempel skapas en prenumeration med namnet AllMessages som använder standard filtret för **MatchAll** .

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Skapa prenumerationer med filter
Du kan också skapa filter som gör att du kan ange vilka meddelanden som skickas till ett ämne som ska visas i en speciell ämnes prenumeration.

Den mest flexibla typen av filter som stöds av prenumerationer är **SqlFilter**, som implementerar en delmängd av SQL92. SQL-filter tillämpas på egenskaperna i de meddelanden som publiceras till ämnet. Mer information om de uttryck som kan användas med ett SQL-filter finns i syntaxen [SqlFilter. SqlExpression][SqlFilter.SqlExpression] .

Filter kan läggas till i en prenumeration med hjälp av metoden `createRule` för **ServiceBusService** -objektet. Med den här metoden kan du lägga till nya filter till en befintlig prenumeration.

> [!NOTE]
> Eftersom standard filtret används automatiskt för alla nya prenumerationer måste du först ta bort standard filtret eller så åsidosätter **MatchAll** eventuella andra filter som du kan ange. Du kan ta bort standard regeln med hjälp av metoden `deleteRule` för **ServiceBusService** -objektet.
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

På samma sätt skapar följande exempel en prenumeration med namnet `LowMessages` med en **SqlFilter** som endast väljer meddelanden som har en `messagenumber`-egenskap som är mindre än eller lika med 3:

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

När ett meddelande nu skickas till `MyTopic`levereras det till mottagare som prenumererar på `AllMessages` ämnes prenumerationen och är selektivt levererade till mottagare som prenumererar på `HighMessages` och `LowMessages` ämnes prenumerationer (beroende på meddelandets innehåll).

## <a name="how-to-send-messages-to-a-topic"></a>Så här skickar du meddelanden till ett ämne
Om du vill skicka ett meddelande till ett Service Bus ämne måste programmet använda metoden `sendTopicMessage` för **ServiceBusService** -objektet.
Meddelanden som skickas till Service Bus ämnen är **BrokeredMessage** -objekt.
**BrokeredMessage** -objekt har en uppsättning standard egenskaper (till exempel `Label` och `TimeToLive`), en ord lista som används för att lagra anpassade programspecifika egenskaper och en text i sträng data. Ett program kan ange meddelandets brödtext genom att skicka ett sträng värde till `sendTopicMessage` och alla nödvändiga standard egenskaper fylls med standardvärden.

Följande exempel visar hur du skickar fem test meddelanden till `MyTopic`. Egenskap svärdet `messagenumber` för varje meddelande varierar beroende på loopen (den här egenskapen avgör vilka prenumerationer som får det):

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

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som lagras i ett ämne, men det finns en gräns för den totala storleken på de meddelanden som innehas av ett ämne. Den här ämnesstorleken definieras när ämnet skapas, med en övre gräns på 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
Meddelanden tas emot från en prenumeration med hjälp av metoden `receiveSubscriptionMessage` på **ServiceBusService** -objektet. Som standard tas meddelanden bort från prenumerationen när de läses. Du kan dock ange den valfria parametern `isPeekLock` till **True** för att läsa (PEEK) och låsa meddelandet utan att ta bort det från prenumerationen.

Standard beteendet för att läsa och ta bort meddelandet som en del av Receive-åtgärden är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera att inte bearbeta ett meddelande när det uppstår ett fel. För att förstå det här beteendet bör du överväga ett scenario där klienten utfärdar Receive-begäran och sedan kraschar innan den bearbetas. Eftersom Service Bus har markerat meddelandet som förbrukat, när programmet startas om och börjar förbruka meddelanden igen, har det fått meddelandet som förbrukades innan kraschen.

Om `isPeekLock`-parametern har angetts till **True**blir mottagningen en åtgärd i två steg, vilket gör det möjligt att stödja program som inte kan tolerera missade meddelanden. När Service Bus tar emot en begäran hittar den nästa meddelande som ska förbrukas, låser det för att hindra andra användare från att ta emot det och returnerar det till programmet.
När programmet bearbetar meddelandet (eller lagrar det tillförlitligt för framtida bearbetning) slutförs det andra steget i Receive-processen genom att anropa metoden **deleteMessage** och skickar meddelandet som ska tas bort som en parameter. **DeleteMessage** -metoden markerar meddelandet som förbrukat och tar bort det från prenumerationen.

Följande exempel visar hur meddelanden kan tas emot och bearbetas med hjälp av `receiveSubscriptionMessage`. Exemplet tar först emot och tar bort ett meddelande från prenumerationen "LowMessages" och tar emot ett meddelande från "HighMessages"-prenumerationen med `isPeekLock` ange True. Den tar sedan bort meddelandet med hjälp av `deleteMessage`:

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
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagar program inte kan bearbeta meddelandet av någon anledning, kan det anropa metoden `unlockMessage` på **ServiceBusService** -objektet. Den här metoden gör att Service Bus låser upp meddelandet i prenumerationen och gör det tillgängligt för att tas emot igen. I den här instansen, antingen av samma användnings program eller ett annat användnings program.

Det finns också en tids gräns som är kopplad till ett meddelande som är låst i prenumerationen. Om programmet inte kan bearbeta meddelandet innan tids gränsen för låsning går ut (till exempel om programmet kraschar) låser Service Bus automatiskt upp meddelandet och gör det tillgängligt för att tas emot igen.

I händelse av att programmet kraschar när meddelandet har bearbetats men innan `deleteMessage`-metoden anropas, kommer meddelandet att skickas vidare till programmet när det startas om. Det här beteendet kallas ofta *minst en gång*. Det vill säga att varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras igen. Om scenariot inte kan tolerera dubbel bearbetning bör du lägga till logik till ditt program för att hantera duplicerad meddelande leverans. Du kan använda meddelandets **messageid** -egenskap, som är konstant över leverans försök.

## <a name="delete-topics-and-subscriptions"></a>Ta bort ämnen och prenumerationer
Ämnen och prenumerationer är permanenta om inte [autoDeleteOnIdle-egenskapen](https://docs.microsoft.com/javascript/api/@azure/arm-servicebus/sbsubscription?view=azure-node-latest#autodeleteonidle) har angetts och måste uttryckligen tas bort antingen via [Azure Portal][Azure portal] eller program mässigt.
Följande exempel visar hur du tar bort ämnet med namnet `MyTopic`:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Om du tar bort ett ämne så tar du även bort alla prenumerationer som är registrerade på det ämnet. Prenumerationer kan även tas bort separat. I följande exempel visas hur du tar bort en prenumeration med namnet `HighMessages` från avsnittet `MyTopic`:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer gör det möjligt för användare att ansluta till en Service Bus namnrymd och administrera meddelande enheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjlighet att testa ämnen, köer, prenumerationer, relä tjänster, Notification Hub och Event Hub. 

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Service Bus ämnen kan du följa dessa länkar om du vill veta mer.

* Se [köer, ämnen och prenumerationer][Queues, topics, and subscriptions].
* API-referens för [SqlFilter][SqlFilter].
* Besök [Azure SDK för Node][Azure SDK for Node] -lagringsplatsen på GitHub.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /javascript/api/@azure/arm-servicebus/sqlfilter?view=azure-node-latest
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Skapa och distribuera ett Node. js-program till en Azure-webbplats]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

