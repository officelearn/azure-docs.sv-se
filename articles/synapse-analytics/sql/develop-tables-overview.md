---
title: Designa tabeller med Synapse SQL
description: Introduktion till att designa tabeller i Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f1eec76d92edc97f7e4058d3afe813f0bb2aae47
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431883"
---
# <a name="design-tables-using-synapse-sql"></a>Designa tabeller med Synapse SQL

Det här dokumentet innehåller nyckelbegrepp för att utforma tabeller med SQL-pool och SQL on-demand (förhandsversion).  

[SQL on-demand (preview)](on-demand-workspace-overview.md) är en frågetjänst över data i datasjön. Den har inte lokal lagring för datainmatning. [SQL-poolen](best-practices-sql-pool.md) representerar en samling analytiska resurser som etableras när Du använder Synapse SQL. Storleken på SQL-poolen bestäms av Data Lagerenheter (DWU).

I följande tabell visas de ämnen som är relevanta för SQL-poolen jämfört med SQL på begäran:

| Hjälpavsnitt                                                        | SQL-pool | SQL på begäran |
| ------------------------------------------------------------ | ------------------ | ----------------------- |
| [Bestämma tabellkategori](#determine-table-category)        | Ja                | Inga                      |
| [Schemanamn](#schema-names)                                | Ja                | Ja                     |
| [Tabellnamn](#table-names)                                  | Ja                | Inga                      |
| [Tabellen persistens](#table-persistence)                      | Ja                | Inga                      |
| [Vanlig tabell](#regular-table)                              | Ja                | Inga                      |
| [Tillfällig tabell](#temporary-table)                          | Ja                | Ja                     |
| [Extern tabell](#external-table)                            | Ja                | Ja                     |
| [Datatyper](#data-types)                                    | Ja                | Ja                     |
| [Distribuerade tabeller](#distributed-tables)                    | Ja                | Inga                      |
| [Hash-distribuerade tabeller](#hash-distributed-tables)          | Ja                | Inga                      |
| [Replikerade tabeller](#replicated-tables)                      | Ja                | Inga                      |
| [Round-robin bord](#round-robin-tables)                    | Ja                | Inga                      |
| [Vanliga distributionsmetoder för tabeller](#common-distribution-methods-for-tables) | Ja                | Inga                      |
| [Partitioner](#partitions)                                    | Ja                | Ja                     |
| [Columnstore-index](#columnstore-indexes)                  | Ja                | Inga                      |
| [Statistik](#statistics)                                    | Ja                | Ja                     |
| [Primärnyckel och unik nyckel](#primary-key-and-unique-key)    | Ja                | Inga                      |
| [Kommandon för att skapa tabeller](#commands-for-creating-tables) | Ja                | Inga                      |
| [Justera källdata med informationslagret](#aligning-source-data-with-the-data-warehouse) | Ja                | Inga                      |
| [Tabellfunktioner som inte stöds](#unsupported-table-features)    | Ja                | Inga                      |
| [Frågor om tabellstorlek](#table-size-queries)                    | Ja                | Inga                      |

## <a name="determine-table-category"></a>Bestämma tabellkategori

Ett [stjärnschema](https://en.wikipedia.org/wiki/Star_schema) ordnar data i fakta- och dimensionstabeller. Vissa tabeller används för integrering eller mellanlagringsdata innan de flyttas till en fakta- eller dimensionstabell. När du utformar en tabell bestämmer du om tabelldata ska tillhöra en fakta-, dimensions- eller integrationstabell. Detta beslut informerar lämplig tabellstruktur och fördelning.

- **Faktatabeller** innehåller kvantitativa data som ofta genereras i ett transaktionssystem och sedan läses in i informationslagret. Ett detaljhandelsföretag genererar till exempel försäljningstransaktioner varje dag och läser sedan in data i faktatabellen för informationslager för analys.

- **Dimensionstabeller** innehåller attributdata som kan ändras men vanligtvis ändras sällan. Till exempel lagras en kunds namn och adress i en dimensionstabell och uppdateras endast när kundens profil ändras. För att minimera storleken på en stor faktatabell behöver kundens namn och adress inte finnas i varje rad i en faktatabell. I stället kan faktatabellen och dimensionstabellen dela ett kund-ID. En fråga kan koppla de två tabellerna för att associera en kunds profil och transaktioner.

- **Integrationstabeller** är en plats för integrering eller mellanlagring av data. Du kan skapa en integrationstabell som en vanlig tabell, en extern tabell eller en tillfällig tabell. Du kan till exempel läsa in data i en mellanlagringstabell, utföra omvandlingar på data i mellanlagring och sedan infoga data i en produktionstabell.

## <a name="schema-names"></a>Schemanamn

Scheman är ett bra sätt att gruppera objekt som används på ett liknande sätt. Följande kod skapar ett [användardefinierat schema](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) som kallas wwi.

```sql
CREATE SCHEMA wwi;
```

## <a name="table-names"></a>Tabellnamn

Om du migrerar flera databaser från en on-prem-lösning till SQL-pool är det bästa att migrera alla fakta-, dimensions- och integrationstabeller till ett SQL-poolschema. Du kan till exempel lagra alla tabeller i [informationslagret WideWorldImportersDW](/sql/samples/wide-world-importers-dw-database-catalog?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) i ett schema som kallas wwi.

Om du vill visa organisationen av tabellerna i SQL-poolen kan du använda fakta, tona och int som prefix till tabellnamnen. Tabellen nedan visar några av schema- och tabellnamnen för WideWorldImportersDW.  

| WideWorldImportersDW tabell  | Tabelltyp | SQL-pool |
|:-----|:-----|:------|:-----|
| Ort | Dimension | Wwi. DimCity (dimcity) |
| Beställa | Fakta | Wwi. FactOrder (Faktaord) |

## <a name="table-persistence"></a>Tabellen persistens

Tabeller lagrar data antingen permanent i Azure Storage, tillfälligt i Azure Storage eller i ett datalager som är externt till datalagret.

### <a name="regular-table"></a>Vanlig tabell

En vanlig tabell lagrar data i Azure Storage som en del av informationslagret. Tabellen och data kvarstår oavsett om en session är öppen eller inte.  I exemplet nedan skapas en vanlig tabell med två kolumner.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Tillfällig tabell

Det finns bara en tillfällig tabell under hela sessionen. Du kan använda en tillfällig tabell för att förhindra att andra användare ser tillfälliga resultat. Om du använder temporära tabeller minskar också behovet av rensning.  Tillfälliga tabeller använder lokal lagring och kan i SQL-pool erbjuda snabbare prestanda.  

SQL on-demand stöder temporära tabeller. Men dess användning är begränsad eftersom du kan välja från tillfällig tabell men inte kan ansluta den med filer i lagring.

Mer information finns i [Temporära tabeller](develop-tables-temporary.md).

### <a name="external-table"></a>Extern tabell

[Externa tabeller](develop-tables-external-tables.md) pekar på data som finns i Azure Storage blob eller Azure Data Lake Storage.

Importera data från externa tabeller till SQL-poolen med hjälp av [CREATE TABLE AS SELECT-uttrycket.](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) En inläsningsdatorkurs finns i [Använda PolyBase för att läsa in data från Azure blob storage](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

För SQL on-demand kan du använda [CETAS](develop-tables-cetas.md) för att spara frågeresultatet i en extern tabell i Azure Storage.

## <a name="data-types"></a>Datatyper

SQL-poolen stöder de vanligaste datatyperna. En lista över datatyper som stöds finns [i datatyper i CREATE TABLE-referensen](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes) i CREATE TABLE-satsen. Mer information om hur du använder datatyper finns i [Datatyper](../sql/develop-tables-data-types.md).

## <a name="distributed-tables"></a>Distribuerade tabeller

En grundläggande funktion i SQL-poolen är hur den kan lagra och fungera på tabeller över [distributioner](../sql-data-warehouse/massively-parallel-processing-mpp-architecture.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#distributions).  SQL-poolen stöder tre metoder för att distribuera data:

- Resursallokering (standard)
- Hash
- Replikerad

### <a name="hash-distributed-tables"></a>Hash-distribuerade tabeller

En hash-distribuerad tabell distribuerar rader baserat på värdet i distributionskolumnen. En hash-distribuerad tabell är utformad för att uppnå hög prestanda för frågor på stora tabeller. Det finns flera faktorer att tänka på när du väljer en distributionskolumn.

Mer information finns i [Designvägledning för distribuerade tabeller](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="replicated-tables"></a>Replikerade tabeller

En replikerad tabell har en fullständig kopia av tabellen som är tillgänglig på varje beräkningsnod. Frågor körs snabbt på replikerade tabeller eftersom kopplingar på replikerade tabeller inte kräver dataförflyttning. Replikering kräver dock extra lagringsutrymme och är inte praktiskt för stora tabeller.

Mer information finns i [Designvägledning för replikerade tabeller](../sql-data-warehouse/design-guidance-for-replicated-tables.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="round-robin-tables"></a>Round-robin bord

En round-robin-tabell fördelar tabellrader jämnt över alla fördelningar. Raderna fördelas slumpmässigt. Det går snabbt att läsa in data i en round robin-tabell.  Men frågor kan kräva mer dataflyttning än de andra distributionsmetoderna.

Mer information finns i [Designvägledning för distribuerade tabeller](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="common-distribution-methods-for-tables"></a>Vanliga distributionsmetoder för tabeller

Tabellkategorin bestämmer ofta det optimala alternativet för tabellfördelning.

| Tabellkategori | Rekommenderat distributionsalternativ |
|:---------------|:--------------------|
| Fakta           | Använd hash-distribution med klustrade columnstore-index. Prestanda förbättras när två hash-tabeller sammanfogas i samma distributionskolumn. |
| Dimension      | Använd replikerad för mindre tabeller. Om tabeller är för stora för att lagras på varje beräkningsnod använder du hash-distribuerad. |
| Mellanlagring        | Använd round-robin för mellanlagringstabellen. Belastningen med CTAS är snabb. När data finns i mellanlagringstabellen använder du INSERT... VÄLJ om du vill flytta data till produktionstabeller. |

## <a name="partitions"></a>Partitioner

I SQL-poolen lagrar och kör en partitionerad tabell åtgärder på tabellraderna enligt dataområden. En tabell kan till exempel partitioneras efter dag, månad eller år. Du kan förbättra frågeprestanda genom partitioneliminering, vilket begränsar en frågegenomsökning till data i en partition.

Du kan också underhålla data via partitionsväxling. Eftersom data i SQL-poolen redan är distribuerade kan för många partitioner försämra frågeprestanda. Mer information finns i [Partitioneringsvägledning](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).  

> [!TIP]
> När partitionen växlar till tabellpartitioner som inte är tomma bör du överväga att använda alternativet TRUNCATE_TARGET i [ALTER TABLE-satsen](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) om befintliga data ska trunkeras.

Koden nedan växlar de transformerade dagliga data till en SalesFact-partition och skriver över alla befintliga data.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

På begäran kan du begränsa de filer/mappar (partitioner) som ska läsas av frågan. Partitionering efter sökväg stöds med hjälp av filsöken och fileinfo-funktionerna som beskrivs i [Fråga lagringsfiler](develop-storage-files-overview.md). I följande exempel står en mapp med data för år 2017:

```sql
SELECT
    nyc.filepath(1) AS [year],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
GROUP BY
    nyc.filepath(1),
    payment_type
ORDER BY
    nyc.filepath(1),
    payment_type
```

## <a name="columnstore-indexes"></a>Columnstore-index

Som standard lagrar SQL-poolen en tabell som ett grupperat columnstore-index. Den här formen av datalagring ger hög datakomprimering och frågeprestanda i stora tabeller.  Det klustrade columnstore-indexet är oftast det bästa valet, men i vissa fall är ett grupperat index eller en heap lämplig lagringsstruktur.  

> [!TIP]
> En heap-tabell kan vara särskilt användbar när du vill läsa in tillfälliga data, till exempel en mellanlagringstabell, som omvandlas till en finaltabell.

En lista över columnstore-funktioner finns i [Nyheter för columnstore-index](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Mer om du vill förbättra indexprestanda i Columnstore finns i [Maximera radgruppskvaliteten för columnstore-index](../sql/data-load-columnstore-compression.md).

## <a name="statistics"></a>Statistik


Frågeoptimeraren använder statistik på kolumnnivå när planen för att köra en fråga skapas. För att förbättra frågeprestanda är det viktigt att ha statistik om enskilda kolumner, särskilt kolumner som används i frågekopplingar. Synapse SQL stöder automatisk skapande av statistik. 

Statistisk uppdatering sker inte automatiskt. Uppdatera statistik efter att ett stort antal rader har lagts till eller ändrats. Uppdatera till exempel statistik efter en inläsning. Ytterligare information finns i [statistikvägeledningsartikeln.](develop-tables-statistics.md)

## <a name="primary-key-and-unique-key"></a>Primärnyckel och unik nyckel

PRIMÄRNYCKEL stöds endast när icke-GRUPPERADE och INTE TILLÄMPAS används båda.  UNIK begränsning stöds endast när INTE ENFORCED används.  Mer information finns i artikeln [Om SQL-tabellbegränsningar.](../sql-data-warehouse/sql-data-warehouse-table-constraints.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="commands-for-creating-tables"></a>Kommandon för att skapa tabeller

Du kan skapa en tabell som en ny tom tabell. Du kan också skapa och fylla i en tabell med resultatet av en select-sats. Följande är T-SQL-kommandon för att skapa en tabell.

| T-SQL-uttryck | Beskrivning |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Skapar en tom tabell genom att definiera alla tabellkolumner och alternativ. |
| [SKAPA EXTERN TABELL](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Skapar en extern tabell. Definitionen av tabellen lagras i SQL-poolen. Tabelldata lagras i Azure Blob storage eller Azure Data Lake Storage. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Fyller i en ny tabell med resultatet av en select-sats. Tabellkolumnerna och datatyperna baseras på resultaten för urvalsutdraget. Om du vill importera data kan den här satsen välja från en extern tabell. |
| [SKAPA EXTERN TABELL SOM MARKERING](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Skapar en ny extern tabell genom att exportera resultatet av en select-sats till en extern plats.  Platsen är antingen Azure Blob storage eller Azure Data Lake Storage. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Justera källdata med informationslagret

Datalagertabeller fylls i genom att data läses in från en annan datakälla. För att en lyckad belastning ska kunna uppnås måste antalet och datatyperna för kolumnerna i källdata justeras med tabelldefinitionen i informationslagret.

> [!NOTE]
> Det kan vara den svåraste delen av att utforma tabellerna att justera data för att justera dem.

Om data kommer från flera datalager kan du portera data till informationslagret och lagra dem i en integrationstabell. När data finns i integrationstabellen kan du använda kraften i SQL-poolen för att implementera omvandlingsåtgärder. När data har förberetts kan du infoga dem i produktionstabeller.

## <a name="unsupported-table-features"></a>Tabellfunktioner som inte stöds

SQL-poolen stöder många, men inte alla, av de tabellfunktioner som erbjuds av andra databaser.  Följande lista visar några av de tabellfunktioner som inte stöds i SQL-poolen.

- Sekundär nyckel, kontrollera [tabellbegränsningar](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Beräknade kolumner](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Indexerade vyer](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Sequence](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Glesa kolumner](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- Surrogatnycklar, implementera med [identitet](../sql-data-warehouse/sql-data-warehouse-tables-identity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Synonymer](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Utlösare](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Unika index](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Användardefinierade typer](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="table-size-queries"></a>Frågor om tabellstorlek

Ett enkelt sätt att identifiera utrymme och rader som förbrukas av en tabell i var och en av de 60 distributionerna är att använda [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Tänk på att använda DBCC kommandon kan vara ganska begränsande.  Dynamiska hanteringsvyer (DMVs) visar mer information än DBCC-kommandon. Börja med att skapa vyn nedan.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count]
    + nps.[row_overflow_used_page_count]
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count]
 - (nps.[reserved_page_count] - nps.[used_page_count])
 - ([in_row_data_page_count]
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
WHERE pn.[type] = 'COMPUTE'
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT *
FROM size
;
```

### <a name="table-space-summary"></a>Sammanfattning av tabellutrymme

Den här frågan returnerar raderna och avståndet efter tabell.  Med sammanfattningen av tabellutrymmet kan du se vilka tabeller som är de största tabellerna. Du ser också om de är round-robin, replikerade eller hash-distribuerade.  För hash-distribuerade tabeller visar frågan distributionskolumnen.  

```sql
SELECT
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM
    dbo.vTableSizes
GROUP BY
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Tabellutrymme efter fördelningstyp

```sql
SELECT
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Tabellutrymme efter indextyp

```sql
SELECT
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Sammanfattning av distributionsutrymme

```sql
SELECT
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Nästa steg

När du har skapat tabellerna för ditt informationslager är nästa steg att läsa in data i tabellen.  En inläsningsstudiekurs finns [i Läsa in data i SQL-poolen](../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#load-the-data-into-sql-pool).
