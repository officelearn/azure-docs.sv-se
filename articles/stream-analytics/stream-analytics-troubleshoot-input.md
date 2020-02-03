---
title: Felsökning av indata för Azure Stream Analytics
description: Den här artikeln beskrivs metoder för att felsöka inkommande anslutningar i Azure Stream Analytics-jobb.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: dac3037f82c38980c9ac16685aa7fddac68a2e7b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720307"
---
# <a name="troubleshoot-input-connections"></a>Felsök inkommande anslutningar

Den här sidan beskriver vanliga problem med inkommande anslutningar och hur du felsöker dem.

## <a name="input-events-not-received-by-job"></a>Inkommande händelser som inte tagits emot av jobb 
1.  Testa anslutningen. Kontrol lera anslutningen till indata och utdata genom att använda knappen **Testa anslutning** för varje indata och utdata.

2.  Granska dina indata.

    1. Du kan kontrol lera att indata flödar till Händelsehubben genom att använda [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) för att ansluta till Azure Event Hub (om indata för Event Hub används).
        
    1. Använd [**exempel data**](stream-analytics-sample-data-input.md) knappen för varje inmatning. Hämta exempel data för indata.
        
    1. Granska exempel data för att förstå formen på data – det vill säga schema-och [data typerna](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics).

3.  Se till att du har valt ett tidsintervall i förhands granskningen. Välj **Välj tidsintervall**och ange sedan en tids längd innan du testar frågan.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Fel vid deserialisering av felaktig inmatningshändelser orsaker 
Deserialisering problem orsakas när Indataströmmen för Stream Analytics-jobbet innehåller felaktig meddelanden. Ett felaktigt meddelande kan exempelvis ha orsakats av en parentes saknas eller en klammerparentes i JSON-objektet, eller en felaktig tidstämpelformatet i fältet. 
 
När ett Stream Analytics-jobb tar emot ett felaktigt meddelande från indata, ignoreras meddelandet och meddelar dig med en varning. En varnings symbol visas på panelen **inmatningar** i ditt Stream Analytics jobb. Den här varningen logga finns så länge som jobbet är i körningstillstånd:

![Azure Stream Analytics-indata panelen](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Aktivera diagnostikloggar att visa information om varningen. Felaktig inmatningshändelser innehålla loggarna för jobbkörning en post med meddelandet som ser ut som: 
```
Could not deserialize the input event(s) from resource <blob URI> as json.
```

### <a name="what-caused-the-deserialization-error"></a>Vad som orsakade felet deserialisering
Du kan vidta följande steg för att analysera de inkommande händelserna i detalj för att få en förståelse för vad som orsakade felet deserialisering. Du kan sedan åtgärda händelsekällan för att generera händelser i rätt format så att du inte stöter på problemet igen.

1. Gå till panelen indata och klicka på varningen symboler vill se en lista över problem.

2. Indatainformation panelen visar en lista över varningar med information om olika problemen. Varningsmeddelandet exemplet nedan innehåller partition, förskjutning och sekvensnummer där det finns felaktiga JSON-data. 

   ![Stream Analytics varnings meddelande med förskjutning](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. Om du vill hitta JSON-data med fel format kör du CheckMalformedEvents.cs-koden som finns i [lagrings platsen för GitHub-exempel](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Den här koden läsningar partitions-ID, förskjutning och skriver ut de data som finns i förskjutningen. 

4. När du läser dessa data kan du sedan analysera och korrigera serialiseringsformatet.

5. Du kan också [läsa händelser från en IoT Hub med Service Bus Explorer](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Jobb överskrider högsta Händelsehubbmottagare
Bästa praxis för att använda Event Hubs är att använda flera konsumentgrupper för att se till att jobbet skalbarhet. Antalet läsare i Stream Analytics-jobb för en specifik indata påverkar antalet läsare i en enskild konsument-grupp. Det exakta antalet mottagare baseras på interna implementeringsdetaljer för skalbar topologi logiken och exponeras inte externt. Antalet läsare kan ändra när ett jobb startas eller uppgradering av jobbet.

Felet som visas när antalet mottagare överskrider det högsta värdet är: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> När antalet läsare ändras vid en uppgradering av jobb, skrivs tillfälliga varningar till granskningsloggar. Stream Analytics-jobb återställa automatiskt från dessa tillfälliga problem.

### <a name="add-a-consumer-group-in-event-hubs"></a>Lägg till en konsumentgrupp i Event Hubs
Följ dessa steg för att lägga till en ny konsumentgrupp i Event Hubs-instans:

1. Logga in på Azure Portal.

2. Leta upp dina Event Hubs.

3. Välj **Event Hubs** under rubriken **entiteter** .

4. Välj Event Hub efter namn.

5. På sidan **Event Hubs instans** , under rubriken **entiteter** , väljer du **konsument grupper**. En konsument grupp med namnet **$default** visas.

6. Välj **+ konsument grupp** för att lägga till en ny konsument grupp. 

   ![Lägg till en konsumentgrupp i Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. När du har skapat indata i Stream Analytics-jobbet så att den pekar till Händelsehubben kan angett du det konsumentgruppen. $Default används när inget har angetts. När du skapar en ny konsumentgrupp kan redigera Event Hub-indata i Stream Analytics-jobb och ange namnet på den nya konsumentgruppen.


## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Läsare per partition överskrider gränsen för Event Hubs

Om strömmande frågesyntaxen hänvisar till samma inkommande Event Hub-resurs flera gånger, kan jobb-motorn använda flera läsare per fråga från samma konsumentgruppen. När det finns för många referenser till samma konsumentgruppen, jobbet kan överskrida gränsen på fem och genereras ett fel. I dessa fall kan du ytterligare dividera med hjälp av flera inmatningar över flera konsumentgrupper med hjälp av lösningen som beskrivs i följande avsnitt. 

Scenarier där antalet läsare per partition är längre än Händelsehubbar fem är följande:

* Flera SELECT-uttryck: om du använder flera SELECT-uttryck som refererar till **samma** Event Hub-inmatare, kan varje SELECT-instruktion orsaka att en ny mottagare skapas.
* UNION: när du använder en UNION är det möjligt att ha flera indata som refererar till **samma** händelsehubben och konsument grupp.
* SJÄLV koppling: när du använder en självbetjänings åtgärd är det möjligt att referera till **samma** händelsehubben flera gånger.

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

Om du behöver ytterligare hjälp kan du prova vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
