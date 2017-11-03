---
title: Distribuera tabeller i SQL Data Warehouse | Microsoft Docs
description: "Komma igång med att distribuera tabeller i Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: barbkess
editor: 
ms.assetid: 5ed4337f-7262-4ef6-8fd6-1809ce9634fc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/31/2016
ms.author: shigu;barbkess
ms.openlocfilehash: d0e12bf821a81826a20b8db84e76c48fa60ad9b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="distributing-tables-in-sql-data-warehouse"></a>Distribuera tabeller i SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Översikt över][Overview]
> * [Datatyper][Data Types]
> * [Distribuera][Distribute]
> * [Index][Index]
> * [Partition][Partition]
> * [Statistik][Statistics]
> * [Tillfällig][Temporary]
>
>

SQL Data Warehouse är ett distribuerat MPP-databassystem.  Genom att dela upp data- och bearbetningsfunktioner på flera noder kan SQL Data Warehouse erbjuda fantastisk skalbarhet – långt över den i ett enskilt system.  Bestämmer hur du distribuerar dina data i ditt SQL Data Warehouse är en av de viktigaste faktorerna att uppnå bästa prestanda.   Nyckeln till optimala prestanda är minimera dataförflyttning och i sin tur på för att minimera dataflyttning är att välja rätt distributionsstrategi.

## <a name="understanding-data-movement"></a>Förstå dataflyttning
Data från varje tabell fördelas på flera underliggande databaser i en MPP-system.  De mest optimerade frågorna på ett MPP-system kan bara skickas via ska köras på enskilda distribuerade databaser utan interaktion mellan andra databaser.  Anta exempelvis att du har en databas med försäljningsdata som innehåller två tabeller, försäljning och kunder.  Om du har en fråga som ska ansluta till försäljning tabellen kundtabellen och du dividerar både försäljnings- och kunden tabeller upp med kundnummer, lägga till varje kund i en separat databas, lösas frågor som förenar försäljning och kunder i varje databas med ingen kunskap om de andra databaserna.  Däremot om du har delat din försäljningsdata genom ordningsnummer och din kundinformation på kundnummer sedan alla angivna databasen inte motsvarande data för varje kund och därmed om du vill ansluta din försäljningsdata till din kundinformation skulle du behöva hämta data för varje kund från andra databaser.  I den här andra exemplet måste dataflyttning inträffa om du vill flytta kundinformation till försäljning data, så att de kan anslutas.  

Dataflyttning inte alltid dåliga konsekvenser, ibland är det nödvändigt för att lösa en fråga.  Men när den här extra steg kan undvikas, naturligt frågan körs snabbare.  Dataflyttning uppstår vanligen när tabellerna eller aggregeringar utförs.  Ofta behöver du både, medan du optimera för ett scenario som en koppling, måste du fortfarande flytt av data för att lösa för andra scenarier, t.ex. en aggregering.  Tips är att räkna ut som är mindre arbete.  I de flesta fall är distribuerar stora faktatabeller i en kolumn som är ofta kopplade mest effektiva metod för att minska de flesta dataflyttning.  Distribuera data på kopplingskolumner är ett mycket vanligt sätt att minska dataflyttning än distribuerar data på kolumner som ingår i en samling.

## <a name="select-distribution-method"></a>Välj distributionsmetod
I bakgrunden delar dina data i 60 databaser i SQL Data Warehouse.  Varje enskild databas kallas för en **distribution**.  När data läses in i varje tabell, har SQL Data Warehouse kunskap om att dela data mellan dessa 60 distributioner.  

Distributionsmetod definieras på tabellnivå och det finns två alternativ:

1. **Resursallokering** som distribuera data jämnt men slumpmässigt.
2. **Hash-distribuerade** som distribuerar data baserat på hash-värden från en enda kolumn

Som standard när du inte definierar en data-distributionsmetod tabellen kommer att distribueras med hjälp av den **resursallokering** distributionsmetod.  Men när du blir mer avancerade i din implementering, kommer du vilja bör du använda **hash distribuerade** tabeller för att minimera dataflyttning som i sin tur optimerar fråga prestanda.

