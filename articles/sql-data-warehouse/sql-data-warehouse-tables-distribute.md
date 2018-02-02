---
title: "Utforma vägledning för distribuerade register - Azure SQL Data Warehouse | Microsoft Docs"
description: "Rekommendationer för att utforma hash-distribueras och resursallokering tabeller i Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 01/18/2018
ms.author: barbkess
ms.openlocfilehash: 3c86b89da796223336e3a0d9dd809ae140d6911e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="guidance-for-designing-distributed-tables-in-azure-sql-data-warehouse"></a>Vägledning för att utforma distribuerade tabeller i Azure SQL Data Warehouse

Den här artikeln ger rekommendationer för att utforma distribuerade tabeller i Azure SQL Data Warehouse. Hash-distribuerade tabellerna förbättra frågeprestanda på stora faktatabeller och är fokus för den här artikeln. Resursallokering tabeller är användbara för att förbättra hastighet för inläsning. De här valen har en betydande inverkan på förbättra frågan och inläsning av prestanda.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du är bekant med datadistribution och begrepp för flytt av data i SQL Data Warehouse.  Mer information finns i [arkitektur](massively-parallel-processing-mpp-architecture.md) artikel. 

Som en del av tabelldesign, Förstå så mycket som möjligt om dina data och hur data efterfrågas.  Till exempel tänka på följande:

- Hur stor är tabellen?   
- Hur ofta uppdateras tabellen?   
- Måste jag fakta-och dimensionstabeller i ett informationslager?   

## <a name="what-is-a-distributed-table"></a>Vad är en distribuerad tabell?
En distribuerad tabell visas som en enda tabell, men raderna lagras över 60-distributioner. Raderna distribueras med hash eller resursallokering algoritm. 

En annan tabellagring är att replikera en mindre tabell över Compute-noder. Mer information finns i [utforma vägledning för replikerade tabeller](design-guidance-for-replicated-tables.md). Snabbt mellan de tre alternativen finns distribuerade tabeller i den [tabeller översikt](sql-data-warehouse-tables-overview.md). 


