---
title: Designriktlinjer för replikerade tabeller – Azure SQL Data Warehouse | Microsoft Docs
description: Rekommendationer för hur man designar replikerade tabeller i Azure SQL Data Warehouse-schemat. 
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/15/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 9b205dea0d6de6469847e8008010a3a2c9d19956
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113146"
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Designriktlinjer för att använda replikerade tabeller i Azure SQL Data Warehouse
Den här artikeln ger rekommendationer för att utforma replikerade tabeller i SQL Data Warehouse-schemat. Använd de här rekommendationerna för att förbättra frågeprestanda genom att minska komplexiteten för data movement och fråga.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du är bekant med datadistribution och begrepp för flytt av data i SQL Data Warehouse.  Mer information finns i den [arkitektur](massively-parallel-processing-mpp-architecture.md) artikeln. 

Förstå så mycket som möjligt om dina data och hur data är den server som en del av tabelldesign.  Till exempel tänka på följande:

- Hur stora är tabellen?   
- Hur ofta uppdateras tabellen?   
- Måste jag fakta-och dimensionstabeller i ett informationslager?   

## <a name="what-is-a-replicated-table"></a>Vad är en replikerad tabell?
En replikerad tabell har en fullständig kopia av tabellen tillgänglig på varje beräkningsnod. Replikera en tabell eliminerar behovet av att överföra data mellan beräkningsnoder innan en join- eller aggregering. Eftersom tabellen har flera kopior, fungerar replikerade tabeller bäst om tabellen är mindre än 2 GB komprimerat.  2 GB är inte en hård gräns.  Om data är statiska och ändrar inte, kan du replikera större tabeller.

Följande diagram visar en replikerad tabell som är tillgänglig på varje beräkningsnod. I SQL Data Warehouse kopieras helt replikerad tabell till en distributionsdatabas på varje beräkningsnod. 

![Replikerat tabell](media/guidance-for-using-replicated-tables/replicated-table.png "replikerat tabell")  

Replikerade tabeller fungerar bra för dimensionstabeller i ett star-schema. Dimensionstabeller kopplas vanligtvis till faktatabeller som distribueras på ett annat sätt än dimensionstabellen.  Dimensioner är vanligtvis med en storlek som gör det möjligt att lagra och hantera flera kopior. Dimensioner lagra beskrivande data som ändras långsamt, som kundens namn och adress och produktinformation. Långsamt föränderliga natur data leder till mindre underhåll av replikerad tabell. 

Överväg att använda en replikerad tabell när:

- Tabellen är på disken mindre än 2 GB, oavsett hur många rader. För att hitta storleken på en tabell, kan du använda den [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) kommandot: `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- Tabellen används i kopplingar som annars skulle kräva dataförflyttning. När du ansluter tabeller som inte distribueras på samma kolumn, till exempel en hash-distribuerad tabell till en resursallokeringstabell krävs Dataförflyttning för att slutföra frågan.  Om någon av tabellerna är liten du en replikerad tabell. Vi rekommenderar att du använder replikerade tabeller i stället för resursallokeringstabeller i de flesta fall. Du kan visa dataflyttningsåtgärder i frågeplaner [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).  BroadcastMoveOperation är typiska dataförflyttningen som kan tas bort med hjälp av en replikerad tabell.  
 
Replikerade tabeller kan inte ge bästa frågeprestanda när:

- Tabellen har ofta infoga, uppdatera och ta bort. Dessa data manipulation language (DML) åtgärder kräver återskapning av replikerad tabell. Återskapa kan ofta orsaka långsammare.
- Datalagret skalas ofta. Skala ett informationslager ändras antalet beräkningsnoder, vilket medför att återskapa replikerad tabell.
- Tabellen har ett stort antal kolumner, men dataåtgärder normalt komma åt inte endast ett litet antal kolumner. I det här scenariot, istället för att replikera hela tabellen, kan det vara mer effektivt att distribuera tabellen och sedan skapa ett index på kolumnerna som används ofta. När en fråga kräver dataförflyttning, flyttar data för de begärda kolumnerna endast i SQL Data Warehouse. 

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Använda replikerade tabeller med enkel fråga predikat
Innan du väljer att distribuera eller replikera en tabell måste tänka på vilka typer av frågor som du planerar att körs mot tabellen. När det är möjligt

- Använda replikerade tabeller för frågor med enkel fråga predikat, till exempel likhet eller olikhet.
- Använd distribuerade tabeller för frågor med komplex fråga predikat som liknande eller inte gillar.

CPU-intensiva frågor gör bäst ifrån sig när arbetet är fördelat över alla Compute-noder. Till exempel utföra frågor som kör beräkningar på varje rad i en tabell bättre på distribuerade tabeller än replikerade tabeller. Eftersom en replikerad tabell lagras med fullständigt på varje beräkningsnod, körs en processorintensiva fråga mot en replikerad tabell mot hela tabellen på varje beräkningsnod. Extra beräkningen kan sakta frågeprestanda.

Den här frågan har exempelvis ett komplext predikat.  Den körs snabbare när data är i en distribuerad tabell i stället för en replikerad tabell. Data kan vara resursallokering som distribueras i det här exemplet.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Konvertera befintliga resursallokeringstabeller till replikerade tabeller
Om du redan har resursallokering tabeller, rekommenderar vi att konvertera dem till replikerade tabeller om de uppfyller de kriterier som beskrivs i den här artikeln. Replikerade tabeller förbättras prestanda när-resursallokeringstabeller eftersom de undanröjer behovet av dataförflyttning.  En resursallokeringstabell kräver alltid Dataförflyttning för kopplingar. 

Det här exemplet används [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) att ändra tabellen dimsalesterritory till en replikerad tabell. Det här exemplet fungerar oavsett om DimSalesTerritory är hash-distribuerad eller resursallokering.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

--Create statistics on new table
CREATE STATISTICS [SalesTerritoryKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryKey]);
CREATE STATISTICS [SalesTerritoryAlternateKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryAlternateKey]);
CREATE STATISTICS [SalesTerritoryRegion] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryRegion]);
CREATE STATISTICS [SalesTerritoryCountry] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryCountry]);
CREATE STATISTICS [SalesTerritoryGroup] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryGroup]);

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```  

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Exempel på sökfråga prestanda för resursallokering jämfört med replikeras 