### <a name="round-robin-tables"></a>Resursallokering tabeller
Använda resursallokering metod distribuerar data är mycket hur det låter.  När data har lästs in, skickas bara varje rad till nästa distributionen.  Den här metoden för att distribuera data kommer alltid slumpmässigt fördela data mycket jämnt över alla distributioner.  Det är ingen sortering under processen resursallokering som placerar dina data.  En distributionsplats för resursallokering kallas ibland ett slumpmässigt hash därför.  Med en distribuerad tabell resursallokering finns inget behov av att förstå informationen.  Därför vara resursallokering tabeller en bra inläsning mål.

Som standard om du väljer Ingen distributionsmetod används distributionsmetod resursallokering.  Medan resursallokering tabeller är enkla att använda, eftersom data distribueras slumpmässigt hela systemet innebär det att systemet inte kan garantera vilken distribution är varje rad på.  Därför måste systemet ibland att anropa en åtgärd för flytt av data för att organisera dina data innan den kan lösa en fråga.  Den här extra steg kan sakta ned dina frågor.

Överväg att använda resursallokering (Round robin) för en tabell i följande scenarier:

* När igång som en enkel startpunkt
* Om det finns ingen uppenbara anslutande nyckel
* Om det inte är bra kandidat kolumn för hash-tabellen distribuerar
* Om tabellen inte delar en gemensam join-nyckel med andra tabeller
* Om kopplingen är mindre viktig än andra kopplingar i frågan
* När tabellen är en tillfällig mellanlagring tabell

Båda dessa exempel skapar en resursallokering tabell:

