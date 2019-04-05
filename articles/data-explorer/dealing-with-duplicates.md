---
title: Hantera duplicerade data i Datautforskaren i Azure
description: Det här avsnittet beskriver olika metoder för att hantera duplicerade data när du använder Datautforskaren i Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 8f55b6dfb7b5bc9eda675aca4ed80a66b8a25a7f
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045789"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Hantera duplicerade data i Datautforskaren i Azure

Enheter som skickar data till molnet underhålla en lokal cache av data. Beroende på storleken på data kunde den lokala cachen lagrar data för dagar eller till och med månader. Vill du skydda dina analytiska databaser från felaktig enheter som skicka cachelagrade data och orsaka duplicerade data i analytiska databasen. Det här avsnittet beskriver de bästa metoderna för hantering av duplicerade data för dessa typer av scenarier.

Den bästa lösningen för duplicerade data förhindrar risken. Om det är möjligt, åtgärda problemet tidigare i datapipeline, vilket sparar kostnader i samband med dataförflyttning längsmed datapipelinen och på så sätt undviker lägga resurser på klara av duplicerade data som matas in i systemet. I situationer där källsystemet inte kan ändras, finns det dock olika sätt att hantera det här scenariot.

## <a name="understand-the-impact-of-duplicate-data"></a>Förstå effekten av duplicerade data

Övervaka procentandelen av duplicerade data. När procentandelen av duplicerade data som har identifierats kan du analysera omfånget för de problem och inverkan på företaget och väljer rätt lösning.

Exempelfråga att identifiera dubblettposter procentandelen:

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

## <a name="solutions-for-handling-duplicate-data"></a>Lösningar för hantering av duplicerade data

### <a name="solution-1-dont-remove-duplicate-data"></a>Lösning #1: Ta inte bort duplicerade data

Förstå dina affärskrav och tolerans dubblettvärden. Vissa datauppsättningar kan hantera med en viss procentandel av duplicerade data. Om duplicerade data inte har avgörande betydelse, kan du ignorera sin närvaro. Fördelen med att inte ta bort duplicerade data är inga ytterligare kostnader på prestanda för datapåfyllning process eller frågan.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>Lösning #2: Hantera dubblettrader när frågan körs

Ett annat alternativ är att filtrera bort dubblettrader i data när frågan körs. Den [ `arg_max()` ](/azure/kusto/query/arg-max-aggfunction) samlingsfunktion kan användas för att filtrera bort dubblettposter och returnerar den sista posten baserat på tidsstämpeln (eller en annan kolumn). Fördelen med att använda den här metoden är snabbare inmatning eftersom avduplicering inträffar under fråga körs. Dessutom kan alla poster (inklusive dubbletter) som är tillgängliga för granskning och felsökning. Nackdelen med den `arg_max` funktionen är ytterligare frågetiden och belastning på Processorn varje gång data efterfrågas. Beroende på mängden data som efterfrågas, den här lösningen kan bli icke-funktionella eller förbrukar minnet och måste växla till andra alternativ.

I följande exempel frågar vi den sista posten som matas in för en uppsättning kolumner som bestämmer unika poster:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Den här frågan kan också placeras inuti en funktion i stället för att fråga direkt till tabellen:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>Lösning #3: Filtrera dubbletter under inmatning

En annan lösning är att filtrera dubbletter under inmatning. Systemet ignorerar den duplicerade informationen under inmatning till Kusto-tabeller. Data matas in i en mellanlagringstabell och kopieras till en annan tabell när du tar bort dubblettrader. Fördelen med den här lösningen är att frågeprestanda avsevärt förbättrar jämfört med den tidigare lösningen. Nackdelarna är ökad inmatning tid och kostnader för lagring av ytterligare data.

I följande exempel visar den här metoden:

1. Skapa en annan tabell med samma schema:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Skapa en funktion för att filtrera bort dubblettposter genom att slå samman mot de nya posterna med de tidigare insamlade.

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
    > Kopplingar är CPU-bundna åtgärder och lägga till ytterligare belastning på systemet.

1. Ange [uppdateringsprincip](/azure/kusto/management/update-policy) på `DeviceEventsUnique` tabell. Update-princip aktiveras när nya data hamnar i den `DeviceEventsAll` tabell. Kusto-motorn kommer automatiskt att köra funktionen som ny [allokeringsutrymmen](/azure/kusto/management/extents-overview) skapas. Bearbetningen är begränsad till de nya data. Följande kommando häftar samman källtabellen (`DeviceEventsAll`), Måltabell (`DeviceEventsUnique`), och funktionen `RemoveDuplicatesDeviceEvents` ihop för att skapa update-princip.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > Uppdateringsprincip utökar varaktigheten för inmatning eftersom data filtreras vid inmatning och sedan matas in två gånger (till den `DeviceEventsAll` tabellen och den `DeviceEventsUnique` tabell).

1. (Valfritt) Ange en lägre kvarhållning av data på den `DeviceEventsAll` tabell för att undvika att lagra kopior av data. Välj antalet dagar, beroende på datavolymen och hur lång tid som du vill behålla data för felsökning. Du kan ange den till `0d` dagars kvarhållning för att spara kostnad för sålda varor och förbättra prestanda, eftersom data inte har överförts till lagring.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Sammanfattning

Duplicerade data kan hanteras på flera olika sätt. Utvärdera alternativ med noggrant, med hänsyn till kontot pris och prestanda, för att fastställa rätt metod för ditt företag.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skriv frågor för Azure Data Explorer](write-queries.md)
