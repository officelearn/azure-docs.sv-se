---
title: Distribuerade tabeller designriktlinjer – Azure SQL Data Warehouse | Microsoft Docs
description: Rekommendationer för att utforma hash-distribuerad och resursallokering distribuerade tabeller i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 36db91cd7c4dad3c28c0c110ee837ca6d1284959
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45575397"
---
# <a name="guidance-for-designing-distributed-tables-in-azure-sql-data-warehouse"></a>Vägledning för att utforma distribuerade tabeller i Azure SQL Data Warehouse
Rekommendationer för att utforma hash-distribuerad och resursallokering distribuerade tabeller i Azure SQL Data Warehouse.

Den här artikeln förutsätter att du är bekant med datadistribution och begrepp för flytt av data i SQL Data Warehouse.  Mer information finns i [Azure SQL Data Warehouse - arkitektur med massivt parallell bearbetning (MPP)](massively-parallel-processing-mpp-architecture.md). 

## <a name="what-is-a-distributed-table"></a>Vad är en distribuerad tabell?
En distribuerad tabell visas som en enskild tabell, men raderna lagras över 60 distributioner. Raderna distribueras med en hash- eller resursallokering algoritmen.  

**Hash-distribuerad tabeller** förbättra frågeprestanda på stora faktatabeller och är fokus i den här artikeln. **Resursallokeringstabeller-** är användbara för att förbättra hastighet för inläsning. De här valen har en betydande inverkan på förbättra fråga och läsa in prestanda.

En annan tabellagring är att replikera en liten tabell över Compute-noder. Mer information finns i [designriktlinjer för replikerade tabeller](design-guidance-for-replicated-tables.md). Snabbt mellan de tre alternativen finns distribuerade tabeller i den [tabeller översikt](sql-data-warehouse-tables-overview.md). 

Förstå så mycket som möjligt om dina data och hur data är den server som en del av tabelldesign.  Till exempel tänka på följande:

- Hur stora är tabellen?   
- Hur ofta uppdateras tabellen?   
- Måste jag fakta-och dimensionstabeller i ett informationslager?   


