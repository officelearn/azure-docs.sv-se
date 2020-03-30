---
title: Designvägledning för distribuerade tabeller
description: Rekommendationer för att utforma delade och round-robin-distribuerade tabeller i SQL Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 35106e73a3a4a143bf22c72c4fe8ac6798ac5219
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351344"
---
# <a name="guidance-for-designing-distributed-tables-in-sql-analytics"></a>Vägledning för att utforma distribuerade tabeller i SQL Analytics
Rekommendationer för att utforma delade och round-robin-distribuerade tabeller i SQL Analytics.

Den här artikeln förutsätter att du är bekant med datadistribution och datarörelsebegrepp i SQL Analytics.Mer information finns i [MPP-arkitektur (SQL Analytics massively parallel processing).](massively-parallel-processing-mpp-architecture.md) 

## <a name="what-is-a-distributed-table"></a>Vad är en distribuerad tabell?
En distribuerad tabell visas som en enda tabell, men raderna lagras faktiskt över 60 distributioner. Raderna distribueras med en hash- eller round-robin-algoritm.  

**Hash-distribuerade tabeller** förbättrar frågeprestanda på stora faktatabeller och är i fokus för den här artikeln. **Round-robin-bord** är användbara för att förbättra lasthastigheten. Dessa designval har en betydande inverkan på att förbättra fråge- och inläsningsprestanda.

Ett annat tabelllagringsalternativ är att replikera en liten tabell över alla beräkningsnoder. Mer information finns i [Designvägledning för replikerade tabeller](design-guidance-for-replicated-tables.md). Mer snabbt välja bland de tre alternativen finns i Distribuerade tabeller i [tabellöversikten](sql-data-warehouse-tables-overview.md). 

Som en del av tabelldesign, förstå så mycket som möjligt om dina data och hur data efterfrågas.Tänk dig till exempel följande frågor:

- Hur stort är bordet?   
- Hur ofta uppdateras tabellen?   
- Har jag fakta- och dimensionstabeller i en SQL Analytics-databas?   


