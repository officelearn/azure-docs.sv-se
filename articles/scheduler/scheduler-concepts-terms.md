---
title: Begrepp, terminologi och entiteter – Azure Scheduler | Microsoft Docs
description: Läs om begrepp, terminologi och entitetshierarki inklusive jobb och jobbsamlingar i Azure Scheduler
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 3ef16fab-d18a-48ba-8e56-3f3e0a1bcb92
ms.topic: get-started-article
ms.date: 08/18/2016
ms.openlocfilehash: 5ed15a58e5b709b003e9f45d04c3654f814aefc7
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334235"
---
# <a name="concepts-terminology-and-entities-in-azure-scheduler"></a>Begrepp, terminologi och entiteter i Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras tillbaka. Om du vill schemalägga jobb kan du [testa Azure Logic Apps istället](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="entity-hierarchy"></a>Entitetshierarki

Azure Scheduler REST API:et visar och använder de här huvudsakliga entiteterna eller resurserna:

| Entitet | Beskrivning |
|--------|-------------|
| **Jobb** | Definierar en enskild återkommande åtgärd med enkla eller komplexa strategier för körning. Exempel på åtgärder kan vara HTTP, Storage-kö, Service Bus-kö eller Service Bus-ämnesbegäranden. | 
| **Jobbsamling** | Innehåller en grupp med jobb och hanterar inställningar, kvoter och begränsningar som delas av jobb i samlingen. Som en Azure-prenumerationsägare kan du skapa jobbsamlingar och gruppera jobb tillsammans baserat på deras användning eller programgränser. En jobbsamling har dessa attribut: <p>– begränsad till en region. <br>– låter dig framtvinga kvoter så att du kan begränsa användningen för alla jobb i en samling. <br>– kvoter inkluderar MaxJobs och MaxRecurrence. | 
| **Jobbhistorik** | Innehåller information om en jobbkörning, till exempel status och eventuell svarsinformation. |
||| 

## <a name="entity-management"></a>Entitetshantering

Vid en hög nivå exponerar Scheduler REST API de här åtgärderna för hantering av entiteter.

### <a name="job-management"></a>Jobbhantering

