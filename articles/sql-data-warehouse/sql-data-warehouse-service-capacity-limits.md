---
title: Kapacitetsbegränsningar – Azure SQL Data Warehouse | Microsoft Docs
description: Högsta värden som tillåts för olika komponenter i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: sachinpMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 11/14/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: b37f16ab914fe4062bc9720ae9cc0139c573fb93
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154283"
---
# <a name="sql-data-warehouse-capacity-limits"></a>SQL Data Warehouse kapacitetsbegränsningar
Högsta värden som tillåts för olika komponenter i Azure SQL Data Warehouse.

## <a name="workload-management"></a>Arbetsbelastningshantering
| Category | Beskrivning | Maximal |
|:--- |:--- |:--- |
| [Informationslagerenheter (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Max DWU för en enda SQL Data Warehouse | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Informationslagerenheter (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Standard DTU per server |54,000<br></br>Varje SQLServer (t.ex, myserver.database.windows.net) har som standard en DTU-kvot på 54 000, vilket gör att upp till DW6000c. Kvoten är helt enkelt en säkerhetsgräns. Du kan öka din kvot genom [skapar en supportbegäran](sql-data-warehouse-get-started-create-support-ticket.md) och välja *kvot* som typ av begäran.  Att beräkna dina DTU behöver, multiplicera 7,5 med det totala antalet DWU behövs eller 9.0 att multiplicera den totala cDWU som behövs. Exempel:<br></br>DW6000 x 7.5 = 45 000 dtu: er<br></br>DW6000c x 9.0 = 54 000 dtu: er.<br></br>Du kan visa din aktuella DTU-förbrukning från SQL server-alternativ i portalen. Både pausade och inte pausade databaser räknas i förhållande till DTU-kvoten. |
| Databasanslutning |Högsta tillåtna antal samtidiga öppna sessioner |1024<br/><br/>Antalet samtidiga öppna sessioner kan variera beroende på den valda DWU. DWU500c och öppna sessioner ovan support högst 1024. DWU400c och stöder en maximal samtidiga öppna session högst 512 nedan. Observera att det finns gränser för antalet frågor som kan köra samtidigt. När gränsen för samtidighet har överskridits, begäran som hamnar i en intern kö där det väntar på att bearbetas. |
| Databasanslutning |Högsta mängd minne för förberedda satser |20 MB |
| [Arbetsbelastningshantering](resource-classes-for-workload-management.md) |Maximalt antal samtidiga frågor |128<br/><br/> SQL Data Warehouse kan köra maximalt 128 samtidiga frågor och köer återstående frågor.<br/><br/>Antalet samtidiga frågor kan minska när användare har tilldelats högre resursklasser eller när SQL Data Warehouse har en lägre [informationslagerenheten](memory-and-concurrency-limits.md) inställningen. Vissa frågor, t.ex DMV frågor tillåts alltid att köra och gör inte påverkar gräns för samtidiga frågor. Mer information om samtidiga frågekörning finns i den [samtidighet maximum](memory-and-concurrency-limits.md#concurrency-maximums) artikeln. |
| [tempdb](sql-data-warehouse-tables-temporary.md) |Maximal GB |399 GB per DW100. Vid DWU1000 storlek tempdb därför till 3,99 TB. |

## <a name="database-objects"></a>Databasobjekt
| Category | Beskrivning | Maximal |
|:--- |:--- |:--- |
| Databas |Maxstorlek | Gen1: 240 TB komprimerat på disken. Här är oberoende av tempdb-eller log och därför tomrummet är dedikerad till permanent tabeller.  Grupperade columnstore-komprimering uppskattas till 5 X.  Den här komprimeringen låter databasen växa till cirka 1 PB när alla tabeller är grupperade (tabelltyp för standard). <br/><br/> Gen2: 240TB för rowstore och obegränsad lagring för columnstore-tabeller |
| Tabell |Maxstorlek |60 TB komprimerat på disk |
| Tabell |Tabeller per databas | 100 000 |
| Tabell |Kolumner per tabell |1 024 kolumner |
| Tabell |Byte per kolumn |Beroende på kolumnen [datatypen](sql-data-warehouse-tables-data-types.md). Gränsen är 8000 för datatyperna char, 4000 för nvarchar eller 2 GB för MAX-datatyper. |
| Tabell |Byte per rad, definierad storlek |8 060 byte<br/><br/>Antalet byte per rad beräknas på samma sätt som för SQL Server med sidan komprimering. Som SQL Server, SQL Data Warehouse stöder rad spill lagring, vilket gör att **variabel längd kolumner** att överföras utanför raden. När variabel längd rader skickas utanför raden, lagras endast 24 byte-roten i den huvudsakliga posten. Mer information finns i [rad spill Data överstiger 8 KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| Tabell |Partitioner per tabell |15 000<br/><br/>För hög prestanda, rekommenderar vi att minimera antalet partitioner måste du även ge stöd åt dina affärsbehov. När antalet partitioner växer, ökar CPU-användningen för Data Definition Language (DDL) och Data Manipulation Language (DML) och gör långsammare. |
| Tabell |Tecken per partition gränsvärdet. |4000 |
| Index |Icke-grupperade index per tabell. |50<br/><br/>Gäller endast rowstore-tabeller. |
| Index |Grupperade index per tabell. |1<br><br/>Gäller för både rowstore och columnstore-tabeller. |
| Index |Indexnyckelstorleken. |900 byte.<br/><br/>Gäller endast rowstore-index.<br/><br/>Du kan skapa index i varchar-kolumner med en maximal storlek på mer än 900 byte om befintliga data i kolumnerna som inte överskrida 900 byte när indexet har skapats. Dock Infoga senare eller uppdateringsåtgärder för kolumner som orsakar den totala storleken överskrida 900 byte misslyckas. |
| Index |Nyckelkolumner per index. |16<br/><br/>Gäller endast rowstore-index. Grupperade columnstore-index med alla kolumnerna. |
| Statistik |Storleken på de kombinerade kolumnvärdena. |900 byte. |
| Statistik |Kolumner per statistik objekt. |32 |
| Statistik |Statistik skapas för kolumner per tabell. |30,000 |
| Lagrade procedurer |Högsta nivåer av inkapsling. |8 |
| Visa |Kolumner per vy |1,024 |

## <a name="loads"></a>Inläsningar
| Category | Beskrivning | Maximal |
|:--- |:--- |:--- |
| Polybase-inläsningar |MB per rad |1<br/><br/>Polybase läser in rader som är mindre än 1 MB.<br/><br/> |

## <a name="queries"></a>Frågor
| Category | Beskrivning | Maximal |
|:--- |:--- |:--- |
| Fråga |Köade frågor om användartabeller. |1000 |
| Fråga |Samtidiga frågor om systemvyer. |100 |
| Fråga |Köade frågor på systemvyer |1000 |
| Fråga |Maximal parametrar |2098 |
| Batch |Maximal storlek |65,536*4096 |
| Välj resultat |Kolumner per rad |4096<br/><br/>Du kan aldrig ha högst 4 096 kolumner per rad i väljer resultatet. Det är inte säkert att du alltid har 4096. Om frågeplanen kräver en tillfällig tabell, kan 1024 kolumner per tabell maximala gälla. |
| VÄLJ |Kapslade underfrågor |32<br/><br/>Du kan aldrig ha fler än 32 kapslade underfrågor i en SELECT-instruktion. Det är inte säkert att du alltid har 32. Exempelvis kan kan en koppling medföra en underfråga i frågeplanen. Antalet underfrågor kan också begränsas av tillgängligt minne. |
| VÄLJ |Kolumner per koppling |1 024 kolumner<br/><br/>Du kan aldrig ha högst 1 024 kolumner i KOPPLINGEN. Det är inte säkert att du alltid har 1024. Om koppling planen kräver en tillfällig tabell med fler kolumner än kopplingsresultatet, gäller 1024-gränsen för den temporära tabellen. |
| VÄLJ |Byte per GROUP BY-kolumner. |8060<br/><br/>Kolumner i GROUP BY-satsen kan ha maximalt 8 060 byte. |
| VÄLJ |Byte per ORDER BY kolumner |8 060 byte<br/><br/>Kolumner i ORDER BY-satsen kan ha maximalt 8 060 byte |
| Identifierare per instruktionen |Antal refererade identifierare |65,535<br/><br/>SQL Data Warehouse begränsar antalet identifierare som kan finnas i ett enda uttryck i en fråga. Som överstiger det här antalet resultatet i SQL Server-felet 8632. Mer information finns i [internt fel: En uttryckstjänstgräns har nåtts](https://support.microsoft.com/en-us/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a). |
| Stränglitteraler | Antal stränglitteraler i en instruktion | 20,000 <br/><br/>SQL Data Warehouse begränsar antalet strängkonstanter i ett enda uttryck i en fråga. Som överstiger det här antalet resultatet i SQL Server-felet 8632.|

## <a name="metadata"></a>Metadata
| Systemvy | Maximalt antal rader |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10 000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Totalt antal DMS arbetare för de senaste 1000 SQL-begäranden. |
| sys.dm_pdw_errors |10 000 |
| sys.dm_pdw_exec_requests |10 000 |
| sys.dm_pdw_exec_sessions |10 000 |
| sys.dm_pdw_request_steps |Totalt antal steg för de senaste 1000 SQL-begäranden som lagras i sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10 000 |
| sys.dm_pdw_sql_requests |De senaste 1000 SQL begäranden som lagras i sys.dm_pdw_exec_requests. |

## <a name="next-steps"></a>Nästa steg
Rekommendationer om hur du använder SQL Data Warehouse finns i den [facit blad](cheat-sheet.md).
