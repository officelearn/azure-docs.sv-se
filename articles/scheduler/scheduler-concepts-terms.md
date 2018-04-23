---
title: Scheduler - begrepp, terminologi och enheter | Microsoft-dokument
description: Begrepp, terminologi och entitetshierarki, inklusive jobb och jobbsamlingar, relaterade till Azure Scheduler.  Innehåller ett omfattande exempel på ett schemalagt jobb.
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 3ef16fab-d18a-48ba-8e56-3f3e0a1bcb92
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 91302d57c43a6c9d14aeeee95df3d61fa6f73172
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="scheduler-concepts-terminology--entity-hierarchy"></a>Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler
## <a name="scheduler-entity-hierarchy"></a>Entitetshierarki i Scheduler
I följande tabell beskrivs de viktigaste resurserna som exponeras eller används av Scheduler-API:et:

| Resurs | Beskrivning |
| --- | --- |
| **Jobbsamling** |En jobbsamling innehåller en grupp med jobb och hanterar inställningar, kvoter och begränsningar som delas av jobb i samlingen. En jobbsamling skapas av en prenumerationsägare och grupperar jobb baserat på användnings- eller programgränser. Den är begränsad till en region. Den gör också att du kan tillämpa kvoter för att begränsa användningen av alla jobb i samlingen. Exempel på kvoter är MaxJobs och MaxRecurrence. |
| **Jobb** |Ett jobb definierar en enskild återkommande åtgärd med enkla eller komplexa strategier för körning. Exempel på åtgärder är HTTP, lagringskö, Service Bus-kö eller Service Bus-ämnesförfrågningar. |
| **Jobbhistorik** |En jobbhistorik representerar information om körningen av ett jobb. Den innehåller information om huruvida jobbet lyckades eller misslyckades, samt eventuell svarsinformation. |

## <a name="scheduler-entity-management"></a>Entitetshantering i Scheduler
På hög nivå exponerar Scheduler och tjänsthanterings-API:et följande åtgärder för resurserna:

| Funktion | Beskrivning och URI-adress |
| --- | --- |
| **Hantering av jobbsamlingar** |Stöd för GET, PUT och DELETE för att skapa och ändra jobbsamlingar och jobben som de innehåller. En jobbsamling är en behållare för jobb och mappar till kvoter och delade inställningar. Exempel på kvoter, som beskrivs senare, är högsta antal jobb och minsta intervall. <p>PUT och DELETE: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>GET: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p> |
| **Jobbhantering** |Stöd för GET, PUT, POST, PATCH och DELETE för att skapa och ändra jobb. Alla jobb måste tillhöra en jobbsamling som redan finns. Ingen implicit generering utförs. <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p> |
| **Hantering av jobbhistorik** |Stöd för GET för att hämta 60 dagars jobbkörningshistorik, t.ex. förfluten tid och körningsresultat. Lägger till parameterstöd för frågesträngar för filtrering baserat på tillstånd och status. <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p> |

## <a name="job-types"></a>Jobbtyper
Det finns flera typer av jobb: HTTP-jobb (inklusive HTTPS-jobb som stöder SSL), jobb för lagringsköer, Service Bus-köjobb och Service Bus-ämnesjobb. HTTP-jobben är idealiska om du har en slutpunkt för en befintlig arbetsbelastning eller tjänst. Du kan använda köjobb för att publicera meddelanden till lagringsköer, vilket gör dessa jobb idealiska för arbetsbelastningar som använder lagringsköer. På liknande sätt är Service Bus-jobb idealiska för arbetsbelastningar som använder Service Bus-köer och Service Bus-ämnen.

## <a name="the-job-entity-in-detail"></a>Jobbentiteten i detalj
På en grundläggande nivå har ett schemalagt jobb flera delar:

* Åtgärden som ska utföras när jobbet utlöses  
* (Valfritt) Tiden då jobbet ska köras  
* (Valfritt) När och hur ofta jobbet ska upprepas  
* (Valfritt) En åtgärd som ska utlösas om den primära åtgärden misslyckas  

Internt innehåller ett schemalagt jobb även systembaserade data, till exempel nästa schemalagda körningstid.

Följande kod är ett komplett exempel på ett schemalagt jobb. Information finns i de efterföljande avsnitten.

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

Som du ser i det schemalagda exempeljobbet ovan har en jobbdefinition flera delar:

* Starttid (”startTime”)  
* Åtgärd (”action”), som även omfattar en felåtgärd (”errorAction”)
* Upprepning (”recurrence”)  
* Tillstånd (”state”)  
* Status (”status”)  
* Återförsöksprincip (”retryPolicy”)  

Nu ska vi titta närmare på var och en av dessa delar:

