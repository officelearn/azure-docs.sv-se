---
title: Prestanda justering med Azure SQL Data Warehouse ordnat grupperat columnstore-index | Microsoft Docs
description: Rekommendationer och överväganden som du bör känna till när du använder ordnat grupperat columnstore-index för att förbättra din frågas prestanda.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 0acdf1496151df57d4097ce5bc71d782dc465873
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554546"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Prestanda justering med ordnat grupperat columnstore-index  

När användarna frågar en columnstore-tabell i Azure SQL Data Warehouse kontrollerar optimeringen de lägsta och högsta värden som lagras i varje segment.  Segment som ligger utanför gränserna för frågespråket går inte att läsa från disk till minne.  En fråga kan få snabbare prestanda om antalet segment som ska läsas och deras sammanlagda storlek är små.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Ordnat eller icke-ordnat grupperat columnstore-index 
Som standard skapar en intern komponent (CCI) ett grupperat columnstore-index (CCI) för varje Azure Data Warehouse-tabell som skapats utan ett index-alternativ.  Data i varje kolumn komprimeras till ett separat CCI radgrupps-segment.  Det finns metadata för varje segments värde intervall, så segment som ligger utanför gränserna för frågespråket är inte lästa från disk under frågekörningen.  CCI erbjuder den högsta nivån av data komprimering och minskar storleken på segment som ska läsas så att frågor kan köras snabbare. Men eftersom index Builder inte sorterar data innan de komprimeras i segment, kan segment med överlappande värde intervall inträffa, vilket gör att frågor kan läsa fler segment från disken och ta längre tid att slutföra.  

När du skapar en ordnad CCI sorterar Azure SQL Data Warehouses motorn befintliga data i minnet efter beställnings nyckeln (erna) innan index verktyget komprimerar dem till index segment.  Med sorterade data minskas överlappande av segment som gör att frågor kan få en mer effektiv segment Eli minering och därmed snabbare prestanda eftersom antalet segment som ska läsas från disken är mindre.  Om alla data kan sorteras i minnet samtidigt, kan segment som överlappa varandra undvikas.  Det här scenariot inträffar inte ofta när du har fått den stora storleken på data i data lager tabeller.  

Om du vill kontrol lera segment intervallen för en kolumn kör du det här kommandot med ditt tabell namn och kolumn namn:

```sql
SELECT o.name, pnp.index_id, cls.row_count, pnp.data_compression_desc, pnp.pdw_node_id, 
pnp.distribution_id, cls.segment_id, cls.column_id, cls.min_data_id, cls.max_data_id, cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
   JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table_Name>' and cols.name = '<Column_Name>' 
ORDER BY o.name, pnp.distribution_id, cls.min_data_id

```

> [!NOTE] 
> I en ordnad CCI-tabell, sorteras inte nya data som resulterar i DML-eller data inläsnings åtgärder automatiskt.  Användare kan återskapa de beställda CCI för att sortera alla data i tabellen.  I Azure SQL Data Warehouse är columnstore-indexet återbyggt en offline-åtgärd.  För en partitionerad tabell görs en ombyggning av en partition i taget.  Data i partitionen som återskapas är offline och otillgängliga tills återskapandet har slutförts för den partitionen. 

## <a name="query-performance"></a>Frågeprestanda

En frågas prestanda vinst från en ordnad CCI är beroende av frågans mönster, storleken på data, hur väl data sorteras, den fysiska strukturen för segment och den DWU och resurs klass som valts för frågekörningen.  Användarna bör granska alla dessa faktorer innan de väljer sorterings kolumner när du designar en ordnad CCI-tabell.

Frågor med alla dessa mönster körs vanligt vis snabbare med ordnade CCI.  
1. Frågorna har likhets-, olikhets-eller intervall-predikat
1. Predikatet predikat och de beställda CCI-kolumnerna är desamma.  
1. Predikat-kolumnerna används i samma ordning som kolumn ordnings talet för de ordnade CCI-kolumnerna.  
 
I det här exemplet har Table T1 ett grupperat columnstore-index som ordnats i sekvensen Col_C, Col_B och Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

Prestanda för fråga 1 kan dra nytta av mer från beställda CCI än de övriga tre frågorna. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>Data inläsnings prestanda

