---
title: Vägledning för prestandajustering för program och databaser
description: Lär dig mer om hur du justerar databasprogram och databaser för prestanda i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: carlrab; jrasnick
ms.date: 03/10/2020
ms.openlocfilehash: 4f30ebe39d86db7076baa8c29b2a5cf060b07bf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255956"
---
# <a name="tune-applications-and-databases-for-performance-in-azure-sql-database"></a>Justera program och databaser efter prestanda i Azure SQL Database

När du har identifierat ett prestandaproblem som du står inför med SQL Database är den här artikeln utformad för att hjälpa dig:

- Finjustera programmet och tillämpa några metodtips som kan förbättra prestanda.
- Justera databasen genom att ändra index och frågor för att arbeta mer effektivt med data.

Den här artikeln förutsätter att du redan har arbetat igenom rekommendationerna för Azure SQL [Database-databasrådgivare](sql-database-advisor.md) och [rekommendationerna](sql-database-automatic-tuning.md)för automatisk justering i Azure SQL Database . Det förutsätter också att du har granskat [En översikt över övervakning och justering](sql-database-monitor-tune-overview.md) och dess relaterade artiklar relaterade till felsökning av prestandaproblem. Dessutom förutsätter den här artikeln att du inte har några PROCESSOR-resurser, som kör-relaterade prestandaproblem som kan lösas genom att öka beräkningsstorleken eller tjänstnivån för att ge mer resurser till databasen.

## <a name="tune-your-application"></a>Finjustera programmet

I traditionella lokala SQL Server, processen för inledande kapacitetsplanering ofta separeras från processen att köra ett program i produktion. Maskinvaru- och produktlicenser köps först och prestandajustering görs efteråt. När du använder Azure SQL Database är det en bra idé att väva samman processen att köra ett program och justera det. Med modellen att betala för kapacitet på begäran kan du ställa in ditt program för att använda de minsta resurser som behövs nu, i stället för överetablering på maskinvara baserat på gissningar av framtida tillväxtplaner för ett program, som ofta är felaktiga. Vissa kunder kanske väljer att inte ställa in ett program och väljer i stället att överetablering av maskinvaruresurser. Den här metoden kan vara en bra idé om du inte vill ändra ett nyckelprogram under en hektisk period. Men att justera ett program kan minimera resurskrav och sänka månadsräkningarna när du använder tjänstnivåerna i Azure SQL Database.

### <a name="application-characteristics"></a>Applikationsegenskaper

Även om Azure SQL Database-tjänstnivåer är utformade för att förbättra prestandastabilitet och förutsägbarhet för ett program, kan vissa metodtips hjälpa dig att ställa in ditt program för att bättre dra nytta av resurserna med en beräkningsstorlek. Även om många program har betydande prestandavinster helt enkelt genom att byta till en högre beräkningsstorlek eller tjänstnivå, behöver vissa program ytterligare justering för att dra nytta av en högre servicenivå. För ökad prestanda bör du överväga ytterligare programjustering för program som har följande egenskaper:

