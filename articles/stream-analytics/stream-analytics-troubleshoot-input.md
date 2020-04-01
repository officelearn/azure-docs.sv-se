---
title: Felsöka indata för Azure Stream Analytics
description: I den här artikeln beskrivs tekniker för att felsöka indataanslutningar i Azure Stream Analytics-jobb.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 3d88123b3dd79e5707c5c19cbbae13c30cbdeb84
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409420"
---
# <a name="troubleshoot-input-connections"></a>Felsöka indataanslutningar

I den här artikeln beskrivs vanliga problem med Azure Stream Analytics-indataanslutningar, hur du felsöker indataproblem och hur du åtgärdar problemen. Många felsökningssteg kräver att diagnostikloggar aktiveras för ditt Stream Analytics-jobb. Om du inte har aktiverat diagnostikloggar läser [du Felsöka Azure Stream Analytics med hjälp av diagnostikloggar](stream-analytics-job-diagnostic-logs.md).

## <a name="input-events-not-received-by-job"></a>Indatahändelser som inte tas emot av jobbet 

1.  Testa indata- och utdataanslutningen. Verifiera anslutningen till in- och utdata med hjälp av knappen **Testa anslutning** för varje in- och utdata.

2.  Undersök dina indata.

    1. Använd knappen [**Exempeldata**](stream-analytics-sample-data-input.md) för varje indata. Hämta indata.
        
    1. Kontrollera exempeldata för att förstå schema- och [datatyperna](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics).
    
    1. Kontrollera [mått för händelsehubb](../event-hubs/event-hubs-metrics-azure-monitor.md) för att säkerställa att händelser skickas. Meddelandemått bör vara större än noll om eventhubbar tar emot meddelanden.

3.  Kontrollera att du har valt ett tidsintervall i förhandsgranskningen av indata. Välj **Välj tidsintervall**och ange sedan en exempellängd innan du testar frågan.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Felaktiga indatahändelser resulterar i deserialiseringsfel 

Avserialiseringsproblem uppstår när indataströmmen för ditt Stream Analytics-jobb innehåller felaktiga meddelanden. Ett felaktigt meddelande kan till exempel orsakas av en parentes, eller klammerparentes, i ett JSON-objekt eller ett felaktigt tidsstämpelformat i tidsfältet. 
 
När ett Stream Analytics-jobb tar emot ett felaktigt meddelande från en indata tappar det meddelandet och meddelar dig med en varning. En **varningssymbol** visas på panelen Ingångar i ditt Stream Analytics-jobb. Följande varningssymbol finns så länge jobbet körs:

![Panelen Azure Stream Analytics-indata](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Aktivera diagnostikloggar för att visa information om felet och meddelandet (nyttolasten) som orsakade felet. Det finns flera orsaker till att deserialiseringsfel kan uppstå. Mer information om specifika deserialiseringsfel finns i [Indatafel](data-errors.md#input-data-errors). Om diagnostikloggar inte är aktiverade kommer ett kort meddelande att vara tillgängligt i Azure-portalen.

![Varningsmeddelande för indatainformation](media/stream-analytics-malformed-events/warning-message-with-offset.png)

Om meddelandenyttolasten är större än 32 kB eller är i binärt format kör du den CheckMalformedEvents.cs kod som är tillgänglig i [GitHub-exempeldatabasen](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Den här koden läser partitions-ID, förskjutning och skriver ut data som finns i den förskjutningen. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Jobbet överskrider maximala mottagare av eventhubben

En bra metod för att använda Event Hubs är att använda flera konsumentgrupper för jobbskalbarhet. Antalet läsare i Stream Analytics-jobbet för en viss indata påverkar antalet läsare i en enskild konsumentgrupp. Det exakta antalet mottagare baseras på interna implementeringsdetaljer för utskalningstopologilogiken och exponeras inte externt. Antalet läsare kan ändras när ett jobb startas eller under jobbuppgraderingar.

Felet som visas när antalet mottagare överskrider det högsta värdet är: 

`The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> När antalet läsare ändras under en jobbuppgradering skrivs tillfälliga varningar till granskningsloggar. Stream Analytics-jobb återställs automatiskt från dessa tillfälliga problem.

### <a name="add-a-consumer-group-in-event-hubs"></a>Lägga till en konsumentgrupp i eventhubbar

Så här lägger du till en ny konsumentgrupp i händelsehubbar-instansen:

1. Logga in på Azure Portal.

2. Leta reda på händelsehubben.

3. Välj **Händelsehubbar** under rubriken **Entiteter.**

4. Välj händelsehubben efter namn.

5. Välj **Konsumentgrupper**under rubriken **Entiteter** på sidan **Instans för händelsehubbar.** En konsumentgrupp med namn **$Default** visas.

6. Välj **+ Konsumentgrupp** om du vill lägga till en ny konsumentgrupp. 

   ![Lägga till en konsumentgrupp i eventhubbar](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. När du skapade indata i Stream Analytics-jobbet för att peka på händelsehubben angav du konsumentgruppen där. **$Default** används när ingen anges. När du har skapat en ny konsumentgrupp redigerar du indata för händelsehubben i Stream Analytics-jobbet och anger namnet på den nya konsumentgruppen.

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Läsare per partition överskrider gränsen för händelsehubbar

Om syntaxen för direktuppspelning refererar till samma indatahändelsehubbresurs flera gånger kan jobbmotorn använda flera läsare per fråga från samma konsumentgrupp. När det finns för många referenser till samma konsumentgrupp kan jobbet överskrida gränsen på fem och skapa ett fel. Under dessa omständigheter kan du dela upp flera indata ytterligare i flera konsumentgrupper med hjälp av den lösning som beskrivs i följande avsnitt. 

Scenarier där antalet läsare per partition överskrider gränsen för händelsehubbar på fem inkluderar följande:

* Flera SELECT-satser: Om du använder flera SELECT-satser som refererar till **samma** händelsehubbinmatning, gör varje SELECT-sats att en ny mottagare skapas.

* UNION: När du använder en UNION är det möjligt att ha flera indata som refererar till **samma** händelsenav och konsumentgrupp.

* SJÄLVKOPPLING: När du använder en SELF JOIN-åtgärd är det möjligt att referera till **samma** händelsehubb flera gånger.

Följande metodtips kan hjälpa till att minska scenarier där antalet läsare per partition överskrider gränsen för händelsehubbar på fem.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Dela upp frågan i flera steg med hjälp av en WITH-sats

MED-satsen anger en tillfällig namngiven resultatuppsättning som kan refereras av en FROM-sats i frågan. Du definierar WITH-satsen i körningsomfånget för en enda SELECT-sats.

I stället för den här frågan:

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

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Se till att indata binder till olika konsumentgrupper

Om du vill ha frågor där tre eller flera indata är anslutna till samma konsumentgrupp för eventhubbar skapar du separata konsumentgrupper. Detta kräver att ytterligare Stream Analytics-indata skapas.

## <a name="get-help"></a>Få hjälp

Om du vill ha mer hjälp kan du prova vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)