### <a name="hash-distributed"></a>Hash distribueras
En hash-distribuerad tabell distribuerar tabellrader över beräkningsnoderna med hjälp av en deterministisk hash-funktion för att tilldela varje rad till en [fördelning](massively-parallel-processing-mpp-architecture.md#distributions). 

![Distribuerad tabell](./media/sql-data-warehouse-tables-distribute/hash-distributed-table.png "Distribuerad tabell")  

Eftersom identiska värden alltid hash till samma distribution, har SQL Analytics inbyggd kunskap om radplatserna. SQL Analytics använder den här kunskapen för att minimera datarörelser under frågor, vilket förbättrar frågeprestanda. 

Hash-distribuerade tabeller fungerar bra för stora faktatabeller i ett stjärnschema. De kan ha ett mycket stort antal rader och ändå uppnå hög prestanda. Det finns naturligtvis vissa designöverväganden som hjälper dig att få den prestanda som det distribuerade systemet är utformat för att tillhandahålla. Att välja en bra distributionskolumn är ett sådant övervägande som beskrivs i den här artikeln. 

Överväg att använda en hash-distribuerad tabell när:

- Tabellstorleken på disken är mer än 2 GB.
- Tabellen har frekventa infognings-, uppdaterings- och borttagningsåtgärder. 

### <a name="round-robin-distributed"></a>Rödhake distribueras
En distribuerad round robin-tabell fördelar tabellrader jämnt över alla fördelningar. Tilldelningen av rader till fördelningar är slumpmässig. Till skillnad från hash-distribuerade tabeller garanteras inte rader med samma värden att tilldelas samma fördelning. 

Därför måste systemet ibland anropa en dataförflyttningsåtgärd för att bättre ordna dina data innan den kan lösa en fråga.  Det här extra steget kan göra dina frågor långsammare. För att till exempel gå med i en round-robin-tabell krävs vanligtvis omfördelning av raderna, vilket är en prestandaträff.

Överväg att använda distributionen av round robin för tabellen i följande scenarier:

- När du kommer igång som en enkel utgångspunkt eftersom det är standard
- Om det inte finns någon uppenbar kopplingsnyckel
- Om det inte finns någon bra kandidatkolumn för hash som distribuerar tabellen
- Om tabellen inte delar en gemensam kopplingsnyckel med andra tabeller
- Om kopplingen är mindre viktig än andra kopplingar i frågan
- När tabellen är en tillfällig mellanlagringstabell

Självstudien [Läs in New York-taxicab-data](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) ger ett exempel på att läsa in data i en mellanlagringstabell för round-robin i SQL Analytics.


## <a name="choosing-a-distribution-column"></a>Välja en distributionskolumn
En hash-distribuerad tabell har en distributionskolumn som är hash-tangenten. Följande kod skapar till exempel en hash-distribuerad tabell med ProductKey som distributionskolumn.

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

Att välja en distributionskolumn är ett viktigt designbeslut eftersom värdena i den här kolumnen avgör hur raderna fördelas. Det bästa valet beror på flera faktorer, och vanligtvis innebär kompromisser. Om du inte väljer den bästa kolumnen första gången kan du använda [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) för att återskapa tabellen med en annan distributionskolumn. 

### <a name="choose-a-distribution-column-that-does-not-require-updates"></a>Välj en distributionskolumn som inte kräver uppdateringar
Du kan inte uppdatera en distributionskolumn om du inte tar bort raden och infogar en ny rad med de uppdaterade värdena. Välj därför en kolumn med statiska värden. 

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Välj en distributionskolumn med data som distribueras jämnt

För bästa prestanda bör alla fördelningar ha ungefär samma antal rader. När en eller flera distributioner har ett oproportionerligt stort antal rader avslutar vissa distributioner sin del av en parallell fråga före andra. Eftersom frågan inte kan slutföras förrän alla distributioner har slutförts, är varje fråga bara lika snabb som den långsammaste distributionen.

- Datasnedställning innebär att data inte fördelas jämnt över distributionerna
- Bearbetning skeva innebär att vissa distributioner tar längre tid än andra när du kör parallella frågor. Detta kan inträffa när data är skeva.
  
Om du vill balansera den parallella bearbetningen väljer du en distributionskolumn som:

- **Har många unika värden.** Kolumnen kan ha vissa dubblettvärden. Alla rader med samma värde tilldelas dock samma distribution. Eftersom det finns 60 fördelningar bör kolumnen ha minst 60 unika värden.  Vanligtvis är antalet unika värden mycket större.
- **Har inte NULLs, eller har bara ett fåtal NULLs.** Om alla värden i kolumnen är NULL tilldelas alla rader till samma fördelning om alla värden i kolumnen är NULL. Därför är frågebearbetningen skev till en distribution och drar inte nytta av parallell bearbetning. 
- **Är inte en datumkolumn**. Alla data för samma datum hamnar i samma fördelning. Om flera användare alla filtrerar på samma datum, gör endast 1 av de 60 distributionerna allt bearbetningsarbete. 

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Välj en distributionskolumn som minimerar datarörelser

Om du vill hämta rätt frågeresultatfrågor kan data flyttas från en beräkningsnod till en annan. Dataförflyttning inträffar ofta när frågor har kopplingar och aggregeringar på distribuerade tabeller. Att välja en distributionskolumn som hjälper till att minimera dataförflyttningar är en av de viktigaste strategierna för att optimera prestanda för din SQL Analytics-databas.

Om du vill minimera dataflyttningen väljer du en distributionskolumn som:

- Används i `JOIN` `GROUP BY`, `DISTINCT` `OVER`, `HAVING` , och satser. När två stora faktatabeller har frekventa kopplingar förbättras frågeprestanda när du distribuerar båda tabellerna i en av kopplingskolumnerna.  När en tabell inte används i kopplingar bör du överväga att `GROUP BY` distribuera tabellen i en kolumn som ofta finns i satsen.
- Används *inte* `WHERE` i satser. Detta kan begränsa frågan så att den inte körs på alla distributioner. 
- Är *inte* en datumkolumn. WHERE-satser filtrerar ofta efter datum.  När detta händer kan all bearbetning köras på endast ett fåtal distributioner.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Vad du ska göra när ingen av kolumnerna är en bra distributionskolumn

Om ingen av kolumnerna har tillräckligt med distinkta värden för en distributionskolumn kan du skapa en ny kolumn som en blandning av ett eller flera värden. Om du vill undvika dataflyttning under frågekörning använder du kolumnen sammansatt distribution som en kopplingskolumn i frågor.

När du har utformat en hash-distribuerad tabell är nästa steg att läsa in data i tabellen.  Läs in översikt [.](design-elt-data-loading.md) 

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Så här berättar du om distributionskolumnen är ett bra val
När data har lästs in i en hash-distribuerad tabell kontrollerar du hur jämnt raderna fördelas över de 60 distributionerna. Raderna per fördelning kan variera upp till 10 % utan märkbar inverkan på prestanda. 

### <a name="determine-if-the-table-has-data-skew"></a>Ta reda på om tabellen har datasnedställning
Ett snabbt sätt att söka efter data skeva är att använda [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql). Följande SQL-kod returnerar antalet tabellrader som lagras i var och en av de 60 distributionerna. För balanserad prestanda bör raderna i den distribuerade tabellen fördelas jämnt över alla fördelningar.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Så här identifierar du vilka tabeller som har mer än 10 % datasnedställning:

1. Skapa vy dbo.vTableSizes som visas i [översiktsartikeln Tabeller.](sql-data-warehouse-tables-overview.md#table-size-queries)  
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
    having (max(row_count * 1.000) - min(row_count * 1.000))/max(row_count * 1.000) >= .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>Kontrollera frågeplaner för dataflyttning
En bra distributionskolumn gör det möjligt för kopplingar och aggregeringar att ha minimal datarörelse. Detta påverkar hur kopplingar ska skrivas. Om du vill få minimal dataförflyttning för en koppling i två hash-distribuerade tabeller måste en av kopplingskolumnerna vara distributionskolumnen.  När två hash-distribuerade tabeller kopplas till i en distributionskolumn av samma datatyp kräver kopplingen inte dataförflyttning. Kopplingar kan använda ytterligare kolumner utan att medföra dataförflyttning.

Så här undviker du datarörelser under en koppling:

- Tabellerna som ingår i kopplingen måste hash distribueras **på en** av kolumnerna som deltar i kopplingen.
- Datatyperna för kopplingskolumnerna måste matcha mellan båda tabellerna.
- Kolumnerna måste sammanfogas med en operator som är lika med.
- Kopplingstypen kanske `CROSS JOIN`inte är en .

Om du vill se om frågor upplever dataröra kan du titta på frågeplanen.  


## <a name="resolve-a-distribution-column-problem"></a>Lösa ett problem med distributionskolumnen
Det är inte nödvändigt att lösa alla fall av data skeva. Att distribuera data handlar om att hitta rätt balans mellan att minimera datasnedställning och dataförflyttning. Det är inte alltid möjligt att minimera både datasnedställning och dataförflyttning. Ibland kan fördelen med att ha minimal dataförflyttning uppväga effekten av att ha data skeva.

För att avgöra om du ska lösa datasnedställning i en tabell bör du förstå så mycket som möjligt om datavolymer och frågor i din arbetsbelastning. Du kan använda stegen i artikeln [Frågeövervakning](sql-data-warehouse-manage-monitor.md) för att övervaka hur skeva är på frågeprestanda. Mer specifikt, leta efter hur lång tid det tar stora frågor att slutföra på enskilda distributioner.

Eftersom du inte kan ändra distributionskolumnen i en befintlig tabell är det vanliga sättet att lösa datasnedvning att återskapa tabellen med en annan distributionskolumn.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Återskapa tabellen med en ny distributionskolumn
I det här exemplet används [SKAPA TABELL SOM SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=aps-pdw-2016-au7) för att återskapa en tabell med en annan hash-distributionskolumn.

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

Om du vill skapa en distribuerad tabell använder du någon av dessa satser:

- [SKAPA TABELL (SQL Analytics)](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [SKAPA TABELL SOM SELECT (SQL Analytics)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)


