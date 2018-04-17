---
title: Utforma vägledning för replikerade tabeller - Azure SQL Data Warehouse | Microsoft Docs
description: Rekommendationer för hur man designar replikerade tabeller i Azure SQL Data Warehouse-schemat.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/11/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 271b832f329e33b68f60fbc62005c6ee36bafe69
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Utforma riktlinjer för att använda replikerade tabeller i Azure SQL Data Warehouse
Den här artikeln ger rekommendationer för att utforma replikerade tabeller i SQL Data Warehouse-schemat. Använd de här rekommendationerna för att förbättra prestanda genom att minska komplexiteten för data movement och fråga.

> [!NOTE]
> Replikerad tabell-funktionen är för närvarande i förhandsversion. Vissa funktioner kan ändras.
> 

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du är bekant med datadistribution och begrepp för flytt av data i SQL Data Warehouse.  Mer information finns i [arkitektur](massively-parallel-processing-mpp-architecture.md) artikel. 

Som en del av tabelldesign, Förstå så mycket som möjligt om dina data och hur data efterfrågas.  Till exempel tänka på följande:

- Hur stor är tabellen?   
- Hur ofta uppdateras tabellen?   
- Måste jag fakta-och dimensionstabeller i ett informationslager?   

## <a name="what-is-a-replicated-table"></a>Vad är en replikerad tabell?
En replikerad tabell har en fullständig kopia av tabellen tillgänglig på varje Compute-nod. Replikera en tabell eliminerar behovet av att överföra data mellan datornoderna innan ett join- eller aggregering. Eftersom tabellen har flera kopior, replikerade tabeller som fungerar bäst när tabellen är mindre än 2 GB komprimerat.

Följande diagram visar en replikerad tabell som är tillgängligt på varje Compute-nod. I SQL Data Warehouse kopieras replikerad tabell fullständigt till en distributionsdatabas på varje beräkningsnod. 

![Replikerade tabellen](media/guidance-for-using-replicated-tables/replicated-table.png "replikerade tabell")  

Replikerade tabeller fungerar bra för små dimensionstabeller i ett stjärnschema. Dimensionstabeller är vanligtvis med en storlek som gör det möjligt att lagra och hantera flera kopior. Dimensioner lagra beskrivande data som ändras långsamt, till exempel kundens namn och adress och produktinformation. Långsamt föränderliga natur data leder till färre ombyggnad av replikerad tabell. 

Överväg att använda en replikerad tabell när:

