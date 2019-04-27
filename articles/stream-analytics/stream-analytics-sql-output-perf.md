---
title: Azure Stream Analytics-utdata till Azure SQL Database
description: Läs om skickar data till SQL Azure från Azure Stream Analytics och uppnå högre hastigheter för skrivning.
services: stream-analytics
author: chetanmsft
ms.author: chetanmsft
manager: katiiceva
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/18/2019
ms.openlocfilehash: 4be73554df0b6bddaafe3910c80c855e127d79f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771659"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Azure Stream Analytics-utdata till Azure SQL Database

Den här artikeln beskrivs tips för att uppnå bättre prestanda för skrivåtgärder dataflöde när du läser in data till SQL Azure Database med Azure Stream Analytics.

SQL-utdata i Azure Stream Analytics har stöd för skrivning parallellt som ett alternativ. Det här alternativet möjliggör [fullständigt parallella](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) jobb-topologier, där flera partitioner i utdata skrivs till tabellen parallellt. Aktivera det här alternativet i Azure Stream Analytics kanske men inte är tillräckliga för att uppnå högre genomströmning, eftersom den avsevärt beror på din konfiguration för SQL Azure-databas och tabellschemat. Valet av index, klustring nyckel, fyllningsfaktor för index och komprimering påverka tid att läsa in tabeller. Läs mer om hur du optimerar dina SQL Azure database för att förbättra fråga och läsa in prestanda baserat på interna prestandamått [SQL database-prestandaråd](../sql-database/sql-database-performance-guidance.md). Sorteringen av skrivningar garanteras inte när du skriver parallellt till SQL Azure-databas.

Här följer några konfigurationer inom varje tjänst som bidrar till att förbättra hela dataflödet för din lösning.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Ärv partitionering** – den här SQL utdata configuration alternativet aktiverar ärver partitioneringsschemat av din föregående frågesteg eller indata. Alternativet är aktiverat, skriver till en diskbaserad tabell och gör att en [fullständigt parallella](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topologi för ditt jobb förvänta dig att se bättre genomströmning. Partitionering redan automatiskt sker för många andra [matar ut](stream-analytics-parallelization.md#partitions-in-sources-and-sinks). Tabellen låsning (TABLOCK) har också inaktiverats för bulkinfogningar som gjorts med det här alternativet.

> [!NOTE] 
> När det finns fler än 8 inkommande partitioner, kanske ärver indata partitioneringsschema inte är det bästa valet. Den här maxgräns observerades i en tabell med en enda identitetskolumn och ett grupperat index. I det här fallet bör du använda [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 i din fråga, kan du uttryckligen ange antalet utdata skrivare. Baserat på schemat och val av index, kan din observationer variera.

- **Batchstorlek** -konfiguration av SQL-utdata kan du ange den maximala batchstorleken i Azure Stream Analytics SQL utdata baserat på typen av mål tabell/arbetsbelastningen. Batch är det maximala antalet poster som skickas med varje bulk insert transaktion. I klustrade columnstore-index, batch-storlekar runt [100 kB](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) tillåter flera parallellisering, minimal loggning och låsa optimeringar. I diskbaserade tabeller vara 10K (standard) eller lägre optimala för din lösning som högre batch-storlekar kan utlösa Lås eskalering under bulkinfogningar.

- **Ange meddelande justering** – om du har optimerats med ärver partitionering och batch-storlek, ökar antalet inkommande händelser per meddelande per partition hjälper dig att skicka ytterligare upp genomströmning för skrivning. Indatameddelande justering kan batch-storlekar i Azure Stream Analytics vara upp till den angivna storleken för Batch, vilket ger bättre genomflöde. Detta kan uppnås med hjälp av [komprimering](stream-analytics-define-inputs.md) eller att öka indatameddelande storlekar i EventHub- eller Blob.

## <a name="sql-azure"></a>SQL Azure

- **Partitionerade tabeller och index** – med hjälp av en [partitionerade](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) kan avsevärt minska contentions mellan SQL-tabell och partitionerade index för tabellen med samma kolumn som din partitionsnyckel (till exempel PartitionId) partitioner under skrivåtgärder. För en partitionerad tabell måste du skapa en [partitionsfunktioner](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) och en [partitionsschema](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) på den primära filgruppen. Detta ökar också tillgängligheten för befintliga data medan nya data läses. Logg-i/o-begränsning kan träffa baserat på antalet partitioner som kan utökas genom att uppgradera SKU: N.

- **Undvika unika nyckelfel** – om du får [flera felet varningsmeddelanden](stream-analytics-common-troubleshooting-issues.md#handle-duplicate-records-in-azure-sql-database-output) i aktivitetsloggen för Azure Stream Analytics, se till att jobbet inte påverkas av unik begränsning överträdelser som kan inträffa under återställning fall. Detta kan undvikas genom att ange den [IGNORERA\_Duplicera\_nyckel](stream-analytics-common-troubleshooting-issues.md#handle-duplicate-records-in-azure-sql-database-output) alternativet på ditt index.

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory och InMemory-tabeller

- **InMemory-tabell som temporär tabell** – [InMemory-tabeller](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) tillåta för mycket snabba Datainläsningen men data som ska få plats i minnet. Prestandamått show massinläsning från en InMemory tabell till en diskbaserad tabell är ungefär 10 gånger snabbare än direkt bulk infoga med en enda skrivare till diskbaserad-tabell med en identity-kolumn och ett grupperat index. Om du vill utnyttja det här bulk insert prestanda ställer du in en [kopieringsjobbet med Azure Data Factory](../data-factory/connector-azure-sql-database.md) som kopierar data från InMemory-tabell till diskbaserad tabell.

## <a name="avoiding-performance-pitfalls"></a>Undvika vanliga problem
Samtidigt som infogar data är mycket snabbare än att läsa in data med enskilda infogningar eftersom det upprepade arbetet med att överföra data, parsning insert-instruktionen, som kör instruktionen och utfärda en transaktion post undviks. I stället används en mer effektiv sökväg till lagringsmotorn för att strömma data. Installationsprogrammet kostnaden för den här sökvägen är dock mycket högre än en enda insert-instruktionen i en diskbaserad tabell. Brytpunkten är vanligtvis cirka 100 rader, utöver massbearbetning läser in nästan alltid är mer effektiv. 

Om takt händelser är låg, kan det enkelt skapa batch-storlekar lägre än 100 rader, vilket gör bulk insert ineffektiv och använder för mycket diskutrymme. För att undvika denna begränsning, kan du göra något av följande:
* Skapa en INSTEAD OF [utlösaren](/sql/t-sql/statements/create-trigger-transact-sql) att använda enkla insert för varje rad.
* Använda en temporär tabell i minnet som beskrivs i föregående avsnitt.

Ett annat scenario inträffar när du skriver till ett icke-grupperade columnstore-index (NCCI), där mindre bulkinfogningar kan skapa för många segment som kan krascha indexet. I det här fallet är rekommendationen att använda ett grupperat Columnstore-index i stället.

## <a name="summary"></a>Sammanfattning

Sammanfattningsvis med partitionerade utdata-funktionen i Azure Stream Analytics för SQL-utdata justerade parallellisering för dina jobb med en partitionerad tabell i SQL Azure bör du få betydande dataflödet förbättringar. Utnyttjar Azure Data Factory för att samordna dataförflyttning från en InMemory-tabell i diskbaserade tabeller kan ge storleksordning genomflödesvinsterna. Om så är möjligt, kan förbättra meddelande densitet också vara en viktig faktor förbättra hela dataflödet.