En replikerad tabell kräver inte någon Dataförflyttning för kopplingar, eftersom det redan finns på varje beräkningsnod hela tabellen. Om dimensionstabellerna är resursallokering distribueras, kopierar en koppling dimensionstabellen med fullständigt till varje beräkningsnod. Om du vill flytta data innehåller en åtgärd som kallas BroadcastMoveOperation i frågeplanen. Den här typen av dataförflyttningen saktar frågeprestanda och elimineras med hjälp av replikerade tabeller. Du kan visa plan frågesteg den [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) system katalogvy. 

I följande fråga mot AdventureWorks-schema, till exempel den ` FactInternetSales` tabellen är hash-distribuerad. Den `DimDate` och `DimSalesTerritory` tabeller är mindre dimensionstabeller. Den här frågan returnerar den totala försäljningen i Nordamerika för räkenskapsåret 2004:
 
```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```
Vi återskapas `DimDate` och `DimSalesTerritory` som resursallokering tabeller. Därför visade frågan följande frågeplanen, som har flera sändning flytta åtgärder: 
 
![Resursallokering frågeplan](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

Vi återskapas `DimDate` och `DimSalesTerritory` som replikerade tabeller och körde frågan igen. Den resulterande frågeplanen är mycket kortare och inte har någon sänder ut flyttar.

![Replikerade frågeplan](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Prestandaöverväganden för att ändra replikerade tabeller
SQL Data Warehouse implementerar en replikerad tabell genom att upprätthålla en huvudversionen av tabellen. Huvudversionen kopieras till en distributionsdatabas på varje beräkningsnod. Om det finns en ändring, uppdaterar huvudtabellen först i SQL Data Warehouse. Sedan återskapar tabellerna på varje beräkningsnod. En återskapning av en replikerad tabell innehåller kopiera tabellen till varje beräkningsnod och att skapa index.  En replikerad tabell på en DW400 har till exempel 5 kopior av data.  En huvudkopian och en fullständig kopia på varje beräkningsnod.  Alla data lagras i distributionsdatabaser. SQL Data Warehouse använder den här modellen för snabbare uttryck för ändring av data och flexibla skalningsåtgärder. 

Behöver krävs efter:
- Data läses in eller ändrade
- Datalagret skalas till en annan nivå
- Tabelldefinitionen har uppdaterats

Det krävs inte behöver efter:
- Pausa åtgärden
- Återuppta drift

Återskapandet sker inte omedelbart efter att data ändras. I stället aktiveras återskapandet första gången en fråga väljer från tabellen.  Frågan som utlöste återskapandet läser direkt från huvudversionen av tabellen när data kopieras asynkront till varje beräkningsnod. Tills Datakopieringen är klar, efterföljande frågor kommer att fortsätta att använda huvudversionen av tabellen.  Om alla aktiviteter inträffar mot replikerad tabell som tvingar en återskapning av en annan, Datakopieringen betraktas som inaktuella och nästa select-instruktionen utlöser data kopieras igen. 

### <a name="use-indexes-conservatively"></a>Använda index var
Indexering standardmetoder gäller för replikerade tabeller. Varje replikerad tabell indexet som en del av återskapandet återskapas i SQL Data Warehouse. Använd endast index när prestanda uppväger kostnaden för att återskapa index.  
 
### <a name="batch-data-loads"></a>Batch-Datainläsningen
När du läser in data i replikerade tabeller, försöka minimera behöver av batchbearbetning belastningar tillsammans. Utföra gruppbaserade belastningen innan du kör select-uttryck.

Det här mönstret belastningen läser in data från fyra källor och anropar fyra behöver. 

- Läsa in från källa 1.
- SELECT-instruktion utlösare återskapa 1.
- Läsa in från källan 2.
- SELECT-instruktion utlösare återskapa 2.
- Läsa in från källan 3.
- SELECT-instruktion utlösare återskapa 3.
- Läsa in från källan 4.
- SELECT-instruktion utlösare återskapa 4.

Läser in data från fyra källor till exempel mönster för belastningsutjämning, men endast anropar en återskapning.

- Läsa in från källa 1.
- Läsa in från källan 2.
- Läsa in från källan 3.
- Läsa in från källan 4.
- SELECT-instruktion utlösare återskapa.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Återskapa en replikerad tabell efter en batch-belastning
Överväg att tvinga versionen av replikerade tabeller när du har en batch-belastning för att säkerställa konsekvent körningstider. I annat fall kommer den första frågan fortfarande använda Dataförflyttning för att slutföra frågan. 

Den här frågan använder den [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) DMV att lista de replikerade tabellerna som har ändrats, men inte återskapas.

```sql 
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id 
  JOIN sys.pdw_table_distribution_properties p 
    ON p.object_id = t.object_id 
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```
 
Kör följande instruktion för att utlösa en återskapning av en i varje tabell i ovanstående utdata. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>Nästa steg 
Använd någon av dessa instruktioner för att skapa en replikerad tabell:

- [Skapa tabell (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [Skapa TABLE AS SELECT (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

En översikt över distribuerade tabeller finns i [distribuerade tabeller](sql-data-warehouse-tables-distribute.md).