- **Program som har långsam prestanda på grund av "pratsamt" beteende**

  Pratsamma program gör överdrivna dataåtkomståtgärder som är känsliga för nätverksfördröjning. Du kan behöva ändra den här typen av program för att minska antalet dataåtkomståtgärder till SQL-databasen. Du kan till exempel förbättra programmets prestanda genom att använda tekniker som batchbearbetning av ad hoc-frågor eller flytta frågorna till lagrade procedurer. Mer information finns i [Batchfrågor](#batch-queries).

- **Databaser med en intensiv arbetsbelastning som inte kan stödjas av en hel enda dator**

   Databaser som överskrider resurserna för den högsta Premium-beräkningsstorleken kan dra nytta av att skala ut arbetsbelastningen. Mer information finns i [Korsdatabasshardring](#cross-database-sharding) och [Funktionell partitionering](#functional-partitioning).

- **Program som har suboptimala frågor**

  Program, särskilt de i dataåtkomstlagret, som har dåligt inställda frågor kanske inte drar nytta av en högre beräkningsstorlek. Detta inkluderar frågor som saknar en WHERE-sats, som saknar index eller har inaktuell statistik. Dessa program drar nytta av vanliga frågeprestandajusteringstekniker. Mer information finns i [Saknade index](#identifying-and-adding-missing-indexes) och [Frågejustering och antyder](#query-tuning-and-hinting).

- **Program som har suboptimal dataåtkomstdesign**

   Program som har inneboende problem med dataåtkomst samtidighet, till exempel dödläge, kanske inte har en högre beräkningsstorlek. Överväg att minska rundresor mot Azure SQL Database genom att cachelagra data på klientsidan med Azure Caching-tjänsten eller en annan cachelagringsteknik. Se [Cachelagring på programnivå](#application-tier-caching).

## <a name="tune-your-database"></a>Finjustera databasen

I det här avsnittet tittar vi på några tekniker som du kan använda för att finjustera Azure SQL Database för att få bästa prestanda för ditt program och köra den med lägsta möjliga beräkningsstorlek. Vissa av dessa tekniker matchar traditionella metodtips för SQL Server-justering, men andra är specifika för Azure SQL Database. I vissa fall kan du undersöka de förbrukade resurserna för en databas för att hitta områden för att ytterligare justera och utöka traditionella SQL Server-tekniker för att arbeta i Azure SQL Database.

### <a name="identifying-and-adding-missing-indexes"></a>Identifiera och lägga till saknade index

Ett vanligt problem i OLTP-databasens prestanda gäller den fysiska databasdesignen. Ofta utformas och levereras databasscheman utan att testa i stor skala (antingen i inläsning eller i datavolym). Tyvärr kan prestanda för en frågeplan vara godtagbara i liten skala men försämras avsevärt under datavolymer på produktionsnivå. Den vanligaste källan till det här problemet är bristen på lämpliga index för att uppfylla filter eller andra begränsningar i en fråga. Ofta manifesterar saknade index som en tabellsökning när en indexsökning kan räcka.

I det här exemplet använder den valda frågeplanen en genomsökning när en sökning räcker:

```sql
DROP TABLE dbo.missingindex;
CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;
```

![En frågeplan med saknade index](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database kan hjälpa dig att hitta och åtgärda vanliga saknade indexvillkor. DMVs som är inbyggda i Azure SQL Database titta på frågekompileringar där ett index avsevärt skulle minska den beräknade kostnaden för att köra en fråga. Under frågekörning spårar SQL Database hur ofta varje frågeplan körs och spårar det uppskattade gapet mellan den körningsfrågeplan som körs och den inbillade där indexet fanns. Du kan använda dessa DMVs för att snabbt gissa vilka ändringar i din fysiska databasdesign som kan förbättra den totala arbetsbelastningskostnaden för en databas och dess verkliga arbetsbelastning.

Du kan använda den här frågan för att utvärdera potentiella saknade index:

```sql
SELECT
   CONVERT (varchar, getdate(), 126) AS runtime
   , mig.index_group_handle
   , mid.index_handle
   , CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
        (migs.user_seeks + migs.user_scans)) AS improvement_measure
   , 'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
        CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
        (' + ISNULL (mid.equality_columns,'')
        + CASE WHEN mid.equality_columns IS NOT NULL
        AND mid.inequality_columns IS NOT NULL
        THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '') + ')'
        + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement
   , migs.*
   , mid.database_id
   , mid.[object_id]
FROM sys.dm_db_missing_index_groups AS mig
   INNER JOIN sys.dm_db_missing_index_group_stats AS migs
      ON migs.group_handle = mig.index_group_handle
   INNER JOIN sys.dm_db_missing_index_details AS mid
      ON mig.index_handle = mid.index_handle
 ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC
```

I det här exemplet resulterade frågan i det här förslaget:

```sql
CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  
```

När den har skapats väljer samma SELECT-sats en annan plan, som använder en sökning i stället för en genomsökning, och sedan kör planen mer effektivt:

![En frågeplan med korrigerade index](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

Den viktigaste insikten är att IO-kapaciteten för ett delat råvarusystem är mer begränsad än en dedikerad serverdator. Det finns en premie på att minimera onödig I/O för att dra maximal nytta av systemet i DTU för varje beräkningsstorlek på Azure SQL Database-tjänstnivåerna. Lämpliga fysiska databasdesignval kan avsevärt förbättra svarstiden för enskilda frågor, förbättra dataflödet för samtidiga begäranden som hanteras per skalenhet och minimera de kostnader som krävs för att uppfylla frågan. Mer information om det saknade indexet DMVs finns i [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Frågejustering och anvisning

Frågeoptimeraren i Azure SQL Database liknar den traditionella SQL Server-frågeoptimeraren. De flesta av de bästa metoderna för att justera frågor och förstå resonemanget modellbegränsningar för frågan optimizer gäller också för Azure SQL Database. Om du ställer in frågor i Azure SQL Database kan du få ytterligare fördelar med att minska de samlade resurskraven. Ditt program kanske kan köras till en lägre kostnad än en ojusterad motsvarighet eftersom det kan köras med en lägre beräkningsstorlek.

Ett exempel som är vanligt i SQL Server och som även gäller för Azure SQL Database är hur frågeoptimeraren "sniffar" parametrar. Under kompileringen utvärderar frågeoptimeraren det aktuella värdet för en parameter för att avgöra om den kan generera en mer optimal frågeplan. Även om den här strategin ofta kan leda till en frågeplan som är betydligt snabbare än en plan som kompileras utan kända parametervärden, fungerar den för närvarande ofullständigt både i SQL Server och i Azure SQL Database. Ibland sniffas inte parametern och ibland sniffas parametern men den genererade planen är deloptimal för hela uppsättningen parametervärden i en arbetsbelastning. Microsoft innehåller frågetips (direktiv) så att du kan ange avsikt mer medvetet och åsidosätta standardbeteendet för parametersniffning. Om du använder tips kan du ofta åtgärda fall där standardbeteendet för SQL Server eller Azure SQL Database är ofullständigt för en viss kundarbetsbelastning.

Nästa exempel visar hur frågeprocessorn kan generera en plan som är suboptimal både för prestanda- och resurskrav. I det här exemplet visas också att om du använder ett frågetips kan du minska frågekörningstiden och resurskraven för SQL-databasen:

```sql
DROP TABLE psptest1;
CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
   WHILE @a < 20000
   BEGIN
     INSERT INTO psptest1(col2) values (1);
     INSERT INTO psptest1(col2) values (@a);
     SET @a += 1;
   END
   COMMIT TRANSACTION
   CREATE INDEX i1 on psptest1(col2);
GO

CREATE PROCEDURE psp1 (@param1 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1
      WHERE col2 = @param1
      ORDER BY col2;
    END
    GO

CREATE PROCEDURE psp2 (@param2 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
      ORDER BY col2
      OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
   END
   GO

CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
GO
```

Inställningskoden skapar en tabell som har skev datadistribution. Den optimala frågeplanen skiljer sig beroende på vilken parameter som har valts. Tyvärr kompileras inte frågan om på planen baserat på det vanligaste parametervärdet. Så det är möjligt för en suboptimal plan som ska cachelagras och användas för många värden, även när en annan plan kan vara ett bättre planval i genomsnitt. Då frågeplanen skapar två lagrade procedurer som är identiska, förutom att man har en speciell fråga tips.

```sql
-- Prime Procedure Cache with scan plan
EXEC psp1 @param1=1;
TRUNCATE TABLE t1;

-- Iterate multiple times to show the performance difference
DECLARE @i int = 0;
WHILE @i < 1000
   BEGIN
      EXEC psp1 @param1=2;
      TRUNCATE TABLE t1;
      SET @i += 1;
    END
```

Vi rekommenderar att du väntar minst 10 minuter innan du börjar del 2 i exemplet, så att resultaten skiljer sig åt i de resulterande telemetridata.

```sql
EXEC psp2 @param2=1;
TRUNCATE TABLE t1;

DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END
```

Varje del av det här exemplet försöker köra en parameteriserad insert-sats 1 000 gånger (för att generera en tillräcklig belastning att använda som testdatauppsättning). När den kör lagrade procedurer undersöker frågeprocessorn parametervärdet som skickas till proceduren under den första kompileringen (parametern "sniffning"). Processorn cachelagrar den resulterande planen och använder den för senare anrop, även om parametervärdet är annorlunda. Den optimala planen kanske inte används i alla fall. Ibland måste du vägleda optimeraren för att välja en plan som är bättre för det genomsnittliga fallet snarare än det specifika fallet från när frågan först kompilerades. I det här exemplet genererar den ursprungliga planen en "scan"-plan som läser alla rader för att hitta varje värde som matchar parametern:

![Frågejustering med hjälp av en skanningsplan](./media/sql-database-performance-guidance/query_tuning_1.png)

Eftersom vi utförde proceduren med värdet 1 var den resulterande planen optimal för värdet 1 men var suboptimal för alla andra värden i tabellen. Resultatet är sannolikt inte vad du skulle vilja om du skulle välja varje plan slumpmässigt, eftersom planen fungerar långsammare och använder mer resurser.

Om du kör `SET STATISTICS IO` testet `ON`med inställt på görs det logiska skanningsarbetet i det här exemplet bakom kulisserna. Du kan se att det finns 1.148 läsningar som gjorts av planen (vilket är ineffektivt, om det genomsnittliga fallet är att returnera bara en rad):

![Frågejustering med hjälp av en logisk genomsökning](./media/sql-database-performance-guidance/query_tuning_2.png)

Den andra delen av exemplet använder en frågeledtråd för att tala om för optimeraren att använda ett visst värde under kompileringsprocessen. I det här fallet tvingar det frågeprocessorn att ignorera värdet som `UNKNOWN`skickas som parameter och i stället anta . Detta refererar till ett värde som har den genomsnittliga frekvensen i tabellen (ignorerar skeva). Den resulterande planen är en sökbaserad plan som är snabbare och använder färre resurser i genomsnitt än planen i del 1 i det här exemplet:

![Frågejustering med hjälp av ett frågetips](./media/sql-database-performance-guidance/query_tuning_3.png)

Du kan se effekten i **tabellen sys.resource_stats** (det finns en fördröjning från den tidpunkt då du kör testet och när data fyller i tabellen). I det här exemplet körs del 1 under tidsfönstret 22:25:00 och del 2 som utfördes klockan 22:35:00. I det tidigare tidsfönstret användes fler resurser i det tidsfönstret än det senare (på grund av förbättringar av planens effektivitet).

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Exempelresultat för frågejustering](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Även om volymen i det här exemplet är avsiktligt liten, kan effekten av suboptimala parametrar vara betydande, särskilt på större databaser. Skillnaden, i extrema fall, kan vara mellan sekunder för snabba fall och timmar för långsamma fall.

Du kan undersöka **sys.resource_stats** för att avgöra om resursen för ett test använder fler eller färre resurser än ett annat test. När du jämför data separerar du tidpunkten för testerna så att de inte är i samma 5-minutersfönster i **vyn sys.resource_stats.** Målet med övningen är att minimera den totala mängden resurser som används och inte att minimera toppresurserna. I allmänhet minskar optimering av en kod för svarstid också resursförbrukningen. Se till att de ändringar du gör i ett program är nödvändiga och att ändringarna inte negativt påverkar kundupplevelsen för någon som kanske använder frågetips i programmet.

Om en arbetsbelastning har en uppsättning återkommande frågor är det ofta meningsfullt att samla in och validera optimaliteten i dina planval eftersom den driver den minsta resursstorleksenhet som krävs för att vara värd för databasen. När du har validerat den, ibland ompröva planerna för att hjälpa dig att se till att de inte har försämrats. Du kan läsa mer om [frågetips (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="very-large-database-architectures"></a>Mycket stora databasarkitekturer

Innan [hyperskala-tjänstnivån](sql-database-service-tier-hyperscale.md) för enskilda databaser i Azure SQL Database släpptes, används kunder för att nå kapacitetsgränser för enskilda databaser. Dessa kapacitetsgränser finns fortfarande för poolade databaser i elastiska pooler och instansdatabas i hanterade instanser. I följande två avsnitt beskrivs två alternativ för att lösa problem med mycket stora databaser i Azure SQL Database när du inte kan använda tjänstnivån Hyperskala.

### <a name="cross-database-sharding"></a>Fragmentering av korsdatabaser

Eftersom Azure SQL Database körs på varumaskinvara är kapacitetsgränserna för en enskild databas lägre än för en traditionell lokal SQL Server-installation. Vissa kunder använder sharding tekniker för att sprida databasåtgärder över flera databaser när åtgärderna inte passar inom gränserna för en enskild databas i Azure SQL Database. De flesta kunder som använder sharding tekniker i Azure SQL Database dela sina data på en enda dimension över flera databaser. För den här metoden måste du förstå att OLTP-program ofta utför transaktioner som gäller för endast en rad eller en liten grupp rader i schemat.

> [!NOTE]
> SQL Database tillhandahåller nu ett bibliotek som hjälper till med fragmentering. Mer information finns i [Översikt över klientbiblioteket för elastisk databas](sql-database-elastic-database-client-library.md).

Om en databas till exempel har kundnamn, order och orderinformation (till exempel det traditionella exemplet Northwind-databas som levereras med SQL Server) kan du dela upp dessa data i flera databaser genom att gruppera en kund med den relaterade order- och orderdetaljinformationen Information. Du kan garantera att kundens data stannar i en enskild databas. Programmet skulle dela olika kunder mellan databaser, effektivt sprida belastningen över flera databaser. Med sharding kan kunder inte bara undvika den maximala gränsen för databasstorlek, men Azure SQL Database kan också bearbeta arbetsbelastningar som är betydligt större än gränserna för de olika beräkningsstorlekarna, så länge varje enskild databas passar in i dess DTU.

Även om databasshardring inte minskar den sammanlagda resurskapaciteten för en lösning är den mycket effektiv när det gäller att stödja mycket stora lösningar som är spridda över flera databaser. Varje databas kan köras med en annan beräkningsstorlek för att stödja mycket stora, "effektiva" databaser med höga resurskrav.

#### <a name="functional-partitioning"></a>Funktionell partitionering

SQL Server-användare kombinerar ofta många funktioner i en enskild databas. Om ett program till exempel har logik för att hantera lager för ett arkiv kan databasen ha logik som är associerad med lager, spåra inköpsorder, lagrade procedurer och indexerade eller materialiserade vyer som hanterar rapportering i slutet av månaden. Den här tekniken gör det enklare att administrera databasen för åtgärder som säkerhetskopiering, men det kräver också att du lägger upp maskinvaran för att hantera toppbelastningen i alla funktioner i ett program.

Om du använder en skalningsarkitektur i Azure SQL Database är det en bra idé att dela upp olika funktioner i ett program i olika databaser. Genom att använda den här tekniken skalas varje program oberoende av varandra. När ett program blir livligare (och belastningen på databasen ökar) kan administratören välja oberoende beräkningsstorlekar för varje funktion i programmet. Vid gränsen, med den här arkitekturen, kan ett program vara större än en enskild varumaskin kan hantera eftersom belastningen är spridd över flera datorer.

### <a name="batch-queries"></a>Batchfrågor

För program som kommer åt data med hjälp av högvolyms-, frekventa, ad hoc-frågor, spenderas en betydande mängd svarstid på nätverkskommunikation mellan programnivån och Azure SQL Database-nivån. Även när både programmet och Azure SQL Database finns i samma datacenter kan nätverksfördröjningen mellan de två förstoras av ett stort antal dataåtkomståtgärder. Om du vill minska nätverksrundningsresorna för dataåtkomståtgärderna bör du överväga att använda alternativet för att antingen batcha ad hoc-frågorna eller kompilera dem som lagrade procedurer. Om du batchar ad hoc-frågorna kan du skicka flera frågor som en stor batch i en enda resa till Azure SQL Database. Om du kompilerar ad hoc-frågor i en lagrad procedur kan du uppnå samma resultat som om du batchar dem. Med hjälp av en lagrad procedur kan du också dra nytta av att öka chanserna att cachelagrar frågeplanerna i Azure SQL Database så att du kan använda den lagrade proceduren igen.

Vissa program är skrivintensiva. Ibland kan du minska den totala IO-belastningen på en databas genom att överväga hur du batch skriver tillsammans. Ofta är detta så enkelt som att använda explicita transaktioner i stället för automatiska transaktioner i lagrade procedurer och ad hoc-batchar. En utvärdering av olika tekniker som du kan använda finns i [Batching-tekniker för SQL Database-program i Azure](sql-database-use-batching-to-improve-performance.md). Experimentera med din egen arbetsbelastning för att hitta rätt modell för batchbearbetning. Var noga med att förstå att en modell kan ha något annorlunda transaktionskonsekvensgarantier. Att hitta rätt arbetsbelastning som minimerar resursanvändning kräver att du hittar rätt kombination av konsekvens och prestanda kompromisser.

### <a name="application-tier-caching"></a>Cachelagring på programnivå

Vissa databasprogram har lästunga arbetsbelastningar. Cachelagringslager kan minska belastningen på databasen och kan eventuellt minska beräkningsstorleken som krävs för att stödja en databas med hjälp av Azure SQL Database. Med [Azure Cache för Redis](https://azure.microsoft.com/services/cache/), om du har en lästunnlad arbetsbelastning, kan du läsa data en gång (eller kanske en gång per programnivådator, beroende på hur den är konfigurerad) och sedan lagra dessa data utanför din SQL-databas. Detta är ett sätt att minska databasbelastningen (CPU och läsa IO), men det finns en effekt på transaktionskonsekvens eftersom de data som läses från cachen kan vara ur synk med data i databasen. Även i många program är viss nivå av inkonsekvens acceptabel, det är inte sant för alla arbetsbelastningar. Du bör till fullo förstå alla programkrav innan du implementerar en cachelagringsstrategi på programnivå.

## <a name="next-steps"></a>Nästa steg

- Mer information om DTU-baserade tjänstnivåer finns i [DTU-baserad inköpsmodell](sql-database-service-tiers-dtu.md).
- Mer information om vCore-baserade tjänstnivåer finns i [vCore-baserad inköpsmodell](sql-database-service-tiers-vcore.md).
- Mer information om elastiska pooler finns i [Vad är en elastisk Azure-pool?](sql-database-elastic-pool.md)
- Information om prestanda och elastiska pooler finns i [När du ska tänka på en elastisk pool](sql-database-elastic-pool-guidance.md)