## <a name="starttime"></a>startTime
”startTime” är starttiden och låter anroparen ange en tidszonsförskjutning för anslutningen i [ISO 8601-format](http://en.wikipedia.org/wiki/ISO_8601).

## <a name="action-and-erroraction"></a>action och errorAction
”action” är den åtgärd som anropas för varje förekomst och beskriver en typ av tjänstanrop. Åtgärden är den handling som utförs enligt det angivna schemat. Scheduler stöder HTTP, lagringskö, Service Bus-ämne och Service Bus-köåtgärder.

Åtgärden i exemplet ovan är en HTTP-åtgärd. Nedan är ett exempel på en åtgärd för lagringskön:

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

Nedan är ett exempel på en Service Bus-ämnesåtgärd.

  "action": { "type": "serviceBusTopic", "serviceBusTopicMessage": { "topicPath": "t1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Kan vara antingen netMessaging eller AMQP "authentication": { "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message", "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, }

Nedan är ett exempel på en Service Bus-köåtgärd:

  "action": { "serviceBusQueueMessage": { "queueName": "q1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Kan vara antingen netMessaging eller AMQP "authentication": {  
        "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message",  
      "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, "type": "serviceBusQueue" }

”errorAction” är felhanteraren, dvs. åtgärden som anropas när den primära åtgärden misslyckas. Du kan använda den här variabeln för att anropa en slutpunkt för felhantering eller skicka ett meddelande till användaren. Detta kan användas för att ansluta till en sekundär slutpunkt om den primära inte är tillgänglig (t.ex. i händelse av fel på platsen för slutpunkten) eller för att avisera en slutpunkt för felhantering. Precis som den primära åtgärden kan felåtgärden ha enkel eller sammansatt logik baserat på andra åtgärder. Mer information om hur du skapar en SAS-token finns i [Skapa och använda en signatur för delad åtkomst](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="recurrence"></a>recurrence
Upprepningskomponenten har flera delar:

* Frekvens: Minut, timme, dag, vecka, månad eller år.  
* Intervall: Intervallet för den angivna frekvensen för upprepningen.  
* Föreskrivet schema: Minuter, timmar, veckodagar, månader och dagar i månaden för upprepningen.  
* Antal: Antal förekomster.  
* Sluttid: Inga jobb körs efter den angivna sluttiden.  

Ett jobb är återkommande om ett återkommande objekt har angetts i jobbets JSON-definition. Om både count och endTime anges tillämpas slutföranderegeln som inträffar först.

## <a name="state"></a>state
Jobbets tillstånd representeras av något av fyra värden: enabled (aktiverat), disabled (inaktiverat), completed (slutfört) eller faulted (har fel). Du kan använda PUT eller PATCH om du vill uppdatera jobbets tillstånd till enabled eller disabled. Om ett jobb har tillståndet completed eller faulted så är det ett slutgiltigt tillstånd som inte kan uppdateras (även om jobbet fortfarande kan tas bort). Ett exempel på tillståndsegenskapen är följande:

        "state": "disabled", // enabled, disabled, completed, or faulted
Jobb med tillståndet completed eller faulted tas bort efter 60 dagar.

## <a name="status"></a>status
När ett Scheduler-jobb har startat returneras information om jobbets aktuella status. Det här objektet kan inte anges av användaren – det definieras av systemet. Det är dock en del av jobbobjektet (i stället för en separat länkad resurs) så att det är lätt att hämta statusen för ett jobb.

Jobbstatusen innehåller tiden för den föregående körningen (i förekommande fall), tiden för nästa schemalagda körning (för pågående jobb) och antalet körningar av jobbet.

## <a name="retrypolicy"></a>retryPolicy
Om ett Scheduler-jobb kan du definiera en återförsöksprincip för att ange om och hur nya åtgärdsförsök ska göras. Detta definieras av **retryType**-objektet – det är inställt på **none** om det inte finns någon återförsöksprincip (se ovan). Ändra inställningen till **fixed** om det finns en återförsöksprincip.

Om du vill ange en återförsöksprincip kan du ange ytterligare två inställningar: ett återförsöksintervall (**retryInterval**) och antalet återförsök (**retryCount**).

Återförsöksintervall, som anges med **retryInterval**-objektet, är intervallet mellan återförsöken. Standardvärdet är 30 sekunder. Det minsta konfigurerbara värdet är 15 sekunder och det högsta värdet är 18 månader. Det definieras i ISO 8601-formatet. På liknande sätt anges värdet för antalet återförsök med objektet **retryCount**, som representerar det antal gånger som ett nytt försök görs. Standardvärdet är 4 och maxvärdet är 20\. Både **retryInterval** och **retryCount** är valfria. De tilldelas sina standardvärden om **retryType** har angetts till **fixed** och inga värden anges.

## <a name="see-also"></a>Se även
 [Vad är Scheduler?](scheduler-intro.md)

 [Komma igång med Scheduler på Azure-portalen](scheduler-get-started-portal.md)

 [Prenumerationer och fakturering i Azure Scheduler](scheduler-plans-billing.md)

 [Skapa komplexa scheman och avancerad upprepning med Azure Scheduler](scheduler-advanced-complexity.md)

 [Referens för REST-API:et för Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)

 [Hög tillgänglighet och tillförlitlighet i Azure Scheduler](scheduler-high-availability-reliability.md)

 [Gränser, standardinställningar och felkoder i Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Utgående autentisering i Azure Scheduler](scheduler-outbound-authentication.md)

