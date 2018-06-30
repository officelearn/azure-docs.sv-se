---
title: Azure Stream Analytics-utdata till Cosmos DB
description: Den här artikeln beskriver hur du använder Azure Stream Analytics för att spara utdata till Azure Cosmos DB för JSON-utdata för dataarkivering och låg latens frågor för Ostrukturerade JSON-data.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: ff2071a703b0b5e94cd68122a878b51e9d97669a
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112759"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Stream Analytics-utdata till Azure Cosmos DB  
Stream Analytics kan rikta [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) att aktivera arkivering och låg latens datafrågor på Ostrukturerade JSON-data för JSON-utdata. Det här dokumentet beskrivs några metoder för att implementera den här konfigurationen.

För de som inte är bekant med Cosmos DB, ta en titt på [Azure Cosmos DB Utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/documentdb/) att komma igång. 

> [!Note]
> Just nu är Azure Stream Analytics endast stöd för anslutning till Azure Cosmos DB med **SQL API**.
> Andra Azure Cosmos DB-API: er stöds inte ännu. Om platsen Azure Stream Analytics till Azure DB som Cosmos-konton som har skapats med andra API: er, kanske data inte korrekt lagras. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Grunderna i Cosmos DB som ett mål för utdata
Azure DB som Cosmos-utdata i Stream Analytics kan skriva strömmen bearbetning resultatet som JSON-utdata till Cosmos-DB-samling(ar). Stream Analytics skapa inte samlingar i databasen, i stället att du behöver skapa dem på en gång. Detta är så att fakturering kostnaderna för Cosmos DB samlingar styrs av du och så att du kan finjustera prestanda, konsekvens och kapacitet för samlingar direkt med den [Cosmos DB-API: er](https://msdn.microsoft.com/library/azure/dn781481.aspx). 

Cosmos DB samling alternativen beskrivs nedan.

## <a name="tune-consistency-availability-and-latency"></a>Finjustera konsekvens, tillgänglighet och svarstid
Om du vill matcha ditt programkrav kan Azure Cosmos DB du finjustera databasen och samlingar och kompromissa mellan konsekvens, tillgänglighet och svarstid. Beroende på vilka nivåer av läskonsekvens scenariot behov mot läsa och skriva latens, du kan välja en konsekvensnivå på ditt konto. Standard kan Azure Cosmos DB också synkron indexering på varje CRUD-åtgärd i din samling. Det här är ett annat bra alternativ för att ange skrivning/läsning prestanda i Azure Cosmos-databasen. Mer information finns i [ändra din databas och fråga konsekvensnivåer](../cosmos-db/consistency-levels.md) artikel.

## <a name="upserts-from-stream-analytics"></a>Upserts från Stream Analytics
Stream Analytics-integrering med Azure Cosmos DB kan du infoga eller uppdatera poster i din samling baserat på en viss dokument-ID-kolumn. Detta kallas även för som en *Upsert*.

Stream Analytics använder en optimistisk upsert metod där uppdateringar görs endast när insert misslyckas med ett dokument-ID-konflikter. Den här uppdateringen utförs som en korrigering, så gör deluppdateringar i dokumentet som, lägga till nya egenskaper eller ersätta en befintlig egenskap utförs inkrementellt. Ändringar i värdena i matrisen egenskaper i JSON-dokument resultatet i hela matrisen komma över, det vill säga matrisen inte slås samman.

## <a name="data-partitioning-in-cosmos-db"></a>Datapartitionering i Cosmos-DB
Azure Cosmos-DB [obegränsade](../cosmos-db/partition-data.md) är den rekommenderade metoden för partitionering dina data som Azure Cosmos DB automatiskt skalas partitioner baserat på din arbetsbelastning. Vid skrivning till obegränsat antal behållare, använder Stream Analytics så många parallella skrivare som föregående frågesteg eller indata som partitioneringsschema.

För fasta Azure Cosmos DB samlingar kan Stream Analytics inget sätt att skala upp eller ut när de är full. De har en övre gräns på 10 GB och 10 000 RU/s genomströmning.  Om du vill migrera data från en fast behållare till ett obegränsat antal behållare (till exempel en med minst 1 000 RU/s och en partitionsnyckel), måste du använda den [datamigreringsverktyg](../cosmos-db/import-data.md) eller [ändra feed biblioteket](../cosmos-db/change-feed.md).

Skrivning till flera fast behållare är inaktuell och är inte den rekommenderade metoden för att skala ut Stream Analytics-jobbet. Artikeln [partitionering och skalning i Cosmos DB](../cosmos-db/sql-api-partition-data.md) innehåller ytterligare information.

## <a name="cosmos-db-settings-for-json-output"></a>Cosmos DB inställningar för JSON-utdata
Skapa Cosmos DB som utdata i Stream Analytics genererar en fråga om information som visas nedan. Det här avsnittet innehåller en förklaring av egenskaper för definition.


![documentdb stream analytics utdata skärmen](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

Fält           | Beskrivning 
-------------   | -------------
Kolumnalias    | Ett alias att referera den här utdata i frågan ASA   
Kontonamn    | Namn eller endpoint-URI för Azure DB som Cosmos-konto 
Kontonyckel     | Den delade åtkomstnyckeln för kontot Azure Cosmos DB
Databas        | Databasnamnet Azure Cosmos DB
Samlingsnamn | Namnet på samlingen för samlingen som ska användas. `MyCollection` är en giltig inmatning för exemplet – en samling med namnet `MyCollection` måste finnas.  
Dokument-id     | Valfri. Kolumnnamnet i utdatahändelserna används som unik nyckel på vilka insert eller update baserat åtgärder. Om den lämnas tom kommer alla händelser att infogas, med uppdateringsalternativet.