Stöder åtgärder för att skapa och redigera jobb. Alla jobb måste tillhöra en befintlig jobbsamling. Ingen implicit generering utförs. Mer information finns i [Scheduler REST API – jobb](https://docs.microsoft.com/rest/api/scheduler/jobs). Här är URI-adressen för de här åtgärderna:

`https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`

### <a name="job-collection-management"></a>Hantering av jobbsamlingar

Stöder åtgärder för att skapa och redigera jobb och jobbsamlingar, som mappar till kvoter och delade inställningar. Till exempel kvoter som anger maximalt antal jobb och minsta upprepningsintervall. Mer information finns i [Scheduler REST API – jobbsamlingar](https://docs.microsoft.com/rest/api/scheduler/jobcollections). Här är URI-adressen för de här åtgärderna:

`https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`

### <a name="job-history-management"></a>Hantering av jobbhistorik

Stöder GET-åtgärden för att hämta 60 dagars jobbkörningshistorik, till exempel förfluten tid och jobbkörningsresultat. Inkluderar parameterstöd för frågesträngar för filtrering baserat på tillstånd och status. Mer information finns i [Scheduler REST API – Jobb – Lista jobbhistorik](https://docs.microsoft.com/rest/api/scheduler/jobs/listjobhistory). Här är URI-adressen för de här åtgärderna:

`https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`

## <a name="job-types"></a>Jobbtyper

Azure Scheduler stöder flera jobbtyper: 

* HTTP-jobb, inklusive HTTPS-jobb som stöder SSL, för när du har slutpunkten för en befintlig tjänst eller arbetsbelastning
* Storage-köjobb för arbetsbelastningar som använder Storage-köer, som att publicera meddelanden till Storage-köer
* Service Bus-köjobb för arbetsbelastningar som använder Service Bus-köer
* Service Bus-ämnesjobb för arbetsbelastningar som använder Service Bus-ämnen

## <a name="job-definition"></a>Jobbdefinition

På hög nivå har ett Scheduler-jobb de här grundläggande delarna:

* Åtgärden som körs när jobbets timer utlöses
* Valfritt: tiden då jobbet ska köras
* Valfritt: när och hur ofta jobbet ska upprepas
* Valfritt: En felåtgärd som körs om den primära åtgärden misslyckas

Jobbet innehåller även systemangivna data, till exempel jobbets nästa schemalagda körning. Jobbets koddefinition är ett objekt i JavaScript Object Notation (JSON)-format, vilket har dessa element:

| Element | Krävs | Beskrivning | 
|---------|----------|-------------| 
| [**startTime**](#start-time) | Nej | Starttid för jobbet med en tidszonsförskjutning i [ISO 8601-format](http://en.wikipedia.org/wiki/ISO_8601) | 
| [**åtgärd**](#action) | Ja | Information om den primära åtgärden, vilket kan inkludera ett **errorAction**-objekt | 
| [**errorAction**](#error-action) | Nej | Information om den sekundära åtgärd som körs om den primära åtgärden misslyckas |
| [**recurrence**](#recurrence) | Nej | Information som frekvens och intervall för ett återkommande jobb | 
| [**retryPolicy**](#retry-policy) | Nej | Information om hur ofta en åtgärd ska göras om | 
| [**state**](#state) | Ja | Information om jobbets aktuella tillstånd |
| [**status**](#status) | Ja | Information om jobbets aktuella status som kontrolleras av tjänsten |
||||

Här är ett exempel som visar en heltäckande jobbdefinition för en HTTP-åtgärd med mer fullständig elementinformation som beskrivs i senare avsnitt: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z",
   "action": {
      "type": "Http",
      "request": {
         "uri": "http://contoso.com/some-method", 
         "method": "PUT",          
         "body": "Posting from a timer",
         "headers": {
            "Content-Type": "application/json"
         },
         "retryPolicy": { 
             "retryType": "None" 
         },
      },
      "errorAction": {
         "type": "Http",
         "request": {
            "uri": "http://contoso.com/notifyError",
            "method": "POST"
         }
      }
   },
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]
      },
      "count": 10,
      "endTime": "2012-11-04"
   },
   "state": "Disabled",
   "status": {
      "lastExecutionTime": "2007-03-01T13:00:00Z",
      "nextExecutionTime": "2007-03-01T14:00:00Z ",
      "executionCount": 3,
      "failureCount": 0,
      "faultedCount": 0
   }
}
```

<a name="start-time"></a>

## <a name="starttime"></a>startTime

I objektet **startTime** kan du ange starttid och en tidszonsförskjutning i [ISO 8601-format](http://en.wikipedia.org/wiki/ISO_8601).

<a name="action"></a>

## <a name="action"></a>åtgärd

Ditt Scheduler-jobb kör en primär **åtgärd** baserat på det angivna schemat. Scheduler stöder HTTP, Storage-kö, Service Bus-kö och Service Bus-ämnesåtgärder. Om den primära **åtgärden** misslyckas, kan Scheduler köra en sekundär [**errorAction**](#errorAction) som hanterar felet. Objektet **åtgärd** beskriver dessa element:

* Åtgärdens tjänsttyp
* Åtgärdens information
* Ett alternativ **errorAction**

Föregående exempel beskriver en HTTP-åtgärd. Här är ett exempel på en Storage-köåtgärd:

```json
"action": {
   "type": "storageQueue",
   "queueMessage": {
      "storageAccount": "myStorageAccount",  
      "queueName": "myqueue",                
      "sasToken": "TOKEN",                   
      "message": "My message body"
    }
}
```

Här är ett exempel på en Service Bus-köåtgärd:

```json
"action": {
   "type": "serviceBusQueue",
   "serviceBusQueueMessage": {
      "queueName": "q1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {  
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",  
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Här är ett exempel på en Service Bus-ämnesåtgärd:

```json
"action": {
   "type": "serviceBusTopic",
   "serviceBusTopicMessage": {
      "topicPath": "t1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Mer information om SAS-tokens (signaturer för delad åtkomst) finns i [auktorisera med signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

<a name="error-action"></a>

## <a name="erroraction"></a>errorAction

Om ditt jobbs primära **åtgärd** misslyckas, kan Scheduler köra en **errorAction** som hanterar felet. I den primära **åtgärden** kan du ange ett **errorAction** objekt så att Scheduler kan anropa en slutpunkt för felhantering eller skicka ett användarmeddelande. 

Om en katastrof till exempel inträffar på den primära slutpunkten så kan du använda **errorAction** för att anropa en sekundär slutpunkt eller för att meddela en slutpunkt för felhantering. 

Precis som den primärra **åtgärden** så kan du låta felåtgärden använda enkel eller sammansatt logik baserat på andra åtgärder. 

<a name="recurrence"></a>

## <a name="recurrence"></a>recurrence

Ett jobb återkommer om jobbets JSON-definition innehåller objektet **upprepning**, till exempel:

```json
"recurrence": {
   "frequency": "Week",
   "interval": 1,
   "schedule": {
      "hours": [10, 22],
      "minutes": [0, 30],
      "weekDays": ["Monday", "Wednesday", "Friday"]
   },
   "count": 10,
   "endTime": "2012-11-04"
},
```

| Egenskap | Krävs | Värde | Beskrivning | 
|----------|----------|-------|-------------| 
| **frequency** | Ja, när **upprepning** används | Minut, timme, dag, vecka, månad, år | Tidsenheten mellan förekomster | 
| **interval** | Nej | 1 till och med 1 000 | Ett positivt heltal som anger antalet tidsenheter mellan varje förekomst utifrån **frekvens** | 
| **schedule** | Nej | Varierar | Information för mer komplicerade och avancerade scheman. Se **timmar**, **minuter**, **weekDays**, **månader** och **monthDays** | 
| **hours** | Nej | 1 till 24 | En matris med timmesmarkeringarrna för när jobbet ska köras | 
| **minutes** | Nej | 1 till 24 | En matris med minutmarkeringarrna för när jobbet ska köras | 
| **månader** | Nej | 1 till 12 | En matris med månaderna då jobbet ska köras | 
| **monthDays** | Nej | Varierar | En matris med dagarna i månaden då jobbet ska köras | 
| **weekDays** | Nej | Måndag, tisdag, onsdag, torsdag, fredag, lördag och söndag | En matris med veckodagarna när jobbet ska köras | 
| **antal** | Nej | <*ingen*> | Antal upprepningar. Standardvärdet är oändlig upprepning. Du kan inte använda både **antal** och **endTime** men regeln som slutar först gäller. | 
| **endTime** | Nej | <*ingen*> | Datum och tid när du vill stoppa upprepningen. Standardvärdet är oändlig upprepning. Du kan inte använda både **antal** och **endTime** men regeln som slutar först gäller. | 
||||

Mer information om dessa element finns i [Skapa komplexa scheman och avancerad upprepningar](../scheduler/scheduler-advanced-complexity.md).

<a name="retry-policy"></a>

## <a name="retrypolicy"></a>retryPolicy

För fallet när ett Scheduler-jobb misslyckas, kan du ställa in en återförsöksprincip som avgör om och hur Scheduler försöker åtgärden igen. Som standard försöker Scheduler köra om jobbet fyra gånger med 30 sekunders mellanrum. Du kan göra den här principen mer eller mindre aggressiv, till exempel försöker den här principen en åtgärd två gånger per dag:

```json
"retryPolicy": { 
   "retryType": "Fixed",
   "retryInterval": "PT1D",
   "retryCount": 2
},
```

| Egenskap | Krävs | Värde | Beskrivning | 
|----------|----------|-------|-------------| 
| **retryType** | Ja | **Fast**, **Ingen** | Avgör om du anger en återförsöksprincip (**fast**) eller inte (**ingen**). | 
| **retryInterval** | Nej | PT30S | Anger intervall och frekvens mellan omförsök i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Minimivärdet är 15 sekunder medan det högsta värdet är 18 månader. | 
| **retryCount** | Nej | 4 | Anger antalet återförsök. Maxvärdet är 20. | 
||||

Mer information finns i [Hög tillgänglighet och tillförlitlighet](../scheduler/scheduler-high-availability-reliability.md).

<a name="status"></a>

## <a name="state"></a>state

Ett jobbs tillstånd är antingen **aktiverat**, **inaktiverat**, **slutfört** eller **fel**, till exempel: 

`"state": "Disabled"`

Ändra jobb till **aktiverat** eller **inaktiverat** tillstånd genom att använda PUT- eller PATCH-åtgärden på de jobben.
Men om ett jobb har tillståndet **slutfört** eller **fel** så kan du inte uppdatera tillståndet, även om du kan utföra DELETE-åtgärden på jobbet. Scheduler tar bort slutförda och feljobb efter 60 dagar. 

<a name="status"></a>

## <a name="status"></a>status

När ett jobb startar så returnerar Scheduler information om jobbets status med hjälp av **status**-objektet, som bara Scheduler styr. Du kan dock hitta **status**-objektet i **jobb**objektet. Här är den information som ett jobbs status innehåller:

* Tid för den föregående körningen, om någon
* Tid för nästa schemalagda körning för pågående jobb
* Antalet jobbkörningar
* Antal misslyckanden om några
* Antal fel om några

Exempel:

```json
"status": {
   "lastExecutionTime": "2007-03-01T13:00:00Z",
   "nextExecutionTime": "2007-03-01T14:00:00Z ",
   "executionCount": 3,
   "failureCount": 0,
   "faultedCount": 0
}
```

## <a name="see-also"></a>Se även

* [Vad är Azure Scheduler?](scheduler-intro.md)
* [Begrepp, terminologi och entitetshierarki](scheduler-concepts-terms.md)
* [Skapa komplexa scheman och avancerad upprepning](scheduler-advanced-complexity.md)
* [Gränser, kvoter, standardvärden och felkoder](scheduler-limits-defaults-errors.md)
* [Referens för REST-API:et för Azure Scheduler](/rest/api/scheduler)
* [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)
