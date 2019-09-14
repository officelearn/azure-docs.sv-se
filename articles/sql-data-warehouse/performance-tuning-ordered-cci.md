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
ms.openlocfilehash: 41fbebcf4b85f6e48babba30c2d05fedb3e7a5c7
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985303"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Prestanda justering med ordnat grupperat columnstore-index  

När användarna frågar en columnstore-tabell i Azure SQL Data Warehouse kontrollerar optimeringen de lägsta och högsta värden som lagras i varje segment.  Segment som ligger utanför gränserna för frågespråket går inte att läsa från disk till minne.  En fråga kan få snabbare prestanda om antalet segment som ska läsas och deras sammanlagda storlek är små.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Ordnat eller icke-ordnat grupperat columnstore-index 
Som standard skapar en intern komponent (CCI) ett grupperat columnstore-index (CCI) för varje Azure Data Warehouse-tabell som skapats utan ett index-alternativ.  Data i varje kolumn komprimeras till ett separat CCI radgrupps-segment.  Det finns metadata för varje segments värde intervall, så segment som ligger utanför gränserna för frågespråket är inte lästa från disk under frågekörningen.  CCI erbjuder den högsta nivån av data komprimering och minskar storleken på segment som ska läsas så att frågor kan köras snabbare. Men eftersom index verktyget inte sorterar data innan du komprimerar det i segment, kan segment med överlappande värde intervall inträffa, vilket gör att frågor kan läsa fler segment från disken och ta längre tid att slutföra.  

När du skapar en ordnad CCI sorterar Azure SQL Data Warehouse-motorn data i minnet efter beställnings nyckeln (erna) innan index verktyget komprimerar det till index segment.  Med sorterade data minskas överlappande av segment som gör att frågor kan få en mer effektiv segment Eli minering och därmed snabbare prestanda eftersom antalet segment som ska läsas från disken är mindre.  Om alla data kan sorteras i minnet samtidigt, kan segment som överlappa varandra undvikas.  Det här scenariot inträffar inte ofta när du har fått den stora storleken på data i data lager tabeller.  

Om du vill kontrol lera segment intervallen för en kolumn kör du det här kommandot med ditt tabell namn och kolumn namn:

```sql
SELECT o.name, pnp.index_id, pnp.rows, pnp.data_compression_desc, pnp.pdw_node_id, 
pnp.distribution_id, cls.segment_id, cls.column_id, cls.min_data_id, cls.max_data_id, cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
   JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<table_name>' and c.name = '<column_name>'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id
```

## <a name="data-loading-performance"></a>Data inläsnings prestanda

Prestanda för data inläsning i en ordnad CCI-tabell liknar data inläsning i en partitionerad tabell.  
Det kan ta längre tid att läsa in data i en ordnad CCI-tabell än data som läses in i en icke-ordnad CCI-tabell på grund av data sorteringen.  

Här är ett exempel på prestanda jämförelse av inläsning av data i tabeller med olika scheman.
![Performance_comparison_data_loading](media/performance-tuning-ordered-cci/cci-data-loading-performance.png)
 
## <a name="reduce-segment-overlapping"></a>Minska överlappande segment
Nedan visas alternativ för att ytterligare minska segment som överlappar när du skapar beställda CCI i en ny tabell via CTAS eller en befintlig tabell med data:

- Använd en större resurs klass för att tillåta att mer data sorteras samtidigt i minnet innan index verktyget komprimerar dem i segment.  En gång i ett index segment kan inte den fysiska platsen för data ändras.  Det finns ingen data sortering inom ett segment eller mellan segment.  

- Använd en lägre grad av parallellitet (DOP = 1, till exempel).  Varje tråd som används för att skapa ordnad CCI fungerar på en delmängd data och sorterar den lokalt.  Det finns ingen global sortering mellan data sorterade efter olika trådar.  Genom att använda parallella trådar kan du minska tiden för att skapa en ordnad CCI men generera fler överlappande segment än att använda en enda tråd. 
- Sortera data efter sorterings nyckel (er) innan du läser in dem i Azure SQL Data Warehouse tabeller.

## <a name="create-ordered-cci-on-large-tables"></a>Skapa beställda CCI i stora tabeller
Att skapa en ordnad CCI är en offline-åtgärd.  För tabeller som inte har några partitioner är data inte tillgängliga för användarna förrän den ordnade CCI-processen har skapats.   För partitionerade tabeller, eftersom motorn skapar den beställda CCI-partitionen per partition, kan användarna fortfarande komma åt data i partitioner där ordnade CCI inte har skapats.   Du kan använda det här alternativet för att minimera stillestånds tiden när du skapar ordnade CCI i stora tabeller: 

1.  Skapa partitioner på den stora mål tabellen (kallas tabell A).
2.  Skapa en tom ordnad CCI-tabell (kallas tabell B) med samma tabell-och partition schema som tabell A.
3.  Växla en partition från tabell A till tabell B.
4.  Kör ALTER INDEX < Ordered_CCI_Index > återskapa på tabell B för att återskapa den switchade partitionen.  
5.  Upprepa steg 3 och 4 för varje partition i tabell A.
6.  När alla partitioner har växlats från tabell A till tabell B och har återskapats, tar du bort tabell A och byter namn på tabell B till tabell A. 

## <a name="examples"></a>Exempel

**EN. Så här söker du efter ordnade kolumner och ordnings tal:**
```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Om du vill ändra kolumn ordnings tal, lägga till eller ta bort kolumner från order listan eller ändra från CCI till ordnade CCI:**
```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [Översikt över SQL Data Warehouse-utveckling](sql-data-warehouse-overview-develop.md).