- Tabellen är på disken mindre än 2 GB, oavsett hur många rader. Du kan använda för att söka efter storleken på en tabell i [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) kommando: `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- Tabellen används i kopplingar som annars skulle kräva dataflyttning. Exempelvis kräver en koppling för hash-distribuerade tabeller dataflyttning när anslutande kolumner inte är samma kolumn i distributionen. Om en distribuerad hash-tabeller är liten du en replikerad tabell. En koppling för en tabell som resursallokering kräver dataflyttning. Vi rekommenderar att du använder replikerade tabeller i stället för resursallokering tabeller i de flesta fall. 


Överväg att konvertera en befintlig distribuerad tabellen till en replikerad tabell när:

- Frågan planerar Använd dataflyttsåtgärderna som sänder data till Compute-noder. BroadcastMoveOperation dyr och långsammare prestanda för frågor. Du kan visa dataflyttsåtgärderna i frågeplaner [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).
 
Replikerade tabeller kan inte ge bästa möjliga prestanda när:

- Tabellen har ofta infoga, uppdatera och ta bort. Dessa data manipulation language (DML) åtgärder kräver en återskapning av replikerad tabell. Återskapa kan ofta ge lägre prestanda.
- Datalagret skalas ofta. Skala ett informationslager ändras antalet Compute-noder, vilket innebär en återskapning.
- Tabellen har ett stort antal kolumner, men dataåtgärder normalt kommer åt litet antal kolumner. I det här scenariot, istället för att replikera hela tabellen, kanske det effektivare att hash distribuera tabellen och skapa ett index på kolumnerna som används ofta. När en fråga kräver dataflyttning, flyttar data endast i begärda kolumner i SQL Data Warehouse. 



## <a name="use-replicated-tables-with-simple-query-predicates"></a>Använda replikerade tabeller med enkel fråga predikat
Innan du väljer att distribuera eller replikera en tabell tänka på vilka typer av frågor som du planerar att köra mot tabellen. När det är möjligt

- Använd replikerade tabeller för frågor med enkel fråga predikat, till exempel likhet eller olikhet.
- Använd distribuerade tabeller för frågor med komplicerade frågan predikat, till exempel vill eller inte gillar.

Processorintensiva frågor gör bäst ifrån sig när arbetet distribueras över alla Compute-noder. Till exempel bättre frågor som körs beräkningar på varje rad i en tabell för distribuerade tabeller än replikerade tabeller. Eftersom en replikerad tabell lagras i sin helhet på varje beräkningsnod körs en processorintensiva fråga mot en replikerad tabell mot hela tabellen på varje beräkningsnod. Extra beräkningen kan sakta frågeprestanda.

Den här frågan har till exempel ett komplext predikat.  Det körs snabbare när leverantören är en distribuerad tabell i stället för en replikerad tabell. Leverantören kan vara hash-distribuerade eller resursallokering distribueras i det här exemplet.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Konvertera befintliga resursallokering tabeller till replikerade tabeller
Om du redan har resursallokering tabeller, rekommenderar vi att konvertera dem till replikerade tabeller, om de uppfyller med villkor som beskrivs i den här artikeln. Replikerade tabeller förbättra prestanda över resursallokering tabeller eftersom de undanröjer behovet av dataflyttning.  En tabell med resursallokering kräver alltid dataflyttning för kopplingar. 

Det här exemplet används [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) ändra tabellen DimSalesTerritory till en replikerad tabell. Det här exemplet fungerar oavsett om DimSalesTerritory är hash-distribuerade eller resursallokering.

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

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Exempel på frågan resultat för resursallokering jämfört med replikeras 

En replikerad tabell kräver inte någon dataflyttning för kopplingar eftersom det redan finns på varje beräkningsnod hela tabellen. Om dimensionstabellerna resursallokering distribueras, kopieras en koppling dimensionstabellen fullständigt till varje Compute-nod. Om du vill flytta data innehåller en åtgärd som kallas BroadcastMoveOperation i frågeplanen. Den här typen av data movement åtgärden långsammare prestanda för frågor och elimineras med hjälp av replikerade tabeller. Du kan visa plan frågesteg den [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) system katalogvyn. 

Till exempel i följande fråga mot AdventureWorks-schemat i ` FactInternetSales` tabellen är hash-distribueras. Den `DimDate` och `DimSalesTerritory` tabeller är mindre dimensionstabeller. Den här frågan returnerar total försäljning i Nordamerika för räkenskapsår 2004:
 
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
Vi återskapas `DimDate` och `DimSalesTerritory` som resursallokering tabeller. Därför frågan visade följande frågeplanen som har flera broadcast flytta operationer: 
 
![Resursallokering frågeplan](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

Vi återskapas `DimDate` och `DimSalesTerritory` som replikerade tabeller och kördes frågan igen. Den resulterande frågeplanen är mycket kortare och inte har någon sänder ut flyttar.

![Replikerade frågeplan](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Prestandaöverväganden för att ändra replikerade tabeller
SQL Data Warehouse implementerar en replikerad tabell genom att underhålla en huvudversionen i tabellen. Huvudversionen kopieras till en distributionsdatabas på varje beräkningsnod. När det finns en ändring, uppdaterar SQL Data Warehouse först huvudtabellen. Sedan kräver den att tabeller på varje Compute-nod. En återskapning av en replikerad tabell innehåller kopiera tabellen till varje Compute-nod och sedan återskapa index.

Ombyggnad krävs efter:
- Data läses in eller ändras
- Datalagret skalas till en annan nivå
- Tabelldefinitionen uppdateras

Ombyggnad krävs inte efter:
- Åtgärden pausa
- Åtgärden återuppta

Återskapandet sker inte omedelbart när data har ändrats. I stället aktiveras återskapandet första gången en fråga väljer från tabellen.  Inom första select-instruktionen från tabellen finns steg för att återskapa replikerad tabell.  Eftersom återskapandet görs i frågan, kan påverkan på första select-uttrycket vara betydande beroende på storleken på tabellen.  Om flera replikerade tabeller ingår som behöver en återskapning, återskapas varje kopia följd som steg i instruktionen.  Om du vill behålla data tas konsekvenskontroll under återskapa replikerad tabell ett exklusivt lås på tabellen.  Låset förhindrar all åtkomst till tabellen under återskapandet. 

### <a name="use-indexes-conservatively"></a>Använda index hänsyn
Standard indexering praxis gäller för replikerade tabeller. SQL Data Warehouse återskapar varje replikerad Tabellindex som en del av återskapandet. Använd endast index om prestandafördelar uppväger kostnaden för att återskapa index.  
 
### <a name="batch-data-loads"></a>Batch databelastningar
När du läser in data i replikerade tabeller, kan du försöka minimera bygger av batchbearbetning belastningar tillsammans. Utföra gruppbaserad belastningarna innan du kör select-satser.

Det här mönstret belastningen läser in data från fyra källor och anropar fyra bygger. 

- Läsa in från datakällan 1.
- SELECT-instruktion utlösare återskapa 1.
- Läsa in från källan 2.
- SELECT-instruktion utlösare återskapa 2.
- Läsa in från källan 3.
- SELECT-instruktion utlösare återskapa 3.
- Läsa in från datakällan 4.
- SELECT-instruktion utlösare återskapa 4.

Det här mönstret belastningen läser in data från fyra källor men endast anropar en återskapning.

- Läsa in från datakällan 1.
- Läsa in från källan 2.
- Läsa in från källan 3.
- Läsa in från datakällan 4.
- SELECT-instruktion utlösare återskapa.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Återskapa en replikerad tabell efter en batch-inläsning
För att säkerställa konsekvent körningstider, rekommenderar vi att tvinga en uppdatering av replikerade tabeller efter en batch inläsningen. I annat fall måste den första frågan vänta på tabeller att uppdatera, vilket innefattar återskapa index. Beroende på storleken och antalet replikerade tabeller påverkas vara prestandapåverkan betydande.  

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
 
Om du vill tvinga en återskapning, kör du följande uttryck för varje tabell i föregående utdata. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>Nästa steg 
Använd någon av dessa instruktioner om du vill skapa en replikerad tabell:

- [Skapa tabell (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [Skapa TABLE AS SELECT (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

En översikt över distribuerade tabeller, se [distribuerade tabeller](sql-data-warehouse-tables-distribute.md).



