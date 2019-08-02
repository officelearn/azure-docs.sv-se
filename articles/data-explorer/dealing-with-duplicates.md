---
title: Hantera dubblettdata i Azure Datautforskaren
description: I det här avsnittet visas olika metoder för att hantera dubblettdata när du använder Azure Datautforskaren.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 60ec2b86e0205060f907f1fe39d084dca3aac1cd
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608223"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Hantera dubblettdata i Azure Datautforskaren

Enheter som skickar data till molnet upprätthåller en lokal cache med data. Beroende på data storleken kan den lokala cachen lagra data i dagar eller till och med månader. Du vill skydda dina analys databaser från att inte använda enheter som skickar cachelagrade data på ett bra och orsaka dataduplicering i analys databasen. Det här avsnittet beskriver metod tips för att hantera dubblettdata för de här typerna av scenarier.

Den bästa lösningen för dataduplicering förhindrar dupliceringen. Om möjligt kan du åtgärda problemet tidigare i datapipeline, vilket sparar kostnader som är kopplade till data förflyttningen tillsammans med datapipelinen och som gör att du kan undvika utgifts resurser på Kopiera med dubbla data som matas in i systemet. I situationer där det inte går att ändra käll systemet finns det dock olika sätt att hantera det här scenariot.

## <a name="understand-the-impact-of-duplicate-data"></a>Förstå effekten av duplicerade data

Övervaka procent andelen duplicerade data. När procent andelen duplicerade data har identifierats kan du analysera omfattningen av problemet och affärs påverkan och välja lämplig lösning.

Exempel fråga för att identifiera procent andelen dubbla poster:

```kusto
let _sample = 0.01; // 1% sampling
let _data =
DeviceEventsAll
| where EventDateTime between (datetime('10-01-2018 10:00') .. datetime('10-10-2018 10:00'));
let _totalRecords = toscalar(_data | count);
_data
| where rand()<= _sample
| summarize recordsCount=count() by hash(DeviceId) + hash(EventId) + hash(StationId)  // Use all dimensions that make row unique. Combining hashes can be improved
| summarize duplicateRecords=countif(recordsCount  > 1)
| extend duplicate_percentage = (duplicateRecords / _sample) / _totalRecords  
```

## <a name="solutions-for-handling-duplicate-data"></a>Lösningar för att hantera dubblettdata

### <a name="solution-1-dont-remove-duplicate-data"></a>Lösnings #1: Ta inte bort duplicerade data

Förstå dina verksamhets krav och tolerans för duplicerade data. Vissa data uppsättningar kan hantera en viss procent andel duplicerade data. Om de duplicerade data inte har stor påverkan kan du ignorera dess förekomst. Fördelen med att inte ta bort dubblettdata är ingen extra kostnad för inmatnings processen eller frågans prestanda.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>Lösnings #2: Hantera duplicerade rader under frågan

Ett annat alternativ är att filtrera bort de duplicerade raderna i data under frågan. Den [`arg_max()`](/azure/kusto/query/arg-max-aggfunction) sammanställda funktionen kan användas för att filtrera bort dubblettposter och returnera den sista posten baserat på tidsstämpeln (eller en annan kolumn). Fördelen med att använda den här metoden är snabbare inmatningen eftersom den avdupliceras under fråge tiden. Dessutom är alla poster (inklusive dubbletter) tillgängliga för granskning och fel sökning. Nack delen med att använda `arg_max` funktionen är den ytterligare fråge tiden och belastningen på processorn varje gång data efter frågas. Beroende på hur mycket data som efter frågas kan den här lösningen bli icke-funktionell eller minnes krävande och behöver växla till andra alternativ.

I följande exempel frågar vi den sista posten som matats in för en uppsättning kolumner som bestämmer unika poster:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Den här frågan kan också placeras inuti en funktion i stället för att direkt fråga tabellen:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>Lösnings #3: Filtrera dubbletter under inmatnings processen

En annan lösning är att filtrera dubbletter under inmatnings processen. Systemet ignorerar dubblettdata vid inmatning i Kusto-tabeller. Data matas in i en mellanlagringsdatabas och kopieras till en annan tabell efter att duplicerade rader har tagits bort. Fördelen med den här lösningen är att frågans prestanda förbättras dramatiskt jämfört med föregående lösning. Nack delarna omfattar ökad Inhämtnings tid och ytterligare kostnader för data lagring. Ytterligare, den här lösningen fungerar bara om dupliceringen inte matas in samtidigt. Om det finns flera samtidiga inmatningar som innehåller dubblettposter kan alla matas in eftersom dedupliceringen inte kan hitta några befintliga matchande poster i tabellen.    

Följande exempel illustrerar den här metoden:

1. Skapa en annan tabell med samma schema:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Skapa en funktion för att filtrera bort dubblettposter genom att koppla de nya posterna till de tidigare inmatade posterna.

    ```kusto
    .create function RemoveDuplicateDeviceEvents()
    {
    DeviceEventsAll
    | join hint.strategy=broadcast kind = anti
        (
        DeviceEventsUnique
        | where EventDateTime > ago(7d)   // filter the data for certain time frame
        | limit 1000000   //set some limitations (few million records) to avoid choking-up the system during outage recovery

        ) on DeviceId, EventId, StationId
    }
    ```

    > [!NOTE]
    > Kopplingar är processor gränser och lägger till en extra belastning i systemet.

1. Ange [uppdaterings princip](/azure/kusto/management/update-policy) för `DeviceEventsUnique` tabell. Uppdaterings principen aktive ras när nya data skickas till `DeviceEventsAll` tabellen. Kusto-motorn kör automatiskt funktionen när nya [omfattningar](/azure/kusto/management/extents-overview) skapas. Bearbetningen är begränsad till de nyligen skapade data. Följande kommando häftar käll tabellen (`DeviceEventsAll`), mål tabellen (`DeviceEventsUnique`) och funktionen `RemoveDuplicatesDeviceEvents` tillsammans för att skapa uppdaterings principen.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > Uppdaterings principen utökar längden på inmatningen eftersom data filtreras under inmatningen och sedan matas in två gånger `DeviceEventsAll` (till tabellen och `DeviceEventsUnique` tabellen).

1. Valfritt Ange en lägre data kvarhållning i `DeviceEventsAll` tabellen för att undvika att lagra kopior av data. Välj antalet dagar beroende på data volymen och hur lång tid du vill behålla data för fel sökning. Du kan ställa in den `0d` på Days kvarhållning för att spara KSV och förbättra prestandan eftersom data inte överförs till lagringen.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Sammanfattning

Dataduplicering kan hanteras på flera sätt. Utvärdera alternativen noggrant och ta hänsyn till pris och prestanda för att fastställa rätt metod för din verksamhet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skriva frågor för Azure Data Explorer](write-queries.md)