### <a name="hash-distributed"></a>Hash distribueras
En distribuerad hash-tabell distribuerar tabellraderna över Compute-noder med hjälp av en deterministisk hash-funktionen för att tilldela varje rad till en [distribution](massively-parallel-processing-mpp-architecture.md#distributions). 

![Distribuerade tabell](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "distribuerade tabell")  

Eftersom identiska värden hash alltid till samma distributionen, har datalagret inbyggd kunskap om rad-platser. SQL Data Warehouse använder denna kunskap för att minimera dataflytt under frågor, vilket förbättrar prestanda för frågor. 

Hash-distribuerade tabeller fungerar väl för stora faktatabeller i ett stjärnschema. De kan ha ett stort antal rader och fortfarande uppnå hög prestanda. Det är naturligtvis vissa designöverväganden som hjälper dig att få prestanda distribuerade system är utformad att ge. Att välja en bra distribution kolumn är en faktor som beskrivs i den här artikeln. 

Överväg att använda en hash-distribuerade när:

- Tabellen är på disken större än 2 GB.
- Tabellen har ofta infoga, uppdatera och ta bort. 

### <a name="round-robin-distributed"></a>Resursallokering distribueras
En distribuerad tabell resursallokering fördelar tabellraderna jämnt över alla distributioner. Tilldelning av rader att distributioner är slumpmässig. Till skillnad från hash-distribuerade tabeller är rader med samma värden inte garanterat tilldelas till samma distributionsplats. 

Därför måste systemet ibland att anropa en åtgärd för flytt av data för att organisera dina data innan den kan lösa en fråga.  Den här extra steg kan sakta ned dina frågor. Till exempel ansluta till en tabell med resursallokering vanligtvis måste reshuffling rader, vilket är en prestanda-träff.

Överväg att använda resursallokering distributionen för en tabell i följande scenarier:

- När igång som en enkel startpunkt eftersom det är standardvärdet
- Om det finns ingen uppenbara anslutande nyckel
- Om det inte är bra kandidat kolumn för hash-tabellen distribuerar
- Om tabellen inte delar en gemensam join-nyckel med andra tabeller
- Om kopplingen är mindre viktig än andra kopplingar i frågan
- När tabellen är en tillfällig mellanlagring tabell

Kursen [läser in data från Azure Storage blob](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) ger ett exempel på att läsa in data i en resursallokering mellanlagringstabellen.


## <a name="choosing-a-distribution-column"></a>Om du väljer en kolumn för distribution
En distribuerad hash-tabell har en kolumn för distribution som hash-nyckeln. Följande kod skapar till exempel en distribuerad hash-tabell med ProductKey som kolumnen distribution.

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
``` 

Att välja en kolumn för distribution är ett viktigt beslut eftersom värdena i den här kolumnen bestämmer hur rader ska distribueras. Det bästa valet beror på flera faktorer, och innefattar vanligen kompromisser. Om du inte väljer den bästa kolumnen första gången, du kan dock använda [Skapa tabell AS Välj (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) att återskapa tabellen med en annan distributionsplats-kolumn. 

### <a name="choose-a-distribution-column-that-does-not-require-updates"></a>Välj en distribution-kolumn som inte kräver uppdateringar
Du kan inte uppdatera en distribution kolumn om du inte ta bort raden och infoga en ny rad med de uppdaterade värdena. Därför ska du markera en kolumn med statiska värden. 

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Välj en distribution kolumn med data som distribuerar jämnt

För bästa prestanda bör alla distributioner har ungefär samma antal rader. När en eller flera distributioner har ett oproportionerligt stort antal rader, Slutför vissa distributioner sin del av en parallell fråga före andra. Eftersom frågan inte kan slutföras förrän alla distributioner har slutfört bearbetningen, är bara varje fråga så snabbt som de långsammaste distributionsplatsen.

- Förskjutning av data innebär att data inte jämnt mellan distributioner
- Bearbetningen skeva innebär att vissa distributioner ta längre tid än andra när du kör parallella frågor. Detta kan inträffa när data är förvrängd.
  
Om du vill utjämna parallell bearbetning, markerar du en kolumn för distribution som:

- **Har många unika värden.** Kolumnen kan ha vissa dubblettvärden. Men tilldelas alla rader med samma värde till samma distributionsplats. Eftersom 60 distributioner bör kolumnen ha minst 60 unika värden.  Antalet unika värden är vanligtvis mycket större.
- **Har inte null-värden eller har bara några null-värden.** Extreme exempelvis om alla värden i kolumnen är NULL, tilldelas alla rader till samma distributionsplats. Därför frågebearbetning är förvrängd till en distributionsplats och omfattas inte av parallell bearbetning. 
- **Är inte en datumkolumn**. Alla data för samma datum hamnar i samma distribution. Om flera användare filtrerar på samma dag, göra alla bearbetning med endast 1 i 60-distributioner. 

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Välj en distribution-kolumn som minimerar dataflyttning

För att få rätt fråga frågor kan flytta data från en beräkningsnod till en annan. Dataflyttning händer ofta när frågor har kopplingar och aggregeringar för distribuerade tabeller. Att välja en kolumn för distribution som hjälper till att minimera dataflyttning är en av de viktigaste strategierna för att optimera prestanda för ditt SQL Data Warehouse.

För att minimera dataflyttning, markerar du en kolumn för distribution som:

- Används i `JOIN`, `GROUP BY`, `DISTINCT`, `OVER`, och `HAVING` satser. När två stora faktatabeller har ofta kopplingar, förbättrar prestanda när du distribuerar båda tabellerna på en av de kopplade kolumnerna.  När en tabell inte används i kopplingar, bör överväga att dela tabellen i en kolumn som är vanliga i den `GROUP BY` satsen.
- Är *inte* används i `WHERE` satser. Detta kan begränsa frågan ska inte köras på alla distributioner. 
- Är *inte* en datumkolumn. WHERE-satserna ofta filtrera efter datum.  När detta sker kan all bearbetning köras på bara några distributioner.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Vad du gör om ingen av kolumnerna som är en bra distribution kolumn

Om ingen av kolumnerna har tillräckligt med distinkta värden för en distribution kan du skapa en ny kolumn som en sammansatt av en eller flera värden. Använd kolumnen sammansatta distributionsplatsen som en kopplingskolumn i frågor för att undvika dataflytt vid körning av fråga.

Nästa steg är att läsa in data i tabellen när du utformar en distribuerad hash-tabell.  För att läsa in vägledning, se [översikt över inläsning](sql-data-warehouse-overview-load.md). 

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Så här avgör du om din distribution kolumnen är bra
När data läses in i en distribuerad hash-tabell, kontrollerar du hur raderna är jämnt mellan de 60-distributioner. Rader per distribution kan variera upp till 10% utan märkbar effekt på prestanda. 

### <a name="determine-if-the-table-has-data-skew"></a>Avgöra om tabellen innehåller data som skeva
Ett snabbt sätt att söka efter data förskjutning är att använda [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql). Följande SQL-kod returnerar antalet rader som lagras i var och en av de 60-distributioner. För belastningsutjämnade prestanda fördelas raderna i tabellen distribuerade jämnt över alla distributioner.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Identifiera vilka tabeller har mer än 10% data förskjutning:

1. Skapa vy dbo.vTableSizes som visas i den [tabeller översikt](sql-data-warehouse-tables-overview.md#table-size-queries) artikel.  
2. Kör följande fråga:

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>Kontrollera frågeplaner för dataförflyttning
En bra distribution-kolumn kan kopplingar och aggregeringar har minimal dataflyttning. Detta påverkar hur kopplingar ska skrivas. En join-kolumner måste vara kolumnen distribution för att få minimal Dataförflyttning för en koppling på två hash-distribuerade tabeller.  När två hash-distribuerade tabeller delta i en kolumn för distribution av samma datatyp, kräver inte kopplingen dataflyttning. Kopplingar kan använda ytterligare kolumner utan att det medför dataflyttning.

Att undvika dataflyttning under en koppling:

- Tabeller som ingår i kopplingen måste vara hash distribueras vid **en** av kolumner som ingår i kopplingen.
- Datatyperna för kopplingskolumnerna måste matcha mellan båda tabellerna.
- Kolumnerna måste kopplas till en equals-operatorn.
- Kopplingstyp får inte vara en `CROSS JOIN`.

För att se om frågor har dataflyttning, kan du titta på frågeplanen.  


## <a name="resolve-a-distribution-column-problem"></a>Lösa problem med distribution kolumn
Du behöver inte matcha skeva i samtliga fall av data. Distribuera data är en fråga för att hitta rätt balans mellan minimera data skeva och dataförflyttning. Det går inte alltid att minimera både förskjutning av data och dataförflyttning. Fördelen med minimal dataflyttning kan ibland uppväger effekten av att data skeva.

Om du vill avgöra om du ska lösa data skeva i en tabell, bör du känna till så mycket som möjligt om datavolymer och frågor i din arbetsbelastning. Du kan använda stegen i den [frågan övervakning](sql-data-warehouse-manage-monitor.md) artikel för att övervaka effekten av skeva på frågeprestanda. Mer specifikt leta efter hur lång tid det tar att stora frågor skulle bli klart på individuella distributioner.

Eftersom du inte kan ändra kolumnen distribution på en befintlig tabell, är det vanliga sättet att lösa förskjutning av data att återskapa tabellen med en annan distributionsplats-kolumn.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Återskapa tabellen med en ny kolumn för distribution
Det här exemplet används [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md) att återskapa en tabell med en annan hash-distribution kolumn.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

## <a name="next-steps"></a>Nästa steg

Använd någon av dessa instruktioner om du vill skapa en distribuerad tabell:

- [Skapa tabell (Azure SQL Data Warehouse)](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [Skapa TABLE AS SELECT (Azure SQL Data Warehouse](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)