Prestanda för data inläsning i en ordnad CCI-tabell liknar en partitionerad tabell.  Inläsning av data i en ordnad CCI-tabell kan ta längre tid än en icke-ordnad CCI-tabell på grund av data sorterings åtgärden, men frågor kan köras snabbare efteråt med ordnade CCI.  

Här är ett exempel på prestanda jämförelse av inläsning av data i tabeller med olika scheman.

![Performance_comparison_data_loading](media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Här är ett exempel på att fråga prestanda jämförelser mellan CCI och ordnade CCI.

![Performance_comparison_data_loading](media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Minska överlappande segment

Antalet överlappande segment beror på storleken på de data som ska sorteras, tillgängligt minne och den maximala graden av parallellitet (MAXDOP) under skapandet av ordnade CCI. Nedan visas alternativ för att minska överlappande segment när du skapar beställda CCI.

- Använd xlargerc resurs klass på en högre DWU för att tillåta mer minne för data sortering innan index verktyget komprimerar data i segment.  En gång i ett index segment kan inte den fysiska platsen för data ändras.  Det finns ingen data sortering inom ett segment eller mellan segment.  

- Skapa beställda CCI med MAXDOP = 1.  Varje tråd som används för att skapa ordnad CCI fungerar på en delmängd data och sorterar den lokalt.  Det finns ingen global sortering mellan data sorterade efter olika trådar.  Genom att använda parallella trådar kan du minska tiden för att skapa en ordnad CCI men generera fler överlappande segment än att använda en enda tråd.  För närvarande stöds inte alternativet MAXDOP i skapa en ordnad CCI-tabell med CREATE TABLE AS SELECT-kommandot.  Att skapa en ordnad CCI via CREATE INDEX eller CREATE TABLE kommandon stöder inte alternativet MAXDOP. Exempel:

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```
- Sortera data efter sorterings nyckel (er) innan du läser in dem i Azure SQL Data Warehouse tabeller.


Här är ett exempel på en ordnad tabell distribution som har noll segment som överlappar följande rekommendationer. Den ordnade CCI-tabellen skapas i en DWU1000c-databas via CTAS från en 20-heap-tabell med MAXDOP 1 och xlargerc.  CCI är ordnade i en BIGINT-kolumn utan dubbletter.  

![Segment_No_Overlapping](media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Skapa beställda CCI i stora tabeller
Att skapa en ordnad CCI är en offline-åtgärd.  För tabeller som inte har några partitioner är data inte tillgängliga för användarna förrän den ordnade CCI-processen har skapats.   För partitionerade tabeller, eftersom motorn skapar den beställda CCI-partitionen per partition, kan användarna fortfarande komma åt data i partitioner där ordnade CCI inte har skapats.   Du kan använda det här alternativet för att minimera stillestånds tiden när du skapar ordnade CCI i stora tabeller: 

1.  Skapa partitioner på den stora mål tabellen (kallas Table_A).
2.  Skapa en tom sorterad CCI-tabell (kallas Table_B) med samma tabell-och partitions schema som tabell A.
3.  Växla en partition från tabell A till tabell B.
4.  Kör ALTER INDEX < Ordered_CCI_Index > på < Table_B > återskapa PARTITION = < Partition_ID > på tabell B för att återskapa den switchade partitionen.  
5.  Upprepa steg 3 och 4 för varje partition i Table_A.
6.  När alla partitioner har växlats från Table_A till Table_B och har återskapats, tar du bort Table_A och byter namn på Table_B till Table_A. 

>[!NOTE]
>Under för hands versionen av det ordnade columnstore-indexet (CCI) i Azure SQL Data Warehouse kan duplicerade data skapas om de ordnade CCI skapas eller återskapas via skapa GRUPPERat COLUMNSTORE-INDEX i en partitionerad tabell. Ingen data förlust berörs. En korrigering av det här problemet kommer snart att vara tillgänglig. För en lösning kan användarna skapa beställda CCI i en partitionerad tabell med kommandot CTAS


## <a name="examples"></a>Exempel

**A. för att kontrol lera sorterade kolumner och ordnings nummer:**
```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. om du vill ändra kolumn ordnings tal, lägga till eller ta bort kolumner från order listan eller ändra från CCI till ordnade CCI:**
```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [Översikt över SQL Data Warehouse-utveckling](sql-data-warehouse-overview-develop.md).