```SQL
-- Round Robin created by default
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
;

-- Explicitly Created Round Robin Table
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
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

> [!NOTE]
> Resursallokering är bästa praxis anses vara standardtypen för tabell som är explicit i din DDL så att din tabellayout avsikt är tydliga till andra.
>
>

### <a name="hash-distributed-tables"></a>Hash-distribuerade tabeller
Med hjälp av en **Hash distribuerade** algoritmen för att distribuera dina tabeller kan förbättra prestanda för många scenarier genom att minska dataflyttning frågan för närvarande.  Distribuerad hash-tabeller finns tabeller som delas mellan de distribuerade databaser med hjälp av en hash-algoritm på en enda kolumn som du väljer.  Kolumnen distribution är vad avgör hur data delas över dina distribuerade databaser.  Hash-funktionen använder kolumnen distribution för att tilldela distributioner rader.  Hash-algoritm och resulterande distribution är deterministisk.  Som har samma värde med samma datatyp kommer alltid till samma distributionsplats.    

Det här exemplet skapar en tabell som distribueras på-id:

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

## <a name="select-distribution-column"></a>Välj distributionsplatser kolumn
När du väljer att **hash distribuera** en tabell, måste du markera en kolumn för enkel distribution.  När du väljer en kolumn för distribution, finns det tre viktiga faktorer att tänka på.  

Markera en kolumn som används för att:

1. Inte uppdateras
2. Distribuera data jämnt, undvika data skeva
3. Minimera dataflyttning

### <a name="select-distribution-column-which-will-not-be-updated"></a>Välj distributionsplatser kolumn som inte kommer att uppdateras
Distributionskolumner kan inte uppdateras, därför, markera en kolumn med statiska värden.  Om en kolumn måste uppdateras, är vanligtvis inte kandidat bra distribution.  Det finns ett fall där du måste uppdatera en kolumn för distribution, kan du göra det genom att först ta bort raden och sedan lägga till en ny rad.

### <a name="select-distribution-column-which-will-distribute-data-evenly"></a>Välj distributionsplatser kolumn som ska distribuera data jämnt
Eftersom ett distribuerat system utför bara så snabbt som motsvarande långsammaste distribution, är det viktigt att dela upp arbetet jämnt mellan distributioner för att uppnå belastningsutjämnade körning i hela systemet.  Hur arbetet är uppdelad i ett distribuerat system baseras på där data för varje distribution finns.  Detta gör det mycket viktigt att välja rätt distribution kolumnen för att distribuera data så att varje distribution har samma arbete och tar samma tid att slutföra sin del av arbetet.  När arbetet är väl indelat i hela systemet, fördelas data över distributioner.  När data inte är balanserade jämnt, vi kallar detta **data förskjutning**.  

För att dela upp data jämnt och undvika skeva data, Tänk på följande när du väljer din distribution kolumnen:

1. Markera en kolumn som innehåller ett stort antal distinkta värden.
2. Undvik att dela ut data på kolumner med några få distinkta värden.
3. Undvik att dela ut data på kolumner med en hög frekvens av null-värden.
4. Undvik att dela ut data på datumkolumnerna.

Eftersom varje värde hash-kodas till 1 i 60 distributioner, för att uppnå jämn fördelning vill du markera en kolumn som är mycket unik och innehåller mer än 60 unika värden.  Överväg att fall där en kolumn har 40 unika värden bara för att illustrera.  Om den här kolumnen har valts som distribution tangent skulle data för tabellen hamna på 40 distributioner mest lämnar 20 distributioner med inga data och ingen bearbetning för att göra.  Däremot måste de 40 distributioner mer behöver arbeta som om data har jämnt fördelade över 60 distributioner.  Det här scenariot är ett exempel på data skeva.

Varje frågesteg väntar på alla distributioner att slutföra sin del av arbetet i MPP-system.  Om en distribution att mer arbete än de andra, sedan till resursen om de andra distributioner är i stort sett nytta bara väntar på upptagen distribution.  När arbetet inte är jämnt fördelade över alla distributioner, vi kallar detta **bearbetning skeva**.  Förskjutning av bearbetning kommer frågor till långsammare än om arbetsbelastningen kan vara jämnt fördelade över distributioner.  Data skeva leder till skeva bearbetning.

Undvik att distribuera på hög nullbara kolumnen som null-värden hamnar på samma distribution. Distribuera på ett datum också göra förskjutning av bearbetning eftersom alla data för ett visst datum hamnar på samma distribution. Om flera användare kör frågor blir bara alla filtrering på samma dag, och sedan endast 1 i 60 distributioner kommer att göra allt arbete sedan det angivna datumet på en distributionsplats. Frågorna körs sannolikt 60 gånger långsammare än om data har sprida jämnt över alla distributioner i det här scenariot.

När det finns inga bra kandidat kolumner, Överväg att använda resursallokering (round robin) som distributionsmetod för.

### <a name="select-distribution-column-which-will-minimize-data-movement"></a>Välj distributionsplatser kolumn som minimerar dataflyttning
Minimera dataflyttning genom att välja rätt distribution kolumnen är en av de viktigaste strategierna för att optimera prestanda för ditt SQL Data Warehouse.  Dataflyttning uppstår vanligen när tabellerna eller aggregeringar utförs.  Kolumner som används i `JOIN`, `GROUP BY`, `DISTINCT`, `OVER` och `HAVING` satser alla gör för **bra** hash-kandidater för distribution.

Å andra sidan kolumner i den `WHERE` satsen gör **inte** gör för bra hash-kolumnen kandidater eftersom de begränsa vilka distributioner delta i frågan, orsakar bearbetning skeva.  Ett bra exempel på en kolumn som kan vara nära till hands att distribuera på, men ofta kan orsaka denna skeva bearbetning är en datumkolumn.

Generellt sett om du har två stora-faktatabeller är ofta ingår i en koppling kommer du få de flesta prestanda genom att distribuera båda tabellerna på en av de kopplade kolumnerna.  Om du har en tabell som inte är ansluten till en annan stor faktatabell leta till kolumner som är vanliga i den `GROUP BY` satsen.

Det finns några viktiga villkor som måste uppfyllas för att undvika dataflyttning under en koppling:

1. Tabeller som ingår i kopplingen måste vara hash distribueras vid **en** av kolumner som ingår i kopplingen.
2. Datatyperna för kopplingskolumnerna måste matcha mellan båda tabellerna.
3. Kolumnerna måste kopplas till en equals-operatorn.
4. Kopplingstyp får inte vara en `CROSS JOIN`.

## <a name="troubleshooting-data-skew"></a>Felsökningsdata skeva
När tabelldata distribueras med hjälp av metoden hash-distribution finns en risk att vissa distributioner kommer förvrängd om du vill att oproportionerligt mer data än andra. Förskjutning av onödigt stora datamängder kan påverka prestanda för frågor eftersom slutresultatet av distribuerade frågor måste vänta på längst körningstid distributionen är klar. Du kan behöva åtgärda detta beroende på mängden data förskjutning.

### <a name="identifying-skew"></a>Identifiera förskjutning
Ett enkelt sätt att identifiera en tabell som förvrängd är att använda `DBCC PDW_SHOWSPACEUSED`.  Detta är ett mycket snabbt och enkelt sätt att se hur många rader som lagras i var och en av de 60 distributioner av databasen.  Kom ihåg att mest belastningsutjämnade prestanda raderna i tabellen distribuerade ska spridas jämnt över alla distributioner.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Om du frågar de dynamiska hanteringsvyer (DMV) för Azure SQL Data Warehouse kan du utföra en mer detaljerad analys.  Starta genom att skapa vyn [dbo.vTableSizes] [ dbo.vTableSizes] visa med SQL från [tabell översikt] [ Overview] artikel.  När vyn har skapats kan du köra den här frågan för att identifiera vilka tabeller har mer än 10% data förskjutning.

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

### <a name="resolving-data-skew"></a>Matcha data skeva
Inte alla skeva räcker till garanterar en korrigering.  I vissa fall kan prestanda för en tabell i några frågor uppväger skada data skeva.  Om du vill avgöra om du ska lösa data skeva i en tabell, bör du känna till så mycket som möjligt om datavolymer och frågor i din arbetsbelastning.   Ett sätt att titta på effekten av förskjutning är att använda stegen i den [frågan övervakning] [ Query Monitoring] artikel för att övervaka effekten av skeva på frågeprestanda och specifikt påverkan på hur länge frågar ta att slutföra på individuella distributioner.

Distribuera data är en fråga för att hitta rätt balans mellan minimera förskjutning av data och minimera dataflyttning. Dessa kan motverkar mål och ibland du vill behålla data skeva för att minska dataflyttning. Till exempel när kolumnen distribution är ofta delade kolumnen kopplingar och aggregeringar kan kommer du att minimera dataflyttning. Fördelen med minimal dataflyttning kan uppväger effekten av att data skeva.

Det vanliga sättet att lösa förskjutning av data är att återskapa tabellen med en annan distributionsplats-kolumn. Eftersom det inte går att ändra kolumnen distribution på en befintlig tabell, ett sätt att ändra distributionen av en tabell att återskapa den med en [CTAS] [].  Här är två exempel på hur lösa skeva data:

### <a name="example-1-re-create-the-table-with-a-new-distribution-column"></a>Exempel 1: Återskapa tabellen med en ny kolumn för distribution
Det här exemplet används [CTAS] [] för att återskapa en tabell med en annan hash-distribution kolumn.

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

### <a name="example-2-re-create-the-table-using-round-robin-distribution"></a>Exempel 2: Återskapa tabellen med resursallokering (round robin)
Det här exemplet används [CTAS] [] för att återskapa en tabell med resursallokering i stället för en hash-distribution. Den här ändringen genererar data fördelas jämnt på bekostnad av ökade dataflyttning.

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
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
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## <a name="next-steps"></a>Nästa steg
Läs mer om tabelldesign i den [fördela][Distribute], [Index][Index], [Partition][Partition], [datatyper][Data Types], [statistik] [ Statistics] och [temporära tabeller] [ Temporary] artiklar.

En översikt över bästa praxis, se [Metodtips för SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md
[Query Monitoring]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#table-size-queries

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED()]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->
