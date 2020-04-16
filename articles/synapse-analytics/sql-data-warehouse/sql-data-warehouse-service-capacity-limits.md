---
title: Kapacitetsgränser - Azure Synapse Analytics (tidigare SQL DW)
description: Högsta tillåtna värden för olika komponenter i Synapse SQL-pool i Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/19/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: fbdf0fda51ae35fac4f3f8ae45bfcd788fc406ae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414008"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-capacity-limits"></a>Kapacitetsbegränsningar för Azure Synapse Analytics (tidigare SQL DW)

Högsta tillåtna värden för olika komponenter i Azure Synapse.

## <a name="workload-management"></a>Arbetsbelastningshantering

| Kategori | Beskrivning | Maximal |
|:--- |:--- |:--- |
| [Data warehouseenheter (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Max DWU för en enda SQL-poolenhet (datalagerenhet) | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Data warehouseenheter (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Standard-DTU per server |54,000<br></br>Som standard har varje SQL-server (till exempel myserver.database.windows.net) en DTU-kvot på 54 000, vilket gör att upp till DW5000c. Kvoten är helt enkelt en säkerhetsgräns. Du kan öka din kvot genom [att skapa en supportbiljett](sql-data-warehouse-get-started-create-support-ticket.md) och välja *Kvot* som förfrågestyp.  För att beräkna dina DTU-behov multiplicerar du 7,5 med den totala DWU som behövs, eller multiplicerar 9,5 med den totala cDWU som behövs. Ett exempel:<br></br>DW6000 x 7,5 = 45 000 DTUs<br></br>DW5000c x 9,5 = 47 500 DU:er.<br></br>Du kan visa din nuvarande DTU-förbrukning från SQL-serveralternativet i portalen. Både pausade och inte pausade databaser räknas i förhållande till DTU-kvoten. |
| Databasanslutning |Maximalt antal öppna sessioner på samma sätt |1024<br/><br/>Antalet samtidiga öppna sessioner varierar beroende på den valda DWU. DWU600c och högre stöder maximalt 1024 öppna sessioner. DWU500c och lägre, stödja en maximal samtidig öppen session gräns på 512. Observera att det finns gränser för antalet frågor som kan köras samtidigt. När samtidighetsgränsen överskrids går begäran till en intern kö där den väntar på att bearbetas. |
| Databasanslutning |Maximalt minne för förberedda satser |20 MB |
| [Arbetsbelastningshantering](resource-classes-for-workload-management.md) |Maximala samtidiga frågor |128<br/><br/>  Högst 128 samtidiga frågor körs och återstående frågor kommer att köas.<br/><br/>Antalet samtidiga frågor kan minska när användare tilldelas högre resursklasser eller när inställningen [för informationslagerenheter](memory-concurrency-limits.md) har sänkts. Vissa frågor, till exempel DMV-frågor, tillåts alltid att köra och påverkar inte den samtidiga frågegränsen. Mer information om samtidig körning av frågor finns i artikeln [om samtidiga maxtal.](memory-concurrency-limits.md) |
| [Tempdb](sql-data-warehouse-tables-temporary.md) |Högsta GB |399 GB per DW100c. Därför på DWU1000c, tempdb är storlek till 3,99 TB. |
||||

## <a name="database-objects"></a>Databasobjekt

| Kategori | Beskrivning | Maximal |
|:--- |:--- |:--- |
| Databas |Max storlek | Gen1: 240 TB komprimerad på disk. Detta utrymme är oberoende av tempdb eller loggutrymme, och därför är detta utrymme dedikerat till permanenta tabeller.  Klustrade columnstore-komprimering uppskattas till 5X.  Med den här komprimeringen kan databasen växa till ungefär 1 PB när alla tabeller är grupperade columnstore (standardtabelltypen). <br/><br/> Gen2: Obegränsad lagring för columnstore-tabeller.  Rowstore-delen av databasen är fortfarande begränsad till 240 TB komprimerad på disken. |
| Tabell |Max storlek |Obegränsad storlek för columnstore-tabeller. <br>60 TB för rowstore-tabeller komprimerade på disk. |
| Tabell |Tabeller per databas | 100 000 |
| Tabell |Kolumner per tabell |1024 kolumner |
| Tabell |Byte per kolumn |Beroende på [kolumndatatyp](sql-data-warehouse-tables-data-types.md). Gräns är 8000 för teckendatatyper, 4000 för nvarchar eller 2 GB för MAX-datatyper. |
| Tabell |Byte per rad, definierad storlek |8060 byte<br/><br/>Antalet byte per rad beräknas på samma sätt som för SQL Server med sidkomprimering. Precis som SQL Server stöds fjärrspilllagring, vilket gör att kolumner med **variabel längd** kan skjutas bort från raden. När rader med variabel längd skjuts bort från raden lagras endast 24-byterot i huvudposten. Mer information finns i [Rad-spilldata som överstiger 8 KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| Tabell |Partitioner per tabell |15 000<br/><br/>För hög prestanda rekommenderar vi att du minimerar antalet partitioner du behöver samtidigt som du stöder dina affärskrav. I takt med att antalet partitioner ökar ökar belastningen för DDL-åtgärder (Data Definition Language) och DML-åtgärder (Data Manipulation Language) och ger långsammare prestanda. |
| Tabell |Tecken per partitionsgränsvärde. |4000 |
| Index |Icke-grupperade index per tabell. |50<br/><br/>Gäller endast för radstoretabeller. |
| Index |Klustrade index per tabell. |1<br><br/>Gäller både rowstore- och columnstore-tabeller. |
| Index |Indexnyckelstorlek. |900 byte.<br/><br/>Gäller endast för rowstore-index.<br/><br/>Index på varcharkolumner med en maximal storlek på mer än 900 byte kan skapas om befintliga data i kolumnerna inte överstiger 900 byte när indexet skapas. Senare insert- eller UPDATE-åtgärder i kolumnerna som gör att den totala storleken överstiger 900 byte misslyckas dock. |
| Index |Nyckelkolumner per index. |16<br/><br/>Gäller endast för rowstore-index. Grupperade columnstore-index innehåller alla kolumner. |
| Statistik |Storleken på de kombinerade kolumnvärdena. |900 byte. |
| Statistik |Kolumner per statistikobjekt. |32 |
| Statistik |Statistik som skapats i kolumner per tabell. |30,000 |
| Lagrade procedurer |Högsta nivåer av kapsling. |8 |
| Visa |Kolumner per vy |1,024 |
||||

## <a name="loads"></a>Massor

| Kategori | Beskrivning | Maximal |
|:--- |:--- |:--- |
| Polybas laster |MB per rad |1<br/><br/>Polybase läser in rader som är mindre än 1 MB. Det går inte att läsa in LOB-datatyper i tabeller med ett klustrade columnstoreindex (CCI).<br/><br/> |
||||

## <a name="queries"></a>Frågor

| Kategori | Beskrivning | Maximal |
|:--- |:--- |:--- |
| Söka i data |Köade frågor på användartabeller. |1000 |
| Söka i data |Samtidiga frågor om systemvyer. |100 |
| Söka i data |Köade frågor om systemvyer |1000 |
| Söka i data |Maximala parametrar |2098 |
| Batch |Maximal storlek |65,536*4096 |
| VÄLJ resultat |Kolumner per rad |4096<br/><br/>Du kan aldrig ha fler än 4096 kolumner per rad i SELECT-resultatet. Det finns ingen garanti för att du alltid kan ha 4096. Om frågeplanen kräver en tillfällig tabell kan de maximala 1024 kolumnerna per tabell tillämpas. |
| VÄLJ |Kapslade underfrågor |32<br/><br/>Du kan aldrig ha fler än 32 kapslade underfrågor i en SELECT-sats. Det finns ingen garanti för att du alltid kan ha 32. En KOPPLING kan till exempel introducera en underfråga i frågeplanen. Antalet underfrågor kan också begränsas av tillgängligt minne. |
| VÄLJ |Kolumner per KOPPLING |1024 kolumner<br/><br/>Du kan aldrig ha fler än 1024 kolumner i JOIN. Det finns ingen garanti för att du alltid kan ha 1024. Om JOIN-planen kräver en tillfällig tabell med fler kolumner än JOIN-resultatet gäller gränsen 1024 för den temporära tabellen. |
| VÄLJ |Byte per GRUPP EFTER-kolumner. |8060<br/><br/>Kolumnerna i GROUP BY-satsen kan ha högst 8060 byte. |
| VÄLJ |Antal byten per ORDER EFTER kolumner |8060 byte<br/><br/>Kolumnerna i ORDER BY-satsen kan ha högst 8060 byte |
| Identifierare per sats |Antal refererade identifierare |65,535<br/><br/> Antalet identifierare som kan finnas i ett enda uttryck för en fråga är begränsat. Om du överskrider det här antalet resulterar det i SQL Server-fel 8632. Mer information finns i [Internt fel: Gränsen för uttryckstjänster har nåtts](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a). |
| Stränglitteraler | Antal stränglitteraler i ett utdrag | 20 000 <br/><br/>Antalet strängkonstanter i ett enda uttryck för en fråga är begränsat. Om du överskrider det här antalet resulterar det i SQL Server-fel 8632.|
||||

## <a name="metadata"></a>Metadata

| Systemvy | Maximala rader |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10 000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Totalt antal DMS-arbetare för de senaste 1000 SQL-begärandena. |
| sys.dm_pdw_errors |10 000 |
| sys.dm_pdw_exec_requests |10 000 |
| sys.dm_pdw_exec_sessions |10 000 |
| sys.dm_pdw_request_steps |Totalt antal steg för de senaste 1000 SQL-begäranden som lagras i sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10 000 |
| sys.dm_pdw_sql_requests |De senaste 1000 SQL-begäranden som lagras i sys.dm_pdw_exec_requests. |
|||

## <a name="next-steps"></a>Nästa steg

Rekommendationer om hur du använder Azure Synapse finns i [fuskarbladet](cheat-sheet.md).
