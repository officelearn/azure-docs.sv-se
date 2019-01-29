---
title: Förbättra columnstore-index prestanda – Azure SQL Data Warehouse | Microsoft Docs
description: Minska minneskrav eller öka det tillgängliga minnet för att maximera antalet rader som ett columnstore-index komprimeras till varje radgrupp.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: de7cc0e67960edf95ace67808ffc677b57a46dab
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55097001"
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Maximera radgrupps kvalitet för columnstore

Radgrupps kvalitet bestäms av antalet rader i en radgrupp. Minska minneskrav eller öka det tillgängliga minnet för att maximera antalet rader som ett columnstore-index komprimeras till varje radgrupp.  Du kan använda dessa metoder för att förbättra komprimeringsgrad och frågeprestanda för columnstore-index.

## <a name="why-the-rowgroup-size-matters"></a>Radgrupps-storlek
Eftersom ett columnstore-index söker igenom en tabell genom att skanna kolumnsegment med enskilda radgrupper, förbättrar ökar antalet rader i varje radgrupp frågeprestanda. När radgrupper har ett stort antal rader, förbättrar datakomprimering vilket innebär att det är mindre data som lästs från disk.

Läs mer om radgrupper [Guide för Kolumnlagringsindex](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Målstorlek för radgrupper
Målet är att maximera antalet rader per radgrupp i ett columnstore-index för bästa frågeprestanda. En radgrupp kan ha högst 1 048 576 rader. Det är Okej att inte låta det maximala antalet rader per radgrupp. Columnstore-index uppnå goda prestanda när radgrupper har minst 100 000 rader.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Radgrupper kan hämta tas bort under komprimering

Under bulk belastning eller columnstore-indexet återskapas, ibland det finns inte tillräckligt med minne för att komprimera alla rader för varje radgrupp. När det finns minnesbelastning, trimma kolumnlagringsindex radgrupps-storlekar så komprimering till columnstore kan lyckas. 

När det finns inte tillräckligt med minne för att komprimera minst 10 000 rader till varje radgrupp, genererar ett fel i SQL Data Warehouse.

Läs mer på massinläsning [massinläsning till ett grupperat columnstore-index](https://msdn.microsoft.com/library/dn935008.aspx#Bulk load into a clustered columnstore index).

## <a name="how-to-monitor-rowgroup-quality"></a>Så här övervakar du radgrupps kvalitet

Det finns en DMV (sys.dm_pdw_nodes_db_column_store_row_group_physical_stats) som exponerar användbar information, till exempel antalet rader i radgrupper och ta bort om det var trimmar orsaken. Du kan skapa följande vy som ett praktiskt sätt att fråga efter den här DMV för att få information om hur radgrupp.

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

Trim_reason_desc meddelar om radgrupps har tas bort (trim_reason_desc = NO_TRIM innebär det fanns ingen beskrivande text och radgrupp är av hög kvalitet). Följande trim anger för tidig Trimning av radgrupps:
- BULKLOAD: Den här trim orsaken används när inkommande batch med rader för arbetsbelastning har mindre än 1 miljon rader. Motorn skapar komprimerade radgrupper om det är större än 100 000 rader infogas (i stället för att infoga i arkivet delta) men anger trim orsaken till BULKLOAD. Överväg att öka belastningen batch fönstret för att ackumuleras fler rader i det här scenariot. Dessutom att omvärdera ditt partitioneringsschema och kontrollera att det inte är för detaljerad eftersom radgrupper inte kan omfatta partitionsgränser.
- MEMORY_LIMITATION: Om du vill skapa radgrupper med 1 miljon rader, krävs en viss mängd arbetsminne av motorn. När tillgängligt minne på den läser in sessionen är mindre än det nödvändiga arbetsminnet, radgrupper hämta tidigt tas bort. I följande avsnitt beskrivs hur du beräkna minne som krävs och tilldela mer minne.
- DICTIONARY_SIZE: Därför trim anger att radgrupps trimning inträffat eftersom det inte fanns minst en strängkolumn med bred och/eller hög kardinalitet strängar. Ordlista storlek är begränsad till 16 MB i minnet och komprimeras när den här gränsen har nåtts i radgrupp. Om du kör i den här situationen, Överväg att isolera den problematiska kolumnen i en separat tabell.

## <a name="how-to-estimate-memory-requirements"></a>Så här att uppskatta minneskrav

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

Det maximala krävs minnet för att komprimera en radgrupp är ungefär

- 72 MB +
- \#rader \* \#kolumner \* 8 byte +
- \#rader \* \#kort strängkolumner \* 32 byte +
- \#långa strängkolumner \* 16 MB för komprimering

där kort strängkolumner använder strängdatatyper av < = 32 byte och långa strängkolumner Använd strängdatatyper > 32 byte.

Lång sträng komprimeras med en komprimeringsmetod som utformats för att komprimera text. Den här komprimeringsmetoden använder en *ordlista* att lagra textmönster. Den maximala storleken för en ordlista är 16 MB. Det finns bara en ordlista för varje lång sträng-kolumn i radgrupp.

En detaljerad beskrivning av columnstore minneskrav finns i videon [skalning i Azure SQL Data Warehouse: konfiguration och vägledning](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Så här minskar minneskrav

Använd följande metoder för att minska minneskraven för komprimerar radgrupper till columnstore-index.

### <a name="use-fewer-columns"></a>Använda färre kolumner
Om det är möjligt, utforma tabell med färre kolumner. När en radgrupp komprimeras till columnstore, komprimeras columnstore-indexet varje kolumn segment separat. Därför ökar minneskrav att komprimera en radgrupp när antalet kolumner ökar.


### <a name="use-fewer-string-columns"></a>Använda färre strängkolumner
Kolumner med strängdatatyper kräver mer minne än numeriska datatyperna och date. Överväg att ta bort strängkolumner från faktatabeller och placera dem i mindre dimensionstabeller för att minska minneskrav.

Ytterligare minneskraven för sträng-komprimering:

- Strängdatatyper högst 32 tecken kan kräva 32 ytterligare byte per värde.
- Datatyper med mer än 32 tecken är komprimerade med ordlistan metoder.  Varje kolumn i radgrupps kan kräva upp till ytterligare 16 MB för att skapa ordlistan.

### <a name="avoid-over-partitioning"></a>Undvika över partitionering

Kolumnlagringsindex skapa en eller flera radgrupper per partition. I SQL Data Warehouse växer antalet partitioner snabbt eftersom data fördelas och varje distribution är partitionerad. Om tabellen innehåller för många partitioner, kanske det inte finns tillräckligt med rader att fylla i radgrupper. Bristen på rader skapar inte minnesbelastning under komprimeringen, men det leder till radgrupper som inte uppnå bästa columnstore frågeprestanda.

En annan orsak att undvika över partitionering är att det finns en minne användning för att läsa in rader i ett columnstore-index för en partitionerad tabell. Under inläsningen av en många partitioner kan ta emot inkommande rader, som sparas i minnet tills varje partition har tillräckligt med rader som ska komprimeras. För många partitioner skapar ytterligare minnesbelastning.

### <a name="simplify-the-load-query"></a>Förenkla frågan belastning

Databasen delar minnestilldelningen för en fråga bland alla operatörer i frågan. När en belastning fråga har komplexa typer och kopplingar, minskas minne för komprimering.

Utforma load-fråga för att fokusera på att läsa in frågan. Om du behöver köra omvandlingar av data, kör dem separat från frågan belastningen. Till exempel mellanlagra data i en heap-tabell, kör omvandlingarna och sedan läsa in mellanlagringstabellen i columnstore-indexet. Du kan också läsa in data först och sedan använda MPP-systemet för att transformera data.

### <a name="adjust-maxdop"></a>Justera MAXDOP

Varje distribution komprimerar radgrupper till columnstore parallellt när det finns fler än en CPU-kärna per distribution. Parallellitet kräver ytterligare minne, vilket kan leda till minnesbelastning och ta bort radgrupp.

För att minska minnesbelastning kan använda du frågetipset MAXDOP för att tvinga inläsningen körs med seriell inom varje distribution.

```
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQUota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Sätt att allokera mer minne

DWU-storleken och den användare resursklassen tillsammans avgör du hur mycket minne som är tillgängliga för en användare-frågor. För att öka minnestilldelningen för en load-fråga kan du öka antalet dwu: er eller öka resursklassen.

- För att öka de dwu: er, se [hur skala prestanda?](quickstart-scale-compute-portal.md)
- Om du vill ändra resursklass för en fråga, se [ändra en klass för användaren resursexempel](resource-classes-for-workload-management.md#change-a-users-resource-class).

På 100 DWU använder en användare i smallrc resursklass till exempel 100 MB minne för varje distribution. Mer information finns i [samtidighet i SQL Data Warehouse](resource-classes-for-workload-management.md).

Anta att du bestämma att du behöver 700 MB minne att få hög kvalitet radgrupps storlekar. De här exemplen visar hur du kan köra frågan belastning med tillräckligt med minne.

- Använda DWU 1000 och mediumrc kan är din minnestilldelningen 800 MB
- Använda DWU 600 och largerc kan är din minnestilldelningen 800 MB.


## <a name="next-steps"></a>Nästa steg

Du hittar fler sätt att förbättra prestanda i SQL Data Warehouse den [prestandaöversikt](sql-data-warehouse-overview-manage-user-queries.md).

