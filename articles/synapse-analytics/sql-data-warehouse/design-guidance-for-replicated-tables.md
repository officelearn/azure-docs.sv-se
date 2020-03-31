---
title: Designvägledning för replikerade tabeller
description: Rekommendationer för att utforma replikerade tabeller i SQL Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: bbf36f8f3aed9d8208c6182daa7237dc57ade67b
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349144"
---
# <a name="design-guidance-for-using-replicated-tables-in-sql-analytics"></a>Designvägledning för användning av replikerade tabeller i SQL Analytics
Den här artikeln innehåller rekommendationer för att utforma replikerade tabeller i SQL Analytics-schemat. Använd dessa rekommendationer för att förbättra frågeprestanda genom att minska dataflyttning och frågekomplexitet.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du är bekant med datadistribution och datarörelsebegrepp i SQL Analytics.Mer information finns [architecture](massively-parallel-processing-mpp-architecture.md) i arkitekturartikeln. 

Som en del av tabelldesign, förstå så mycket som möjligt om dina data och hur data efterfrågas.Tänk dig till exempel följande frågor:

- Hur stort är bordet?   
- Hur ofta uppdateras tabellen?   
- Har jag fakta- och dimensionstabeller i en SQL Analytics-databas?   

## <a name="what-is-a-replicated-table"></a>Vad är en replikerad tabell?
En replikerad tabell har en fullständig kopia av tabellen som är tillgänglig för varje beräkningsnod. När du replikerar en tabell behöver du inte överföra data till beräkningsnoder innan en koppling eller aggregering. Eftersom tabellen innehåller flera kopior fungerar replikerade tabeller bäst när tabellstorleken är mindre än 2 GB komprimerad.  2 GB är inte en hård gräns.  Om data är statiska och inte ändras kan du replikera större tabeller.

Följande diagram visar en replikerad tabell som är tillgänglig för varje beräkningsnod. I SQL Analytics kopieras den replikerade tabellen helt till en distributionsdatabas på varje beräkningsnod. 

![Replikerad tabell](./media/design-guidance-for-replicated-tables/replicated-table.png "Replikerad tabell")  

Replikerade tabeller fungerar bra för dimensionstabeller i ett stjärnschema. Dimensionstabeller är vanligtvis kopplade till faktatabeller som fördelas på ett annat sätt än dimensionstabellen.  Dimensioner är vanligtvis av en storlek som gör det möjligt att lagra och underhålla flera kopior. Dimensioner lagrar beskrivande data som ändras långsamt, till exempel kundens namn och adress, och produktinformation. Datas långsamt föränderliga karaktär leder till mindre underhåll av den replikerade tabellen. 

Överväg att använda en replikerad tabell när:

- Tabellstorleken på disken är mindre än 2 GB, oavsett antalet rader. Om du vill hitta storleken på en tabell kan du `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`använda kommandot [DBCC PDW_SHOWSPACEUSED:](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) . 
- Tabellen används i kopplingar som annars skulle kräva dataflyttning. När du sammanfogar tabeller som inte distribueras i samma kolumn, till exempel en hash-distribuerad tabell till en round-robin-tabell, krävs dataförflyttning för att slutföra frågan.  Om en av tabellerna är liten bör du överväga en replikerad tabell. Vi rekommenderar att du använder replikerade tabeller i stället för round-robin-tabeller i de flesta fall. Om du vill visa dataflyttningar i frågeplaner använder [du sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).  BroadcastMoveOperation är den typiska dataflyttningsåtgärden som kan elimineras med hjälp av en replikerad tabell.  
 
Replikerade tabeller kanske inte ger bäst frågeprestanda när:

- Tabellen har frekventa infognings-, uppdaterings- och borttagningsåtgärder.DML-åtgärderna (Data manipulation language) kräver en ombyggnad av den replikerade tabellen.Ombyggnad ofta kan orsaka långsammare prestanda.
- SQL Analytics-databasen skalas ofta. Skalning av en SQL Analytics-databas ändrar antalet beräkningsnoder, vilket medför att återskapa den replikerade tabellen.
- Tabellen har ett stort antal kolumner, men dataåtgärder kommer vanligtvis bara åt ett litet antal kolumner. I det här fallet, i stället för att replikera hela tabellen, kan det vara mer effektivt att distribuera tabellen och sedan skapa ett index på de kolumner som används ofta. När en fråga kräver dataflyttning flyttar SQL Analytics bara data för de begärda kolumnerna. 

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Använda replikerade tabeller med enkla frågepredikater
Innan du väljer att distribuera eller replikera en tabell bör du tänka på vilka typer av frågor du planerar att köra mot tabellen. När det är möjligt,

- Använd replikerade tabeller för frågor med enkla frågepredikater, till exempel likhet eller ojämlikhet.
- Använd distribuerade tabeller för frågor med komplexa frågepredikater, till exempel GILLA eller INTE.

CPU-intensiva frågor fungerar bäst när arbetet distribueras över alla beräkningsnoder. Frågor som kör beräkningar på varje rad i en tabell presterar till exempel bättre på distribuerade tabeller än replikerade tabeller. Eftersom en replikerad tabell lagras i sin helhet på varje beräkningsnod körs en CPU-intensiv fråga mot en replikerad tabell mot hela tabellen på varje beräkningsnod. Den extra beräkningen kan göra frågeprestanda långsammare.

Den här frågan har till exempel ett komplext predikat.  Den körs snabbare när data finns i en distribuerad tabell i stället för en replikerad tabell. I det här exemplet kan data distribueras av avrundning.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'
       