### <a name="hash-distributed"></a>Hash-distribueras
En hash-distribuerad tabell distribuerar tabellrader över Compute-noder med en deterministisk hash-funktionen för att tilldela varje rad till en [distribution](massively-parallel-processing-mpp-architecture.md#distributions). 

![Distribuerad tabell](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "distribuerad tabell")  

Eftersom identiska värden hash alltid till samma distributionen, har datalagret det inbyggd kunskap av platserna som rad. SQL Data Warehouse använder den här kunskapen för att minimera dataförflyttning vid frågor, vilket förbättrar prestanda för frågor. 

Hash-distribuerad tabeller fungerar bra för stora faktatabeller i ett star-schema. De kan ha ett stort antal rader och samtidigt som du får höga prestanda. Det finns självklart vissa designöverväganden som hjälper dig att få prestanda distribuerat system är utformad att ge. En sådan hänsyn som beskrivs i den här artikeln är att välja en bra distributionskolumn. 

Överväg att använda en hash-distribuerad när:

- Tabellen är på disken mer än 2 GB.
- Tabellen har ofta infoga, uppdatera och ta bort. 

### <a name="round-robin-distributed"></a>Distribuerade för resursallokering
En distribuerad tabell resursallokering fördelar tabellrader jämnt över alla distributioner. Tilldelningen av rader till distributioner är slumpmässig. Till skillnad från hash-distribuerad tabeller garanteras inte rader med samma värden som ska tilldelas till samma distributionsplats. 

Systemet måste därför ibland att anropa en åtgärd för flytt av data för att organisera dina data innan den kan lösa en fråga.  Den här extra steg kan sakta ned dina frågor. Till exempel ansluta till en resursallokeringstabell vanligtvis måste reshuffling rader, vilket är en träff prestanda.

Överväg att använda resursallokering-distribution för din tabell i följande scenarier:

- När du kommer igång som en enkel startpunkt eftersom det är standard
- Om det finns ingen uppenbar sammanbinder nyckel
- Om det inte passar bra kolumn för hash-distribuera tabellen
- Om tabellen inte delar en gemensam join-nyckel med andra tabeller
- Om kopplingen är mindre viktig än andra kopplingar i frågan
- När tabellen är en tillfällig mellanlagringstabell

Självstudien [belastningen New York-taxidata till Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) ger ett exempel på Läs in data i en mellanlagringstabell (round-robin).


## <a name="choosing-a-distribution-column"></a>Välja en distributionskolumn
En hash-distribuerad tabell har en distributionskolumn som är den hash-nyckeln. Följande kod skapar till exempel en hash-distribuerad tabell med ProductKey som kolumnen distribution.

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

Att välja en distributionskolumn är ett viktigt beslut eftersom värdena i den här kolumnen bestämmer hur raderna distribueras. Det bästa valet beror på flera faktorer och innefattar vanligen kompromisser. Om du inte väljer den bästa kolumnen första gången, du kan dock använda [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) återskapa tabellen med en annan distributionsplats kolumn. 

### <a name="choose-a-distribution-column-that-does-not-require-updates"></a>Välj en distributionskolumn som inte kräver uppdateringar
Du kan inte uppdatera en distributionskolumn om du inte ta bort raden och infoga en ny rad med de uppdaterade värdena. Därför kan markera en kolumn med statiska värden. 

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Välj en distributionskolumn med data som distribuerar jämnt

För bästa prestanda bör alla distributionerna har ungefär samma antal rader. När en eller flera distributioner har ett oproportionerligt stort antal rader, Slutför sin del av en parallell fråga före andra vissa distributioner. Varje fråga är bara så snabbt som långsammaste distributionen eftersom frågan inte kan slutföras förrän alla distributioner har bearbetats.

- Datasnedställning innebär att data inte är jämnt över distributioner
- Bearbetning av skeva innebär att vissa distributioner ta längre tid än andra när du kör parallella frågor. Detta kan inträffa när data är förvrängd.
  
För att jämna ut parallell bearbetning, väljer en distributionskolumn som:

- **Har många unika värden.** Kolumnen kan ha några dubblettvärden. Men tilldelas alla rader med samma värde till samma distributionsplats. Eftersom det finns 60 distributioner är bör kolumnen ha minst 60 unika värden.  Antalet unika värden är vanligtvis mycket större.
- **Har inte null-värden eller har endast några null-värden.** Extreme exempelvis om alla värden i kolumnen är NULL, har alla rader tilldelats samma distribution. Därför frågebearbetning är förvrängd till en distributionsplats och åtnjuter inte parallell bearbetning. 
- **Är inte en datumkolumn**. Alla data för samma datum hamnar i samma distribution. Om flera användare filtrerar på samma datum, gör allt arbete för bearbetning med endast 1 i 60 distributioner. 

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Välj en distributionskolumn som minimerar dataförflyttning

För att få rätt fråga frågor kan flytta data från en beräkningsnod till en annan. Dataförflyttning händer ofta när frågor har kopplingar och aggregeringar på distribuerade tabeller. Välja en distributionskolumn som hjälper till att minimera dataförflyttning är en av de viktigaste strategierna för att optimera prestanda för SQL Data Warehouse.

Välj en distributionskolumn för att minimera dataförflyttning, som:

- Används i `JOIN`, `GROUP BY`, `DISTINCT`, `OVER`, och `HAVING` satser. När två stora faktatabeller har ofta kopplingar, förbättrar prestanda för frågor när du distribuerar båda tabellerna på en av de kopplade kolumnerna.  När en tabell inte används i kopplingar, bör överväga att dela tabellen på en kolumn som är vanliga i den `GROUP BY` satsen.
- Är *inte* används i `WHERE` satser. Detta kan begränsa frågan till kan inte köras i alla distributioner. 
- Är *inte* en datumkolumn. WHERE-satserna filtrera ofta efter datum.  När detta sker kan all bearbetning köras på bara några distributioner.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Vad gör ingen kolumn är en bra distributionskolumn

Om ingen av kolumnerna har tillräckligt med distinkta värden för en distributionskolumn, kan du skapa en ny kolumn som en sammansatt av en eller flera värden. Undvik dataförflyttning vid körning av fråga genom att använda sammansatta distributionskolumn som en kopplingskolumn i frågor.

När du utformar en hash-distribuerad tabell är nästa steg att läsa in data i tabellen.  Vägledning för inläsning av, finns i [översikt över inläsning](sql-data-warehouse-overview-load.md). 

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Så här avgör du om din distributionskolumn är ett bra alternativ
När data läses in i en hash-distribuerad tabell, kontrollerar du om du vill se hur raderna är jämnt över 60 distributioner. Rader per distribution kan variera upp till 10% utan en märkbar effekt på prestanda. 

### <a name="determine-if-the-table-has-data-skew"></a>Avgöra om tabellen innehåller data som förskjuta
Ett snabbt sätt att kontrollera om datasnedställning är att använda [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql). Följande SQL-kod returnerar antalet rader som lagras i varje 60 distributioner. För belastningsutjämnade prestanda ska raderna i din distribuerad tabell vara jämnt fördelade över alla distributioner.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Att identifiera vilka tabeller som har fler än 10% datasnedställning:

1. Skapa vy-dbo.vTableSizes som visas i den [tabeller översikt](sql-data-warehouse-tables-overview.md#table-size-queries) artikeln.  
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
En bra distributionskolumn kan kopplingar och aggregeringar har minimal dataförflyttning. Detta påverkar hur kopplingar ska skrivas. För att få minimal Dataförflyttning för en koppling till två hash-distribuerad tabeller kan måste en av de kopplade kolumnerna vara kolumnen distribution.  När två tabeller för hash-distribuerad ansluter till en kolumn för distribution av samma datatyp kan kräver kopplingen inte dataförflyttning. Kopplingar kan använda ytterligare kolumner utan att fakturera dataförflyttning.

Undvik dataförflyttning under en koppling:

- Tabeller som är inblandade i kopplingen måste vara hash-distribueras på **en** av kolumner som ingår i kopplingen.
- Datatyperna för de kopplade kolumnerna måste matcha mellan båda tabellerna.
- Kolumnerna måste vara ansluten med ett equals-operatorn.
- Kopplingstyp kanske inte är en `CROSS JOIN`.

Om du vill se om frågor som har dataförflyttning, kan du titta på frågeplanen.  


## <a name="resolve-a-distribution-column-problem"></a>Lösa ett problem med distribution kolumn
Du behöver inte matcha förskjuta i samtliga fall av data. Distribuera data är en fråga om att hitta rätt balans mellan minimerar datasnedställning och dataförflyttning. Det går inte alltid att minimera både datasnedställning och dataförflyttning. Fördelen med att integrera minimal dataförflyttning kan ibland uppväga konsekvensen av att förskjuta data.

När du ska välja om du bör åtgärda data skeva i en tabell, bör du förstå så mycket som möjligt om datavolymer och frågor i din arbetsbelastning. Du kan använda stegen i den [fråga övervakning](sql-data-warehouse-manage-monitor.md) artikeln om du vill övervaka effekten av skeva på frågeprestanda. Mer specifikt leta efter hur lång tid det tar att stora frågor skulle bli klart på enskilda distributioner.

Eftersom du inte kan ändra kolumnen distribution på en befintlig tabell, är det vanliga sättet att lösa datasnedställning att återskapa tabellen med en annan distributionsplats kolumn.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Återskapa tabellen med en ny distributionskolumn
Det här exemplet används [CREATE TABLE AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=aps-pdw-2016-au7) återskapa en tabell med en annan hash-distributionskolumn.

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

Använd någon av dessa instruktioner för att skapa en distribuerad tabell:

- [Skapa tabell (Azure SQL Data Warehouse)](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [Skapa TABLE AS SELECT (Azure SQL Data Warehouse](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)


