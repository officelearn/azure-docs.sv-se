---
title: Hantera dubblettdata i Azure Data Explorer
description: Det här avsnittet visar olika metoder för att hantera dubblettdata när du använder Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 60ec2b86e0205060f907f1fe39d084dca3aac1cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68608223"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Hantera dubblettdata i Azure Data Explorer

Enheter som skickar data till molnet upprätthåller en lokal cache av data. Beroende på datastorleken kan den lokala cachen lagra data i dagar eller till och med månader. Du vill skydda dina analytiska databaser från felaktiga enheter som skickar cachelagrade data igen och orsakar dataduplicering i den analytiska databasen. I det här avsnittet beskrivs metodtips för hantering av dubblettdata för dessa typer av scenarier.

Den bästa lösningen för dataduplicering är att förhindra dubblering. Om möjligt, åtgärda problemet tidigare i datapipelinen, vilket sparar kostnader i samband med dataförflyttning längs datapipelinen och undviker att spendera resurser på att hantera dubbla data som används i systemet. Men i situationer där källsystemet inte kan ändras finns det olika sätt att hantera det här scenariot.

## <a name="understand-the-impact-of-duplicate-data"></a>Förstå effekten av dubblettdata

Övervaka procentandelen dubblettdata. När procentandelen dubblettdata har identifierats kan du analysera problemets omfattning och affärspåverkan och välja lämplig lösning.

Exempelfråga för att identifiera procentandelen dubblettposter:

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

## <a name="solutions-for-handling-duplicate-data"></a>Lösningar för hantering av dubblettdata

### <a name="solution-1-dont-remove-duplicate-data"></a>Lösning #1: Ta inte bort dubblettdata

Förstå dina affärskrav och tolerans för dubblettdata. Vissa datauppsättningar kan hantera med en viss procentandel dubblettdata. Om de duplicerade data inte har stor inverkan kan du ignorera dess närvaro. Fördelen med att inte ta bort dubblettdata är inga ytterligare omkostnader för inmatningsprocessen eller frågeprestanda.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>Lösning #2: Hantera dubblettrader under fråga

Ett annat alternativ är att filtrera bort dubblettraderna i data under frågan. Den [`arg_max()`](/azure/kusto/query/arg-max-aggfunction) aggregerade funktionen kan användas för att filtrera bort dubblettposterna och returnera den sista posten baserat på tidsstämpeln (eller en annan kolumn). Fördelen med att använda den här metoden är snabbare inmatning eftersom de-duplicering sker under frågetiden. Dessutom är alla poster (inklusive dubbletter) tillgängliga för granskning och felsökning. Nackdelen med `arg_max` att använda funktionen är ytterligare frågetid och belastning på processorn varje gång data efterfrågas. Beroende på mängden data som efterfrågas kan den här lösningen bli icke-funktionell eller minnesförbrukande och kräver att du växlar till andra alternativ.

I följande exempel frågar vi den sista posten som förtärs för en uppsättning kolumner som bestämmer de unika posterna:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Den här frågan kan också placeras i en funktion i stället för att direkt fråga tabellen:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>Lösning #3: Filtrera dubbletter under inmatningsprocessen

En annan lösning är att filtrera dubbletter under inmatningsprocessen. Duplicera data ignoreras vid inmatning i Kusto-tabeller. Data förtärs till en mellanlagringstabell och kopieras till en annan tabell efter att du har tagit bort dubblettrader. Fördelen med den här lösningen är att frågeprestanda förbättras dramatiskt jämfört med den tidigare lösningen. Nackdelarna inkluderar ökad intagstid och ytterligare datalagringskostnader. Dessutom fungerar den här lösningen bara om duplicering inte förtärs samtidigt. Om det finns flera samtidiga intag som innehåller dubblettposter kan alla intas eftersom dedupliceringsprocessen inte hittar några befintliga matchande poster i tabellen.    

I följande exempel visas den här metoden:

1. Skapa en annan tabell med samma schema:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Skapa en funktion för att filtrera bort dubblettposterna genom att koppla de nya posterna mot de tidigare intappade posterna.

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
    > Kopplingar är CPU-bundna åtgärder och lägger till en extra belastning på systemet.

1. Ange [uppdateringsprincip](/azure/kusto/management/update-policy) på `DeviceEventsUnique` bordet. Uppdateringsprincipen aktiveras när nya data `DeviceEventsAll` hamnar i tabellen. Kusto-motorn kommer automatiskt att köra funktionen när nya [omfattningar skapas.](/azure/kusto/management/extents-overview) Bearbetningen är begränsad till de nyskapade data. Följande kommando innehåller källtabellen`DeviceEventsAll`( ),`DeviceEventsUnique`måltabellen `RemoveDuplicatesDeviceEvents` ( ) och funktionen tillsammans för att skapa uppdateringsprincipen.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > Uppdateringsprincipen förlänger varaktigheten för inmatning eftersom data filtreras under inmatning och sedan intas två gånger (till `DeviceEventsAll` tabellen och till `DeviceEventsUnique` tabellen).

1. (Valfritt) Ange en lägre datalagring i `DeviceEventsAll` tabellen för att undvika att lagra kopior av data. Välj antalet dagar beroende på datavolymen och hur länge du vill behålla data för felsökning. Du kan ställa `0d` in den på dagar lagring för att spara COGS och förbättra prestanda, eftersom data inte överförs till lagring.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Sammanfattning

Dataduplicering kan hanteras på flera sätt. Utvärdera alternativen noggrant, med hänsyn till pris och prestanda, för att bestämma rätt metod för ditt företag.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skriva frågor för Azure Data Explorer](write-queries.md)
