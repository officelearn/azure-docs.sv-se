---
title: Förbättra indexprestanda för columnstore
description: Minska minneskraven eller öka det tillgängliga minnet för att maximera antalet rader som ett columnstore-index komprimerar till varje radgrupp.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: f1f3667c088c5f7300317ea02ca19a72e4e62905
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431038"
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Maximera radgruppskvaliteten för columnstore

Radgruppskvaliteten bestäms av antalet rader i en radgrupp. Om du ökar det tillgängliga minnet kan du maximera antalet rader som ett columnstore-index komprimerar till varje radgrupp.  Använd dessa metoder för att förbättra komprimeringshastigheter och frågeprestanda för columnstore-index.

## <a name="why-the-rowgroup-size-matters"></a>Varför radgruppsstorleken är viktig

Eftersom ett columnstore-index söker igenom en tabell genom att skanna kolumnsegment för enskilda radgrupper, förbättrar resultatet av frågeprestanda om du maximerar antalet rader i varje radgrupp. När radgrupper har ett stort antal rader förbättras datakomprimeringen vilket innebär att det finns mindre data att läsa från disken.

Mer information om radgrupper finns i [Columnstore Indexes Guide](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="target-size-for-rowgroups"></a>Målstorlek för radgrupper

För bästa frågeprestanda är målet att maximera antalet rader per radgrupp i ett columnstore-index. En radgrupp kan ha högst 1 048 576 rader. Det är okej att inte ha det maximala antalet rader per radgrupp. Columnstore-index uppnår goda prestanda när radgrupper har minst 100 000 rader.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Radgrupper kan trimmas under komprimering

Under en massinläsning eller ombyggnad av columnstore-index finns det ibland inte tillräckligt med ledigt minne för att komprimera alla rader som har angetts för varje radgrupp. När det finns minnestryck trimmar columnstore-index radgruppsstorlekarna så att komprimeringen till columnstore kan lyckas.

När det inte finns tillräckligt med minne för att komprimera minst 10 000 rader till varje radgrupp genereras ett fel.

Mer information om massinläsning finns [i Massinläsning i ett grupperat columnstore-index](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#Bulk ).

## <a name="how-to-monitor-rowgroup-quality"></a>Så här övervakar du radgruppskvaliteten

DMV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats ([sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) innehåller vydefinitionen som matchar SQL DB) som visar användbar information, till exempel antal rader i radgrupper och orsaken till trimning om det fanns trimning. Du kan skapa följande vy som ett praktiskt sätt att fråga den här DMV:n för att få information om radergruppstrimning.

```sql
create view dbo.vCS_rg_physical_stats
as
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]
)
select *
from cte;
```

Den trim_reason_desc anger om radgruppen trimmades(trim_reason_desc = NO_TRIM innebär att det inte fanns någon trimning och radgruppen är av optimal kvalitet). Följande trimorsaker indikerar för tidig trimning av radgruppen:

- BULKLOAD: Den här trimorsaken används när den inkommande batchen med rader för belastningen hade mindre än 1 miljon rader. Motorn skapar komprimerade radgrupper om det finns fler än 100 000 rader som infogas (i stället för att infogas i deltaarkivet) men anger trimningsorsaken till BULKLOAD. I det här fallet bör du öka batchinläsningen så att den innehåller fler rader. Omvärdera partitioneringsschemat för att säkerställa att det inte är för detaljerat eftersom radgrupper inte kan spänna över partitionsgränser.
- MEMORY_LIMITATION: För att skapa radgrupper med 1 miljon rader krävs en viss mängd arbetsminne av motorn. När det tillgängliga minnet för inläsningssessionen är mindre än det arbetsminne som krävs, trimmas radgrupper i förtid. I följande avsnitt beskrivs hur du uppskattar det minne som krävs och allokerar mer minne.
- DICTIONARY_SIZE: Den här trimoren anger att radgruppstrimning inträffade eftersom det fanns minst en strängkolumn med breda och/eller höga kardinalitetssträngar. Ordlistestorleken är begränsad till 16 MB i minnet och när den här gränsen har nåtts komprimeras radgruppen. Om du stöter på den här situationen kan du isolera den problematiska kolumnen i en separat tabell.

## <a name="how-to-estimate-memory-requirements"></a>Så här beräknar du minneskrav

Det maximala minne som krävs för att komprimera en radgrupp är ungefär

- 72 MB +
- \#rader \* \#kolumner \* 8 byte +
- \#\* \#rader kortsträngkolumner \* 32 byte +
- \#långsträngskolumner \* 16 MB för komprimeringsordlista

där korta strängkolumner använder strängdatatyper av <= 32 byte och långsträngskolumner använder strängdatatyper av > 32 byte.

Långa strängar komprimeras med en komprimeringsmetod som är avsedd för komprimering av text. Med den här komprimeringsmetoden används en *ordlista* för att lagra textmönster. Den maximala storleken på en ordlista är 16 MB. Det finns bara en ordlista för varje lång strängkolumn i radgruppen.

En djupgående diskussion om columnstore-minneskrav finns i [video-synaps-SQL-skalningen: konfiguration och vägledning](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Olika sätt att minska minneskraven

Använd följande tekniker för att minska minneskraven för att komprimera radgrupper till columnstore-index.

### <a name="use-fewer-columns"></a>Använda färre kolumner

Om möjligt utformar du tabellen med färre kolumner. När en radgrupp komprimeras till columnstore komprimerar columnstore-indexet varje kolumnsegment separat. Därför ökar minneskraven för att komprimera en radgrupp när antalet kolumner ökar.

### <a name="use-fewer-string-columns"></a>Använda färre strängkolumner

Kolumner med strängdatatyper kräver mer minne än numeriska datatyper och datumdatatyper. Om du vill minska minneskraven bör du överväga att ta bort strängkolumner från faktatabeller och placera dem i mindre dimensionstabeller.

Ytterligare minneskrav för strängkomprimering:

- Strängdatatyper upp till 32 tecken kan kräva ytterligare 32 byte per värde.
- Strängdatatyper med fler än 32 tecken komprimeras med ordlistemetoder.  Varje kolumn i radgruppen kan kräva upp till ytterligare 16 MB för att skapa ordlistan.

### <a name="avoid-over-partitioning"></a>Undvik överpartitionering

Columnstore-index skapar en eller flera radgrupper per partition. För datalagring i Azure Synapse Analytics växer antalet partitioner snabbt eftersom data distribueras och varje distribution partitioneras. Om tabellen har för många partitioner kanske det inte finns tillräckligt med rader för att fylla radgrupperna. Bristen på rader skapar inte minnestryck under komprimering, men det leder till radergrupper som inte uppnår den bästa columnstore-frågeprestandan.

En annan anledning att undvika överpartitionering är att det finns ett minne omkostnader för att läsa in rader i ett columnstore-index på en partitionerad tabell. Under en belastning kan många partitioner ta emot inkommande rader, som hålls i minnet tills varje partition har tillräckligt med rader som ska komprimeras. Om du har för många partitioner skapas ytterligare minnestryck.

### <a name="simplify-the-load-query"></a>Förenkla inläsningsfrågan

Databasen delar minnesbidraget för en fråga bland alla operatorer i frågan. När en belastningsfråga har komplexa sorteringar och kopplingar minskas det tillgängliga minnet för komprimering.

Utforma inläsningsfrågan så att den bara fokuserar på att läsa in frågan. Om du behöver köra omvandlingar på data kör du dem separat från inläsningsfrågan. Till exempel arrangera data i en heap-tabell, kör omvandlingarna och sedan läsa in mellanlagringstabellen i columnstore-indexet. 

### <a name="adjust-maxdop"></a>Justera MAXDOP

Varje distribution komprimerar radgrupper till columnstore parallellt när det finns mer än en CPU-kärna tillgänglig per distribution. Parallellismen kräver ytterligare minnesresurser, vilket kan leda till minnestryck och radgruppstrimning.

För att minska minnestrycket kan du använda MAXDOP-frågetipset för att tvinga inläsningen att köras i seriellt läge inom varje distribution.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Olika sätt att allokera mer minne

DWU-storlek och användarresursklassen avgör tillsammans hur mycket minne som är tillgängligt för en användarfråga. Om du vill öka minnesbidraget för en belastningsfråga kan du antingen öka antalet DWUs eller öka resursklassen.

- Mer om du vill öka DWUs:erna finns i [Hur skalar jag prestanda?](../sql-data-warehouse/quickstart-scale-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- Information om hur du ändrar resursklassen för en fråga finns i [Ändra ett exempel på användarresursklass](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#change-a-users-resource-class).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan förbättra prestanda i Synapse SQL finns i [översikten Över prestanda](../overview-cheat-sheet.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

 