```       
           
## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Konvertera befintliga round robin-tabeller till replikerade tabeller
Om du redan har round-robin-tabeller rekommenderar vi att du konverterar dem till replikerade tabeller om de uppfyller de villkor som beskrivs i den här artikeln. Replikerade tabeller förbättrar prestanda jämfört med round-robin-tabeller eftersom de eliminerar behovet av dataförflyttning.  En round-robin-tabell kräver alltid dataförflyttning för kopplingar. 

I det här exemplet används [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) för att ändra dimsalesterritorytabellen till en replikerad tabell. Det här exemplet fungerar oavsett om DimSalesTerritory är hash-distribuerad eller round-robin.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
``` 
    
### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Frågeprestandaexempel för round-robin kontra replikerad 
    
En replikerad tabell kräver ingen dataflyttning för kopplingar eftersom hela tabellen redan finns på varje beräkningsnod. Om dimensionstabellerna är deladee varandra kopierar en koppling dimensionstabellen i sin helhet till varje beräkningsnod. Om du vill flytta data innehåller frågeplanen en åtgärd som kallas BroadcastMoveOperation. Den här typen av dataförflyttningsåtgärd gör frågeprestanda långsammare och elimineras med hjälp av replikerade tabeller. Om du vill visa frågeplanssteg använder du [systemkatalogvyn sys.dm_pdw_request_steps.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)  

I följande fråga mot AdventureWorks-schemat `FactInternetSales` är tabellen till exempel hash-distribuerad. `DimDate` Tabellerna `DimSalesTerritory` och är mindre dimensionstabeller. Den här frågan returnerar den totala försäljningen i Nordamerika för räkenskapsåret 2004:

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
Vi återskapade `DimDate` `DimSalesTerritory` och som round-robin bord. Som ett resultat visade frågan följande frågeplan, som har flera broadcast-flyttåtgärder: 
 
![Frågeplan för round-robin](./media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

Vi återskapade `DimDate` `DimSalesTerritory` och som replikerade tabeller och körde frågan igen. Den resulterande frågeplanen är mycket kortare och har inga broadcast-rörelser.

![Replikerad frågeplan](./media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Prestandaöverväganden för ändring av replikerade tabeller
SQL Analytics implementerar en replikerad tabell genom att underhålla en huvudversion av tabellen. Den kopierar huvudversionen till en distributionsdatabas på varje beräkningsnod. När det sker en ändring uppdaterar SQL Analytics första gången huvudtabellen. Sedan återskapas tabellerna på varje beräkningsnod. En ombyggnad av en replikerad tabell inkluderar att kopiera tabellen till varje beräkningsnod och sedan skapa indexen.  En replikerad tabell på en DW400 har till exempel 5 kopior av data.  En huvudkopia och en fullständig kopia på varje beräkningsnod.  Alla data lagras i distributionsdatabaser. SQL Analytics använder den här modellen för att stödja snabbare dataändringssatser och flexibla skalningsåtgärder. 

Ombyggnader krävs efter:
- Data läses in eller ändras
- SQL Analytics-instansen skalas till en annan nivå
- Tabelldefinitionen uppdateras

Ombyggnader krävs inte efter:
- Pausa åtgärd
- Återuppta åtgärden

Återskapandet sker inte omedelbart efter att data har ändrats. I stället utlöses ombyggnaden första gången en fråga väljs från tabellen.  Frågan som utlöste återskapan läser omedelbart från huvudversionen av tabellen medan data asynkront kopieras till varje beräkningsnod. Tills datakopian är klar fortsätter efterföljande frågor att använda huvudversionen av tabellen.  Om någon aktivitet inträffar mot den replikerade tabellen som tvingar en annan ombyggnad, är datakopian ogiltig och nästa select-sats utlöser data som ska kopieras igen. 

### <a name="use-indexes-conservatively"></a>Använd index försiktigt
Standardindexeringsmetoder gäller för replikerade tabeller. SQL Analytics återskapar varje replikerat tabellindex som en del av återskapandet. Använd endast index när resultatökningen uppväger kostnaden för att återskapa indexen.  
 
### <a name="batch-data-loads"></a>Batchdata läses in
När du läser in data i replikerade tabeller, försök att minimera ombyggnader genom batchning laster tillsammans. Utför alla batchade laster innan du kör select-satser.

Det här inläsningsmönstret läser till exempel in data från fyra källor och anropar fyra ombyggnader. 

        Load from source 1.
- Välj satsutlösare ombyggnad 1.
        Ladda från källa 2.
- Välj satsutlösare ombyggnad 2.
- Ladda från källa 3.
- Välj satsutlösare ombyggnad 3.
- Ladda från källa 4.
- Välj satsutlösare ombyggnad 4.

Det här inläsningsmönstret läser till exempel in data från fyra källor, men anropar bara en ombyggnad.

- Ladda från källa 1.
- Ladda från källa 2.
- Ladda från källa 3.
- Ladda från källa 4.
- Välj utdragsutlösare.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Återskapa en replikerad tabell efter en batchbelastning
För att säkerställa konsekventa frågekörningstider bör du överväga att tvinga fram byggandet av de replikerade tabellerna efter en batchbelastning. Annars kommer den första frågan fortfarande att använda dataförflyttning för att slutföra frågan. 

Den här frågan använder [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) DMV för att lista de replikerade tabeller som har ändrats, men inte återskapats.

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
 
Om du vill utlösa en ombyggnad kör du följande sats på varje tabell i föregående utdata. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>Nästa steg 
Om du vill skapa en replikerad tabell använder du någon av dessa satser:

- [SKAPA TABELL (SQL Analytics)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [SKAPA TABELL SOM SELECT (SQL Analytics)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

En översikt över distribuerade tabeller finns i [distribuerade tabeller](sql-data-warehouse-tables-distribute.md).
