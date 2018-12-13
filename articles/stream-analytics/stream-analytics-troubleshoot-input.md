---
title: Felsökning av indata för Azure Stream Analytics
description: Den här artikeln beskrivs metoder för att felsöka inkommande anslutningar i Azure Stream Analytics-jobb.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 6694865909a165842f994501befa404e1bc0a447
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164389"
---
# <a name="troubleshoot-input-connections"></a>Felsök inkommande anslutningar

Den här sidan beskriver vanliga problem med inkommande anslutningar och hur du felsöker dem.

## <a name="input-events-not-received-by-job"></a>Inkommande händelser som inte tagits emot av jobb 
1.  Testa anslutningen. Kontrollera anslutningen till in- och utdata med hjälp av den **Testanslutningen** knappen för varje indata och utdata.

2.  Granska dina indata.

    Använd för att kontrollera att data flödar till Event Hub, [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) att ansluta till Azure Event Hub (om Händelsehubben indata används).
        
    Använd den [ **exempeldata** ](stream-analytics-sample-data-input.md) för varje indata och ladda ned exempeldata för ingången.
        
    Gå igenom dina exempeldata för att förstå formen: schemat och [datatyper](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="malformed-input-events-causes-deserialization-errors"></a>Fel vid deserialisering av felaktig inmatningshändelser orsaker 
Deserialisering problem orsakas när Indataströmmen för Stream Analytics-jobbet innehåller felaktig meddelanden. Ett felaktigt meddelande kan exempelvis ha orsakats av en parentes saknas eller en klammerparentes i JSON-objektet, eller en felaktig tidstämpelformatet i fältet. 
 
När ett Stream Analytics-jobb tar emot ett felaktigt meddelande från indata, ignoreras meddelandet och meddelar dig med en varning. En varningssymbol visas på den **indata** panelen för ditt Stream Analytics-jobb. Den här varningen logga finns så länge som jobbet är i körningstillstånd:

![Azure Stream Analytics-indata panelen](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Aktivera diagnostikloggar att visa information om varningen. Felaktig inmatningshändelser innehålla loggarna för jobbkörning en post med meddelandet som ser ut som: 
<code>Could not deserialize the input event(s) from resource <blob URI> as json.</code>

### <a name="what-caused-the-deserialization-error"></a>Vad som orsakade felet deserialisering
Du kan vidta följande steg för att analysera de inkommande händelserna i detalj för att få en förståelse för vad som orsakade felet deserialisering. Du kan sedan åtgärda händelsekällan för att generera händelser i rätt format så att du inte stöter på problemet igen.

1. Gå till panelen indata och klicka på varningen symboler vill se en lista över problem.

2. Indatainformation panelen visar en lista över varningar med information om olika problemen. Varningsmeddelandet exemplet nedan innehåller partition, förskjutning och sekvensnummer där det finns felaktiga JSON-data. 

   ![Stream Analytics varningsmeddelandet visas med förskjutning](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. Om du vill söka efter JSON-data med felaktigt format, kör du CheckMalformedEvents.cs kod i den [GitHub-lagringsplats med exempel](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Den här koden läsningar partitions-ID, förskjutning och skriver ut de data som finns i förskjutningen. 

4. När du läser dessa data kan du sedan analysera och korrigera serialiseringsformatet.

5. Du kan också [läsa händelser från en IoT-hubb med Service Bus Explorer](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Jobb överskrider högsta Händelsehubbmottagare
Bästa praxis för att använda Event Hubs är att använda flera konsumentgrupper för att se till att jobbet skalbarhet. Antalet läsare i Stream Analytics-jobb för en specifik indata påverkar antalet läsare i en enskild konsument-grupp. Det exakta antalet mottagare baseras på interna implementeringsdetaljer för skalbar topologi logiken och exponeras inte externt. Antalet läsare kan ändra när ett jobb startas eller uppgradering av jobbet.

Felet som visas när antalet mottagare överskrider maximalt är: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> När antalet läsare ändras vid en uppgradering av jobb, skrivs tillfälliga varningar till granskningsloggar. Stream Analytics-jobb återställa automatiskt från dessa tillfälliga problem.

### <a name="add-a-consumer-group-in-event-hubs"></a>Lägg till en konsumentgrupp i Event Hubs
Följ dessa steg för att lägga till en ny konsumentgrupp i Event Hubs-instans:

1. Logga in på Azure Portal.

2. Leta upp dina Event Hubs.

3. Välj **Händelsehubbar** under den **entiteter** rubrik.

4. Välj Event Hub efter namn.

5. På den **Event Hubs-instans** sidan under den **entiteter** väljer **konsumentgrupper**. En konsumentgrupp med namnet **$Default** visas.

6. Välj **+ konsumentgrupp** att lägga till en ny konsumentgrupp. 

   ![Lägg till en konsumentgrupp i Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. När du har skapat indata i Stream Analytics-jobbet så att den pekar till Händelsehubben kan angett du det konsumentgruppen. $Default används när inget har angetts. När du skapar en ny konsumentgrupp kan redigera Event Hub-indata i Stream Analytics-jobb och ange namnet på den nya konsumentgruppen.


## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Läsare per partition överskrider gränsen för Event Hubs

Om strömmande frågesyntaxen hänvisar till samma inkommande Event Hub-resurs flera gånger, kan jobb-motorn använda flera läsare per fråga från samma konsumentgruppen. När det finns för många referenser till samma konsumentgruppen, jobbet kan överskrida gränsen på fem och genereras ett fel. I dessa fall kan du ytterligare dividera med hjälp av flera inmatningar över flera konsumentgrupper med hjälp av lösningen som beskrivs i följande avsnitt. 

Scenarier där antalet läsare per partition är längre än Händelsehubbar fem är följande:

* Flera SELECT-satser: Om du använder flera SELECT-satser som refererar till **samma** händelsehubb indata, gör en ny mottagare som ska skapas för varje SELECT-instruktion.
* UNION: När du använder en UNION, det är möjligt att ha flera inmatningar som refererar till den **samma** event hub och konsument-grupp.
* SJÄLVKOPPLING: När du använder en SJÄLVSIGNERAT JOIN-åtgärd, det är möjligt att referera till den **samma** händelsehubb flera gånger.

Följande metoder kan minimera scenarier där antalet läsare per partition är längre än Händelsehubbar fem.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Dela upp din fråga i flera steg med hjälp av en WITH-satsen

WITH-satsen anger en tillfällig namngivna resultatuppsättning som kan refereras av en FROM-sats i frågan. Du definierar WITH-satsen i omfånget för körning av en enda SELECT-instruktion.

Till exempel i stället för den här frågan:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Använd den här frågan:

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Kontrollera att indata binda till olika konsumentgrupper

Skapa separata konsumentgrupper för frågor som tre eller flera inmatningar är anslutna till samma konsumentgrupp för Event Hubs. Detta kräver skapandet av ytterligare indata för Stream Analytics.

## <a name="get-help"></a>Få hjälp

För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
