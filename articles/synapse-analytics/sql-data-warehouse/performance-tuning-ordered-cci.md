---
title: Prestandajustering med grupperade kolumnlagringsindex
description: Rekommendationer och överväganden som du bör känna till när du använder beställt grupperat columnstore-index för att förbättra frågeprestanda.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 088a0d10b96a30ef830b4e8a8dc12c19127141db
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417046"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Prestandajustering med grupperade kolumnlagringsindex  

När användare frågar en columnstore-tabell i Synapse SQL-pool kontrollerar optimeraren de lägsta och högsta värden som lagras i varje segment.  Segment som ligger utanför gränserna för frågepredikatet läss inte från disk till minne.  En fråga kan få snabbare prestanda om antalet segment som ska läsas och deras totala storlek är små.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Beställt jämfört med icke-beställt grupperat columnstore-index

Som standard skapar en intern komponent (indexbyggare) ett icke-beställt klustrade columnstore-index (CCI) för varje tabell som skapas utan indexalternativ.  Data i varje kolumn komprimeras till ett separat CCI-radgruppssegment.  Det finns metadata för varje segments värdeintervall, så segment som ligger utanför gränserna för frågepredikatet läss inte från disken under frågekörningen.  CCI erbjuder den högsta nivån av datakomprimering och minskar storleken på segment att läsa så att frågor kan köras snabbare. Eftersom indexverktyget inte sorterar data innan de komprimeras till segment kan segment med överlappande värdeintervall uppstå, vilket gör att frågor läs fler segment från disken och det tar längre tid att slutföra.  

När du skapar en ordnad CCI sorterar Synapse SQL-motorn befintliga data i minnet efter ordernyckeln/nyckelerna innan indexverktyget komprimerar dem till indexsegment.  Med sorterade data minskas segmentöverlappningen så att frågor kan ha en effektivare segmenteliminering och därmed snabbare prestanda eftersom antalet segment som ska läsas från disken är mindre.  Om alla data kan sorteras i minnet samtidigt kan segmentöverlappning undvikas.  På grund av stora tabeller i informationslager händer det här scenariot inte ofta.  

Om du vill kontrollera segmentintervallen för en kolumn kör du följande kommando med tabellnamnet och kolumnnamnet:

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id 


```

> [!NOTE] 
> I en ordnad CCI-tabell sorteras de nya data som kommer från samma batch med DML- eller datainläsningsåtgärder i den batchen, det finns ingen global sortering över alla data i tabellen.  Användare kan återskapa den beställda CCI för att sortera alla data i tabellen.  I Synapse SQL är columnstore-indexet REBUILD en offlineåtgärd.  För en partitionerad tabell görs OM-partitionen i taget.  Data i partitionen som byggs om är "offline" och inte tillgänglig förrän ÅTERSKAPA är klar för den partitionen. 

## <a name="query-performance"></a>Frågeprestanda

En frågas prestandavinst från en ordnad CCI beror på frågemönstren, storleken på data, hur väl data sorteras, segmentens fysiska struktur och DWU- och resursklass som valts för frågekörningen.  Användare bör granska alla dessa faktorer innan de väljer ordningskolumnerna när de utformar en ordnad CCI-tabell.

Frågor med alla dessa mönster körs vanligtvis snabbare med beställda CCI.  
1. Frågorna har jämlikhet, ojämlikhet eller intervall predikater
1. Predikatkolumnerna och de ordnade CCI-kolumnerna är desamma.  
1. Predikatkolumnerna används i samma ordning som kolumnordisen för ordnade CCI-kolumner.  
 
I det här exemplet har tabell T1 ett grupperat columnstore-index ordnat i sekvensen Col_C, Col_B och Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

Prestanda för fråga 1 kan dra mer nytta av beställda CCI än de andra tre frågorna. 

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

## <a name="data-loading-performance"></a>Datainläsningsprestanda

Prestanda för data som läses in i en ordnad CCI-tabell liknar en partitionerad tabell.  Att läsa in data i en ordnad CCI-tabell kan ta längre tid än en icke-ordnad CCI-tabell på grund av datasorteringsåtgärden, men frågor kan köras snabbare efteråt med beställda CCI.  

Här är ett exempel på prestandajämförelse av inläsning av data i tabeller med olika scheman.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Här är ett exempel på frågeprestandajämnning mellan CCI och ordnade CCI.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Minska segmentöverlappningen

Antalet överlappande segment beror på storleken på data som ska sorteras, det tillgängliga minnet och den maximala graden av parallellitet (MAXDOP) under ordnad cci-skapande. Nedan finns alternativ för att minska segmentöverlappningen när du skapar ordnade CCI.

- Använd xlargerc-resursklass på en högre DWU för att tillåta mer minne för datasortering innan indexverktyget komprimerar data till segment.  En gång i ett indexsegment kan datas fysiska plats inte ändras.  Det finns ingen datasortering inom ett segment eller mellan segment.  

- Skapa beställda CCI med MAXDOP = 1.  Varje tråd som används för ordnad CCI-skapande fungerar på en delmängd av data och sorterar den lokalt.  Det finns ingen global sortering mellan data sorterade efter olika trådar.  Med hjälp av parallella trådar kan minska tiden för att skapa en ordnad CCI men kommer att generera mer överlappande segment än att använda en enda tråd.  För närvarande stöds maxdop-alternativet endast när du skapar en ordnad CCI-tabell med kommandot SKAPA TABELL SOM SELECT.  Att skapa en ordnad CCI via SKAPA INDEX- eller CREATE TABLE-kommandon stöder inte maxdop-alternativet. Exempel:

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- Försortera data efter sorteringsnyckeln/sorterna innan de läses in i tabeller.

Här är ett exempel på en ordnad CCI-tabelldistribution som har noll segment överlappande enligt ovanstående rekommendationer. Den beställda CCI-tabellen skapas i en DWU1000c-databas via CTAS från en 20 GB heap-tabell med MAXDOP 1 och xlargerc.  CCI är ordnad på en BIGINT-kolumn utan dubbletter.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Skapa ordnade CCI på stora tabeller

Att skapa en ordnad CCI är en offlineåtgärd.  För tabeller utan partitioner är data inte tillgängliga för användare förrän den ordnade CCI-skapandeprocessen är klar.   För partitionerade tabeller, eftersom motorn skapar den ordnade CCI-partitionen efter partition, kan användare fortfarande komma åt data i partitioner där ordnad CCI-skapande inte pågår.   Du kan använda det här alternativet för att minimera driftstoppet under ordnad CCI-skapande på stora tabeller: 

1.    Skapa partitioner i den stora måltabellen (kallas Table_A).
2.    Skapa en tom ordnad CCI-tabell (kallas Table_B) med samma tabell- och partitionsschema som tabell A.
3.    Växla en partition från tabell A till tabell B.
4.    Kör ALTER INDEX <Ordered_CCI_Index> ON <Table_B> REBUILD PARTITION = <Partition_ID> på tabell B för att återskapa den inkopplade partitionen.  
5.    Upprepa steg 3 och 4 för varje partition i Table_A.
6.    När alla partitioner har växlats från Table_A till Table_B och har byggts om, släpp Table_A och byta namn på Table_B till Table_A. 

## <a name="examples"></a>Exempel

**A. För att kontrollera ordnade kolumner och beställa ordning:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Om du vill ändra kolumnordningen lägger du till eller tar bort kolumner från orderlistan eller byter från CCI till beställda CCI:**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Nästa steg

Fler utvecklingstips finns i [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).
