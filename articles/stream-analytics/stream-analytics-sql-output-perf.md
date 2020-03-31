---
title: Azure Stream Analytics-utdata till Azure SQL Database
description: Lär dig mer om att mata ut data till SQL Azure från Azure Stream Analytics och uppnå högre skrivdataflödeshastigheter.
author: chetanmsft
ms.author: chetang
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: f68f973882af28d80b3a27bc4591c5ee932404a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443608"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Azure Stream Analytics-utdata till Azure SQL Database

I den här artikeln beskrivs tips för att uppnå bättre resultat för skrivdataflöde när du läser in data i SQL Azure Database med Azure Stream Analytics.

SQL-utdata i Azure Stream Analytics stöder att skriva parallellt som ett alternativ. Det här alternativet möjliggör [helt parallella](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) jobbtopologier, där flera utdatapartitioner skriver till måltabellen parallellt. Aktivera det här alternativet i Azure Stream Analytics kan dock inte vara tillräckligt för att uppnå högre dataflöde, eftersom det beror avsevärt på din SQL Azure-databaskonfiguration och tabellschema. Valet av index, klusternyckel, indexfyllningsfaktor och komprimering påverkar tiden för att läsa in tabeller. Mer information om hur du optimerar din SQL Azure-databas för att förbättra fråge- och belastningsprestanda baserat på interna riktmärken finns i [prestandavägledning för SQL-databasen](../sql-database/sql-database-performance-guidance.md). Beställning av skrivningar garanteras inte när du skriver parallellt med SQL Azure Database.

Här är några konfigurationer inom varje tjänst som kan förbättra det övergripande dataflödet för din lösning.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Ärva partitionering** – Det här sql-utdatakonfigurationsalternativet gör det möjligt att ärva partitioneringsschemat för föregående frågesteg eller indata. Med detta aktiverat, skriva till en disk-baserad tabell och har en [helt parallell](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topologi för ditt jobb, förväntar sig att se bättre dataflöde. Den här partitioneringen sker redan automatiskt för många andra [utdata](stream-analytics-parallelization.md#partitions-in-sources-and-sinks). Tabelllåsning (TABLOCK) är också inaktiverad för bulkskär som görs med det här alternativet.

> [!NOTE] 
> När det finns fler än 8 indatapartitioner kanske ärvning av indatapartitioneringsschemat inte vara ett lämpligt val. Den här övre gränsen observerades i en tabell med en enda identitetskolumn och ett grupperat index. I det här fallet bör du använda [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 i frågan för att uttryckligen ange antalet utdataskrivare. Baserat på ditt schema och val av index kan dina observationer variera.

- **Batchstorlek** - SQL-utdatakonfiguration kan du ange den maximala batchstorleken i en Azure Stream Analytics SQL-utdata baserat på vilken typ av måltabell/arbetsbelastning. Batchstorlek är det maximala antalet poster som skickas med varje bulkinfogningstransaktion. I grupperade columnstore-index möjliggör batchstorlekar runt [100 000](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) för mer parallellisering, minimal loggning och låsningsoptimeringar. I diskbaserade tabeller kan 10K (standard) eller lägre vara optimalt för din lösning, eftersom högre batchstorlekar kan utlösa låseskalering under massinfogningar.

- **Indatameddelandejustering** – Om du har optimerat med ärva partitionering och batchstorlek bidrar det ytterligare genom att öka antalet indatahändelser per meddelande per partition. Indatameddelandejustering gör att batchstorlekar i Azure Stream Analytics kan vara upp till den angivna batchstorleken, vilket förbättrar dataflödet. Detta kan uppnås genom att använda [komprimering](stream-analytics-define-inputs.md) eller öka indatameddelandestorlekar i EventHub eller Blob.

## <a name="sql-azure"></a>SQL Azure

- **Partitionerad tabell och index** – Om du använder en [partitionerad](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) SQL-tabell och partitionerade index i tabellen med samma kolumn som partitionsnyckeln (till exempel PartitionId) kan avsevärt minska konkurrensen mellan partitioner under skrivningar. För en partitionerad tabell måste du skapa en [partitionsfunktion](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) och ett [partitionsschema](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) i primärfilgruppen. Detta kommer också att öka tillgängligheten för befintliga data medan nya data läses in. Log IO-gränsen kan nås baserat på antalet partitioner, som kan ökas genom att uppgradera SKU.

- **Undvik unika huvudöverträdelser** – Om du får [flera varningsmeddelanden](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) om nyckelfel i Aktivitetsloggen för Azure Stream Analytics, se till att ditt jobb inte påverkas av unika begränsningsöverträdelser som sannolikt kommer att inträffa under återställningsärenden. Detta kan undvikas genom att ange alternativet [IGNORERA\_DUP-tangenten\_](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) på dina index.

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory- och In-Memory-tabeller

- **Minnestabell som temporär tabell** – [Tabeller i minnet](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) möjliggör mycket snabba databelastningar, men data måste passa i minnet. Benchmarks visar massinläsning från en minnestabell till en diskbaserad tabell är ungefär 10 gånger snabbare än direkt massinfogning med en enda skrivare i den diskbaserade tabellen med en identitetskolumn och ett grupperat index. Om du vill utnyttja den här massinfogningsprestandan konfigurerar du ett [kopieringsjobb med Azure Data Factory](../data-factory/connector-azure-sql-database.md) som kopierar data från tabellen i minnet till den diskbaserade tabellen.

## <a name="avoiding-performance-pitfalls"></a>Undvika prestanda fallgropar
Massinfogning av data är mycket snabbare än att läsa in data med enskilda infogningar eftersom upprepade omkostnader för överföring av data, tolkning av infoga-satsen, köra utdraget och utfärda en transaktionspost undviks. I stället används en effektivare sökväg i lagringsmotorn för att strömma data. Inställningskostnaden för den här sökvägen är dock mycket högre än en enda infogningssats i en diskbaserad tabell. Den break-even punkten är vanligtvis runt 100 rader, bortom vilken bulk lastning är nästan alltid effektivare. 

Om den inkommande händelsehastigheten är låg kan den enkelt skapa batchstorlekar som är lägre än 100 rader, vilket gör massinfogning ineffektivt och använder för mycket diskutrymme. Om du vill ta dig runt den här begränsningen kan du göra någon av följande åtgärder:
* Skapa en [IE-utlösare](/sql/t-sql/statements/create-trigger-transact-sql) för att använda enkel infogning för varje rad.
* Använd en temp-tabell för in-memory enligt beskrivningen i föregående avsnitt.

Ett annat sådant scenario inträffar när du skriver till ett icke-grupperat columnstore-index (NCCI), där mindre massinfogningar kan skapa för många segment, som kan krascha indexet. I det här fallet är rekommendationen att använda ett clustered columnstore-index i stället.

## <a name="summary"></a>Sammanfattning

Sammanfattningsvis, med den partitionerade utdatafunktionen i Azure Stream Analytics för SQL-utdata, justerad parallellisering av ditt jobb med en partitionerad tabell i SQL Azure bör ge dig betydande förbättringar av dataflödet. Genom att använda Azure Data Factory för att dirigera dataförflyttningar från en tabell i minnet till diskbaserade tabeller kan det ge storleksgenomströmningsvinster. Om det är möjligt kan förbättrad meddelandetäthet också vara en viktig faktor för att förbättra den totala genomströmningen.
