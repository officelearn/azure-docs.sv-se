---
title: "Förbättra prestanda för columnstore-index - Azure SQL Data Warehouse | Microsoft Docs"
description: "Minska minneskrav eller öka mängden tillgängligt minne för att maximera antalet rader som ett columnstore-index komprimeras i varje radgrupps."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 03/15/2018
ms.author: barbkess
ms.openlocfilehash: 74e641f9da418d678bdbef0c69f9f59ccee32303
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Maximera radgrupps kvalitet för columnstore

Radgrupps kvalitet bestäms av antalet rader i en radgrupps. Minska minneskrav eller öka mängden tillgängligt minne för att maximera antalet rader som ett columnstore-index komprimeras i varje radgrupps.  Du kan använda dessa metoder för att förbättra komprimering priser och fråga prestanda för columnstore-index.

## <a name="why-the-rowgroup-size-matters"></a>Radgrupps storlek
Eftersom ett columnstore-index skannar en tabell genom att skanna kolumnsegment med enskilda rowgroups, förbättrar maximera antalet rader i varje radgrupps prestanda för frågor. När rowgroups har ett stort antal rader, förbättrar datakomprimering vilket innebär att det finns mindre data vid läsning från disken.

Läs mer om rowgroups [Columnstore-index guiden](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Målstorlek för rowgroups
Målet är att maximera antalet rader per radgrupps i ett columnstore-index för bästa möjliga prestanda. En radgrupps kan ha högst 1 048 576 rader. Det går inte har det maximala antalet rader per radgrupps. Columnstore-index uppnå goda prestanda när rowgroups har minst 100 000 rader.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Rowgroups kan hämta trimmade under komprimering

Under en bulk load eller columnstore-index återskapning, ibland det finns inte tillräckligt med minne för att komprimera alla rader som anges för varje radgrupps. När minnesbelastning trim kolumnlagringsindex radgrupps-storlekar så komprimering i columnstore kan genomföras. 

När det finns inte tillräckligt med minne för att komprimera minst 10 000 rader till varje radgrupps, genererar ett fel i SQL Data Warehouse.

Mer information om massinläsning finns [massinläsning till ett grupperat columnstore-index](https://msdn.microsoft.com/en-us/library/dn935008.aspx#Bulk load into a clustered columnstore index).

## <a name="how-to-monitor-rowgroup-quality"></a>Så här övervakar du radgrupps kvalitet

Det finns en DMV (sys.dm_pdw_nodes_db_column_store_row_group_physical_stats) som visar användbar information, till exempel antalet rader i rowgroups och orsaken till trimning om det var trimning. Du kan skapa följande vy som ett praktiskt sätt att fråga den här DMV för att få information om radgrupps trimning.

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

Trim_reason_desc talar om radgrupps har trimmade (trim_reason_desc = NO_TRIM innebär det fanns ingen beskrivande text och raden är optimal kvalitet). Följande trim anger tidigt Trimning av radgrupps:
- BULKLOAD: Trim därför används när inkommande batchen med rader för den har mindre än 1 miljoner rader. Motorn skapar komprimerade radgrupper om det är större än 100 000 rader som ska infogas (i stället för att infoga i arkivet delta) men anger trim orsaken till BULKLOAD. Överväg att öka din batch belastningen fönster om du vill samla fler rader i det här scenariot. Dessutom omvärdera din partitioneringsschema så det inte är för detaljerade som radgrupper inte får omfatta partitionsgränser.
- MEMORY_LIMITATION: För att skapa radgrupper med 1 miljon rader, krävs en viss mängd arbetsminne av motorn. När tillgängligt minne för inläsning sessionen är mindre än det nödvändiga arbetsminnet, hämta tidigt radgrupper bort. I följande avsnitt beskrivs hur du uppskatta minne som krävs och allokera mer minne.
- DICTIONARY_SIZE: Trim därför anger att radgrupps trimning inträffat eftersom det har minst en strängkolumn med bred och/eller hög kardinalitet strängar. Ordlistan storlek är begränsad till 16 MB i minnet och när den här gränsen har nåtts gruppen raden har komprimerats. Överväg att isolera problematiska kolumnen till en separat tabell om du kör i den här situationen.

## <a name="how-to-estimate-memory-requirements"></a>Hur du beräkna minneskrav

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

Det maximala begärt minnet att komprimera en radgrupps är ungefär

- 72 MB +
- \#rader \* \#kolumner \* 8 byte +
- \#rader \* \#kort-sträng-kolumner \* 32 byte +
- \#lång sträng staplar \* 16 MB för komprimering

där kort-sträng-kolumner använder strängdatatyper av < = 32 byte och lång sträng staplar Använd strängdatatyper > 32 byte.

Lång sträng har komprimerats med en komprimeringsmetod som utformats för att komprimera text. Den här komprimeringsmetoden använder en *ordlista* att lagra textmönster. Den maximala storleken för en ordlista är 16 MB. Det finns endast en ordlista för varje lång sträng-kolumn i radgrupps.

En detaljerad beskrivning av columnstore minneskrav finns i videon [Azure SQL Data Warehouse skalning: konfiguration och vägledning](https://myignite.microsoft.com/videos/14822).

## <a name="ways-to-reduce-memory-requirements"></a>Sätt att minska minneskrav

Använd följande metoder för att minska minneskrav för att komprimera rowgroups columnstore-index.

### <a name="use-fewer-columns"></a>Använd färre kolumner
Om möjligt utforma tabell med färre kolumner. När en radgrupps komprimeras i columnstore komprimerar columnstore-indexet varje kolumn segment separat. Därför öka minneskrav att komprimera en radgrupps som antalet kolumner ökar.


### <a name="use-fewer-string-columns"></a>Använd färre sträng kolumner
Kolumner i strängdatatyper kräver mer minne än numeriska datatyperna och date. Överväg att ta bort sträng kolumner från faktatabeller och placera dem i mindre dimensionstabeller för att minska minneskrav.

Ytterligare minneskrav för sträng komprimering:

- Strängdatatyper högst 32 tecken kan kräva 32 ytterligare byte per värde.
- Strängdatatyper med mer än 32 tecken komprimeras med ordlistan metoder.  Varje kolumn i radgrupps kan kräva upp till ytterligare 16 MB för att skapa ordlistan.

### <a name="avoid-over-partitioning"></a>Undvika över partitionering

Columnstore-index skapa en eller flera rowgroups per partition. I SQL Data Warehouse växer antalet partitioner snabbt eftersom data distribueras och varje distribution är partitionerad. Om tabellen innehåller för många partitioner, kanske det inte tillräckligt många rader för att fylla i rowgroups. Bristen på rader skapar inte minnesbelastning under komprimeringen, men det leder till rowgroups som inte uppnår bästa frågeprestanda för columnstore.

En annan orsak att undvika över partitionering är finns det en minne omkostnader för att läsa in rader i ett columnstore-index för en partitionerad tabell. Under inläsningen av en kan många partitioner få de inkommande rader som är kvar i minnet tills varje partition har tillräckligt med rader som ska komprimeras. Med för många partitioner skapar ytterligare minnesbelastning.

### <a name="simplify-the-load-query"></a>Förenkla frågan belastning

Databasen delar minnestilldelningen för en fråga bland alla operatorer i frågan. När en fråga belastningen har komplexa sorteringar och kopplingar, minskas minne för komprimering.

Utforma belastningen fråga att fokusera på att frågan läses in. Om du behöver köra omvandlingar på data köra dem separat från frågan belastningen. Till exempel mellanlagra data i en heap-tabell, kör omvandlingarna och sedan ladda mellanlagringstabellen i columnstore-indexet. Du kan också läsa in data först och sedan använda MPP-systemet för att omvandla data.

### <a name="adjust-maxdop"></a>Adjust MAXDOP

Varje distributionsplats komprimerar rowgroups till columnstore parallellt om det inte finns fler än en CPU-kärnor per distribution. Parallellitet kräver ytterligare minne, vilket kan leda till minnesbelastning och radgrupps trimning.

Du kan använda frågetipset MAXDOP för att minska minnesbelastning för att tvinga Inläsningsåtgärd för seriell läget i varje distribution.

```
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQUota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Sätt att allokera mer minne

DWU storlek och användaren resursklassen tillsammans bestämmer du hur mycket minne som är tillgängligt för en användarfråga. För att öka minnestilldelningen för en load-fråga kan du öka antalet dwu: er eller öka resursklassen.

- Om du vill öka de dwu: er Se [hur skala prestanda?](quickstart-scale-compute-portal.md)
- Om du vill ändra resursklassen för en fråga, se [ändra ett exempel på användaren resurs klassen](resource-classes-for-workload-management.md#assigning-resource-classes).

Till exempel på DWU 100 kan användare i resursklassen smallrc använda 100 MB minne för varje distribution. Mer information, se [samtidighet i SQL Data Warehouse](resource-classes-for-workload-management.md).

Anta att du kontrollera att du behöver 700 MB minne för att få hög kvalitet radgrupps storlekar. De här exemplen visar hur du kan köra frågan belastningen med tillräckligt med minne.

- Använder DWU 1000 och mediumrc, är din minnestilldelningen 800 MB
- Använder DWU 600 och largerc, är din minnestilldelningen 800 MB.


## <a name="next-steps"></a>Nästa steg

Du hittar fler sätt att förbättra prestanda i SQL Data Warehouse den [översikt över prestanda](sql-data-warehouse-overview-manage-user-queries.md).

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->
