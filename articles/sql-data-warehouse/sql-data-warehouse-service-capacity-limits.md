---
title: Kapacitetsbegränsningar - Azure SQL Data Warehouse | Microsoft Docs
description: Högsta värden som tillåts för olika komponenter i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: antvgski
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a0646bad9f440fc1e7d0bbdfae5bd2a23156c52f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="sql-data-warehouse-capacity-limits"></a>SQL Data Warehouse kapacitetsbegränsningar
Högsta värden som tillåts för olika komponenter i Azure SQL Data Warehouse.

## <a name="workload-management"></a>Arbetsbelastningshantering
| Kategori | Beskrivning | Maximal |
|:--- |:--- |:--- |
| [Informationslagerenheter (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Max DWU för en enskild SQL Data Warehouse | Optimerad för elasticitet [prestandanivån](memory-and-concurrency-limits.md#performance-tiers): DW6000<br></br>Optimerad för beräkning [prestandanivån](memory-and-concurrency-limits.md#performance-tiers): DW30000c |
| [Informationslagerenheter (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Standard DTU per server |54,000<br></br>Varje SQLServer (till exempel myserver.database.windows.net) har en DTU-kvot för 54 000, vilket gör att upp till DW6000c som standard. Kvoten är helt enkelt en säkerhetsgräns. Du kan öka din kvot genom [skapat ett supportärende](sql-data-warehouse-get-started-create-support-ticket.md) och välja *kvot* som den Begärandetypen.  Att beräkna dina DTU behöver, multiplicerar 7.5 med det totala antalet DWU behövs eller 9.0 att multiplicera den totala cDWU som behövs. Exempel:<br></br>DW6000 x 7.5 = 45,000 dtu: er<br></br>DW600c x 9.0 = 54 000 dtu: er.<br></br>Du kan visa din aktuella DTU-förbrukning från SQL server-alternativ i portalen. Både pausade och inte pausade databaser räknas i förhållande till DTU-kvoten. |
| Databasanslutning |Öppna samtidiga sessioner |1024<br/><br/>Var och en av 1024 aktiva sessioner kan skicka begäranden till en SQL Data Warehouse-databas på samma gång. Observera att det finns begränsningar för antalet frågor som kan köras samtidigt. När samtidighet gränsen överskrids, förfrågan som skickas till en intern kö där det väntar på att bearbetas. |
| Databasanslutning |Största minnesstorlek för förberedda instruktioner |20 MB |
| [Arbetsbelastningshantering](resource-classes-for-workload-management.md) |Maximalt antal samtidiga frågor |32<br/><br/> Som standard kan SQL Data Warehouse köra maximalt 32 samtidiga frågor och köer återstående frågor.<br/><br/>Minska antalet samtidiga frågor när användare tilldelas högre resursklasser eller när SQL Data Warehouse har en lägre [data warehouse enhet](memory-and-concurrency-limits.md) inställningen. Några frågor som DMV frågor är alltid att kunna köras. |
| [TempDB](sql-data-warehouse-tables-temporary.md) |Maximal GB |399 GB per DW100. Vid DWU1000 storlek tempdb därför till 3,99 TB. |

## <a name="database-objects"></a>objekt i databasen
| Kategori | Beskrivning | Maximal |
|:--- |:--- |:--- |
| Databas |Maxstorlek |240 TB komprimerat på disk<br/><br/>Här är oberoende av utrymme i tempdb- eller loggfil och därför detta utrymme har reserverats för permanenta tabeller.  Det grupperade columnstore-komprimering uppskattas till 5 X.  Den här komprimeringen låter databasen växa till ungefär 1 PB när alla tabeller som är grupperade columnstore (standard tabelltyp). |
| Tabell |Maxstorlek |60 TB komprimerat på disk |
| Tabell |Tabeller per databas |10 000 |
| Tabell |Kolumner per tabell |1 024 kolumner |
| Tabell |Byte per kolumn |Beroende på kolumnen [datatyp](sql-data-warehouse-tables-data-types.md). Gränsen är 8000 för datatyperna char, 4000 för nvarchar eller 2 GB för MAX-datatyper. |
| Tabell |Byte per rad, definierad storlek |8 060 byte<br/><br/>Antalet byte per rad beräknas på samma sätt som för SQL Server med sidan komprimering. T.ex. SQL Server SQL Data Warehouse stöder raden spill lagringen, vilket gör att **kolumner med variabel längd** ska kunna skickas utanför raden. När variabel längd rader skickas utanför raden, lagras endast 24 byte roten i den huvudsakliga posten. Mer information finns i [raden spill Data mer än 8 KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| Tabell |Partitioner per tabell |15,000<br/><br/>För hög prestanda, rekommenderar vi att minimera antalet partitioner måste du medan fortfarande stöd för dina affärsbehov. När antalet partitioner växer växer CPU-användningen för Data Definition Language (DDL) och Data Manipulation Language (DML) och orsakar långsammare. |
| Tabell |Tecken per partition gränsvärdet. |4000 |
| Index |Icke-grupperade index per tabell. |50<br/><br/>Gäller endast rowstore-tabeller. |
| Index |Grupperade index per tabell. |1<br><br/>Gäller för både rowstore och columnstore-tabeller. |
| Index |Indexnyckelstorleken. |900 byte.<br/><br/>Gäller endast rowstore-index.<br/><br/>Index för varchar kolumner med en maximal storlek på mer än 900 byte kan skapas om befintliga data i kolumnerna som inte överskrida 900 byte när indexet skapas. Dock Infoga senare eller uppdateringsåtgärder på de kolumner som orsakar den totala storleken överskrida 900 byte misslyckas. |
| Index |Nyckelkolumner per index. |16<br/><br/>Gäller endast rowstore-index. Grupperade columnstore-index omfattar alla kolumner. |
| Statistik |Storleken på kombinerade kolumnvärdena. |900 byte. |
| Statistik |Kolumner per statistik objekt. |32 |
| Statistik |Statistik skapas för kolumner per tabell. |30,000 |
| Lagrade procedurer |Maximal kapslingsnivåer. |8 |
| Visa |Kolumner per vy |1,024 |

## <a name="loads"></a>Belastningar
| Kategori | Beskrivning | Maximal |
|:--- |:--- |:--- |
| Polybase belastningar |MB per rad |1<br/><br/>Polybase läser in endast för rader som är mindre än 1 MB och det går inte att läsa in VARCHAR(MAX), NVARCHAR(MAX) eller VARBINARY(MAX).<br/><br/> |

## <a name="queries"></a>Frågor
| Kategori | Beskrivning | Maximal |
|:--- |:--- |:--- |
| Fråga |Köade frågor om användartabeller. |1000 |
| Fråga |Samtidiga frågor om systemvyer. |100 |
| Fråga |Köade frågor för systemvyer |1000 |
| Fråga |Maximal parametrar |2098 |
| Batch |Maximal storlek |65,536*4096 |
| Välj resultat |Kolumner per rad |4096<br/><br/>Du kan aldrig ha fler än 4096 kolumner per rad i väljer resultatet. Det är inte säkert att du alltid har 4096. Om frågeplanen kräver en tillfällig tabell, kanske på 1 024 kolumner per tabell maximala gäller. |
| Välj |Kapslade underfrågor |32<br/><br/>Du kan aldrig ha fler än 32 kapslade underfrågor i en SELECT-instruktion. Det är inte säkert att du alltid har 32. Exempelvis kan en koppling införa en underfråga i frågeplanen. Antalet underfrågor kan också begränsas av tillgängligt minne. |
| Välj |Kolumner per koppling |1 024 kolumner<br/><br/>Du kan aldrig ha fler än 1024 kolumner i KOPPLINGEN. Det är inte säkert att du alltid har 1024. Om koppling planen kräver en tillfällig tabell med fler kolumner än kopplingsresultatet, gäller 1024 gränsen den temporära tabellen. |
| Välj |Antal byte per GROUP BY-kolumner. |8060<br/><br/>Kolumner i GROUP BY-satsen kan ha högst 8 060 byte. |
| Välj |Byte per ORDER BY kolumner |8 060 byte<br/><br/>Kolumner i ORDER BY-satsen kan ha högst 8 060 byte |
| Identifierare per instruktionen |Antalet refererade identifierare |65,535<br/><br/>SQL Data Warehouse begränsar antalet identifierare som kan finnas i ett enda uttryck i en fråga. Överstiger det här antalet resultat i SQL Server-fel 8632. Mer information finns i [internt fel: en uttryckstjänstgräns har nåtts] [internt fel: en uttryckstjänstgräns har nåtts]. |
| Stränglitteraler | Antal stränglitteraler i ett uttryck | 20,000 <br/><br/>SQL Data Warehouse begränsar antalet strängkonstanter i ett enda uttryck i en fråga. Överstiger det här antalet resultat i SQL Server-fel 8632.|

## <a name="metadata"></a>Metadata
| Visa system | Max antal rader |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10 000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Totalt antal DMS arbetare för senaste 1000 SQL-begäranden. |
| sys.dm_pdw_errors |10 000 |
| sys.dm_pdw_exec_requests |10 000 |
| sys.dm_pdw_exec_sessions |10 000 |
| sys.dm_pdw_request_steps |Totalt antal steg för de senaste 1000 SQL-begäranden som lagras i sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10 000 |
| sys.dm_pdw_sql_requests |De senaste 1000 SQL begäranden som lagras i sys.dm_pdw_exec_requests. |

## <a name="next-steps"></a>Nästa steg
Rekommendationer för att använda SQL Data Warehouse, finns det [Cheat blad](cheat-sheet.md).
