---
title: Azure SQL Database prestandajustering vägledning | Microsoft Docs
description: Lär dig mer om hur du använder rekommendationer för att förbättra prestanda för Azure SQL Database-frågor.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 2956dfab3b9c1e6e8de54648dae9d2be99788ac2
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309223"
---
# <a name="tuning-performance-in-azure-sql-database"></a>Justera prestanda i Azure SQL Database

Azure SQL Database tillhandahåller [rekommendationer](sql-database-advisor.md) att du kan använda för att förbättra prestandan för din databas eller så kan du låta Azure SQL Database [automatiskt anpassas till ditt program](sql-database-automatic-tuning.md) och tillämpa ändringar som förbättrar prestandan för din arbetsbelastning.

Du har inte några tillämpliga rekommendationer och du fortfarande har problem med prestanda, du kan använda följande metoder för att förbättra prestanda:
- Öka tjänstnivåer i din [DTU-baserade inköpsmodell](sql-database-service-tiers-dtu.md) eller [vCore-baserade inköpsmodell (förhandsgranskning)](sql-database-service-tiers-vcore.md) att ge mer resurser till din databas.
- Finjustera ditt program och tillämpa några metoder som kan förbättra prestanda. 
- Finjustera databasen genom att ändra index och frågor att mer effektivt arbeta med data.

Dessa är manuella metoder eftersom måste du bestämma hur mycket resurser som uppfyller dina behov. Annars skulle du behöva skriva om programmet eller koden för databasen och distribuera ändringarna.

## <a name="increasing-performance-tier-of-your-database"></a>Öka prestandanivån för din databas

Azure SQL Database erbjuder två köp modeller, en [DTU-baserade inköpsmodell](sql-database-service-tiers-dtu.md) och en [vCore-baserade inköpsmodell (förhandsgranskning)](sql-database-service-tiers-vcore.md) som du kan välja från. Varje tjänstnivå isolerar strikt resurser att din SQL-databas kan använda och garanterar förutsägbar prestanda för den servicenivån. Vi erbjuder vägledning som hjälper dig att välja tjänstnivån för ditt program i den här artikeln. Dessutom diskuterar vi sätt att du kan finjustera ditt program för att få ut mesta möjliga av Azure SQL Database.

> [!NOTE]
> Den här artikeln fokuserar på prestanda vägledning för enskilda databaser i Azure SQL Database. Anvisningar prestanda rör elastiska pooler finns [pris- och prestandaöverväganden för elastiska pooler](sql-database-elastic-pool-guidance.md). Observera dock att du kan tillämpa många av rekommendationerna i den här artikeln prestandajustering för databaser i en elastisk pool och få liknande prestandafördelarna.
> 

* **Grundläggande**: det grundläggande service tier erbjudanden goda prestanda förutsägbarhet för varje databas, timme över timme. I en grundläggande databas stöder tillräckligt med resurser för goda prestanda i en liten databas som inte har flera samtidiga begäranden. Vanliga användningsområden när du använder grundläggande tjänstnivå är:
  * **Du precis har börjat med Azure SQL Database**. Program som är under utveckling ofta behöver inte högpresterande nivåer. Basic-databaser är en perfekt miljö för utveckling eller testning vid ett lågt pris.
  * **Du har en databas med en enda användare**. Program som vanligtvis associerar en användare med en-databas har inte hög kraven för samtidighet och prestanda. Dessa program lämpar sig för grundläggande tjänstnivån.
* **Standard**: den Standard-tjänstnivå erbjuder bättre prestanda, förutsägbarhet och ger bra prestanda för databaser som har flera samtidiga förfrågningar, som arbetsgrupp och webbprogram. När du väljer en Standard-nivån tjänstdatabasen, du kan ändra storlek på databasen appen baserat på förutsägbar prestanda, minut över minut.
  * **Databasen har flera samtidiga förfrågningar**. Program som tjänsten mer än en användare åt gången vanligtvis måste högre prestandanivåer. Exempelvis är arbetsgrupps- eller webbappar program som har låg medelhög i/o-trafik krav som stöder flera samtidiga frågor bra kandidater för Standard-tjänstnivå.
* **Premium**: den premiumnivån erbjuder förutsägbara prestanda sekund över sekund, för varje Premium eller Business kritiska (förhandsgranskning)-databas. När du väljer premiumnivån storleken du databasprogrammet utifrån belastning för den här databasen. Planen tar bort fall i vilka prestanda varians kan orsaka små frågor tar längre tid än förväntat i känslig för fördröjningar åtgärder. Den här modellen kan förenkla utvecklings- och validering cykler för program som behöver göra starkt uttalanden om resursbehov för belastning, prestanda varians eller svarstid. Premium-tjänsten nivå används oftast har en eller flera av följande egenskaper:
  * **Hög belastning**. Ett program som kräver betydande CPU, minne eller in-/ utdata (I/O) att slutföra dessa åtgärder kräver en dedikerad, högpresterande nivå. Till exempel är en databasåtgärd känt att använda flera processorkärnor en längre tid en kandidat för premiumnivån.
  * **Många samtidiga förfrågningar**. Vissa databasprogram tjänst många samtidiga förfrågningar, exempelvis när du hanterar en webbplats som har en hög trafik. Basic och Standard tjänstnivåer begränsa antalet samtidiga förfrågningar per databas. Program som kräver fler anslutningar måste du välja en lämplig Reservationsstorleken att hantera det maximala antalet begäranden som behövs.
  * **Låg latens**. Vissa program behöver garantera ett svar från databasen i minimal tid. Om en specifik lagrad procedur anropas som en del av en bredare kund-åtgärd, kanske ett krav för att ha en returtyp från anropet i fler än 20 millisekunder 99 procent av tiden. Den här typen av program fördelar från premiumnivån, se till att nödvändiga datorkraft är tillgänglig.

Servicenivåer som du behöver för SQL-databasen beror på belastningskraven belastning för varje resursdimension. Vissa program använder en trivial mängd en enskild resurs, men har betydande krav för andra resurser.

### <a name="service-tier-capabilities-and-limits"></a>Tjänsten funktioner och begränsningar

På varje tjänstnivå ställa du in prestanda, så att du har möjlighet att betala endast för kapacitet du behöver. Du kan [justera kapaciteten](sql-database-service-tiers-dtu.md), uppåt eller nedåt när arbetsbelastningen ändras. Till exempel om din databas arbetsbelastning är hög under perioder som jul tillbaka till skolan, kan du öka prestandanivån för databasen för en viss tid, juli via September. Du kan minska den när belastning-säsongen avslutas. Du kan minimera du betalar genom att optimera din molnmiljö till säsongsvärdet för ditt företag. Den här modellen fungerar också bra för programvara produkten versionen cykler. Ett test-team kan allokera kapacitet när det testa körs, och släpper den kapaciteten när de är klara testning. I en begäran modell kapacitet betalar du för kapacitet du behöver den och undvika att förlora på dedicerade resurser som du kan använda sällan.

### <a name="why-service-tiers"></a>Varför tjänstnivåer?
Även om varje arbetsbelastning i databasen kan skilja sig, är syftet med tjänstnivåer att tillhandahålla prestanda förutsägbarhet på olika prestandanivåer. Kunder med stora databasen resurskrav kan arbeta i en mer dedikerade datormiljö.

## <a name="tune-your-application"></a>Finjustera ditt program
I traditionella lokala SQL Server är av inledande kapacitetsplanering ofta skild från processen med att köra ett program i produktion. Maskin- och licenser köps först och prestandajustering görs efteråt. När du använder Azure SQL Database är en bra idé att interweave processen för att köra ett program och justera den. Du kan finjustera programmet så att de lägsta resurser som krävs nu, i stället för överetablering på maskinvara baserat på gissningar framtida tillväxt planer för ett program som ofta är felaktiga med modellen av betalar för kapacitet på begäran. Vissa kunder kan välja att inte Finjustera ett program och i stället välja att overprovision maskinvaruresurser. Den här metoden kan vara en bra idé om du inte vill ändra ett program som nyckel under en period som är upptagen. Men inställning av ett program kan minimera resurskraven och lägre månatliga växlar när du använder tjänstnivåer i Azure SQL Database.

### <a name="application-characteristics"></a>Egenskaper för programmet
Även om Azure SQL Database servicenivåer är utformade för att förbättra prestanda stabilitet och förutsägbarhet för ett program, kan några rekommendationer hjälpa dig justera programmet bättre dra nytta av resurser på prestandanivå. Även om många program har betydande prestandavinster genom att helt enkelt växla till en högre prestandanivå eller tjänstnivå, vissa program behöver finjusteringar om du vill dra nytta av en högre servicenivå. Överväg att ytterligare finjustera för program som har följande egenskaper för bättre prestanda:

* **Program som har långsam prestanda på grund av ”chatty” beteende**. Chatty program Se onödigt stora datamängder åtkomståtgärder som är känsliga för nätverks-svarstid. Du kan behöva ändra dessa typer av program för att minska antalet dataåtgärder för åtkomst till SQL-databasen. Exempelvis kan du förbättra programmets prestanda genom att använda tekniker som batchbearbetning ad hoc-frågor eller flytta frågorna till lagrade procedurer. Mer information finns i [Batch-frågor](#batch-queries).
* **Databaser med en intensiv arbetsbelastning som inte stöds av en enskild dator hela**. Databaser som överskrider resurser på översta nivån i Premium prestanda kan ha nytta av att skala ut arbetsbelastningen. Mer information finns i [flera databaser horisontell partitionering](#cross-database-sharding) och [funktionella partitionering](#functional-partitioning).
* **Program som har något sämre frågor**. Program, särskilt de i dataåtkomstlagret som har dåligt justerade frågor kan inte dra nytta av en högre prestandanivå. Detta inkluderar frågor som saknar en WHERE-sats, saknar index eller vara inaktuell statistik. Dessa program utnyttja standardfråga prestandajustering tekniker. Mer information finns i [saknas index](#identifying-and-adding-missing-indexes) och [fråga justera och bl a](#query-tuning-and-hinting).
* **Program som har något sämre data åt design**. Program som har inbyggd databassamtidighet åtkomstproblem, till exempel deadlocking kan inte dra nytta av en högre prestandanivå. Överväg att minska sändningar mot Azure SQL Database av cachelagring på klientsidan med tjänsten Azure Caching eller en annan teknik för cachelagring. Se [programmet nivån cachelagring](#application-tier-caching).

## <a name="tune-your-database"></a>Finjustera din databas
I det här avsnittet ska titta vi på några metoder som du kan använda för att justera Azure SQL Database för att få bästa möjliga prestanda för programmet och kör det på den lägsta nivån möjliga prestanda. Vissa av dessa tekniker matchar traditionella SQL Server justera bästa praxis, men andra som är specifika för Azure SQL Database. I vissa fall kan undersöka du förbrukade resurser för en databas för att hitta områden för att ytterligare finjustera och utöka traditionella SQL Server-tekniker fungerar i Azure SQL Database.

### <a name="identify-performance-issues-using-azure-portal"></a>Identifiera prestandaproblem med hjälp av Azure portal
Följande verktyg i Azure-portalen kan hjälpa dig att analysera och åtgärda prestandaproblem med SQL-databasen:

* [Query Performance Insight](sql-database-query-performance.md)
* [SQL Database Advisor](sql-database-advisor.md)

Azure-portalen har mer information om båda dessa verktyg och hur de används. För att effektivt diagnostisera och åtgärda problem, rekommenderar vi att du först försöka verktyg i Azure-portalen. Vi rekommenderar att du använder den manuella justera metoder som sedan diskuterar vi för index och frågejusteringar i särskilda fall saknas.

Mer information om hur du identifierar problem i Azure SQL Database på [prestandaövervakning](sql-database-single-database-monitor.md) artikel.

### <a name="identifying-and-adding-missing-indexes"></a>Identifiera och lägga till index som saknas
Ett vanligt problem i OLTP databasprestanda relaterar till den fysiska databasdesignen. Ofta databasen scheman designas och levereras utan att testa i skala (antingen i belastning eller datavolym). Tyvärr prestanda för en frågeplan användas i liten skala men försämras avsevärt under produktion nivå datavolymer. Den vanligaste orsaken till problemet är brist på lämplig index att uppfylla filter eller andra begränsningar i en fråga. Saknade index manifest som en tabell skanna ofta när ett index seek kan vara tillräckligt.

I det här exemplet använder valda frågeplanen en genomsökning när en sökning skulle vara tillräckligt:

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

![En frågeplan med index som saknas](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database kan hjälpa dig att hitta och korrigera vanliga saknas index-villkor. Av DMV: er som är inbyggda i Azure SQL Database titta på frågekompileringar som skulle ett index avsevärt minska den beräknade kostnaden för att köra en fråga. Vid körning av fråga SQL-databas håller reda på hur ofta varje frågeplan körs och spårar uppskattade mellanrummet mellan verkställande frågeplanen och det imagined där index fanns. Du kan använda dessa av DMV: er för att snabbt gissa vilka ändringar i utformningen fysiska databasen kan förbättra den övergripande arbetsbelastning kostnaden för en databas och dess faktiska arbetsbelastningen.

Du kan använda den här frågan för att utvärdera potentiella index som saknas:

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

Frågan resulterade i den här i det här exemplet:

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

När den har skapats, hämtar en annan plan som använder en Målsökning i stället för en genomsökning och utför planen mer effektivt att samma SELECT-uttryck:

![En frågeplan med korrigerade index](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

Den viktiga information är att i/o-kapaciteten för ett delat, vara system är mer begränsad än den som en dedikerad server-dator. Det finns en premium på Minimera onödiga IO du drar maximal nytta av systemet i DTU för varje prestandanivå Azure SQL Database servicenivåer. Lämplig fysiska databasdesign val kan avsevärt förbättra svarstiden för enskilda frågor, förbättra systemets genomflöde samtidiga förfrågningar som hanteras per skalningsenhet och minimera kostnader som krävs för att uppfylla frågan. Mer information om saknas index av DMV: er finns [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Frågejusteringar och bl a
Frågeoptimeraren i Azure SQL Database liknar Frågeoptimeringen traditionella SQL Server. De flesta av bästa praxis för inställning av frågor och förstå motivera modellen begränsningar för Frågeoptimeringen gäller även för Azure SQL Database. Om du finjustera frågor i Azure SQL Database, kan du få ytterligare fördelen med att minska behovet av sammanställda resursdata. Programmet kanske kan köras på en lägre kostnad än untuned motsvarande eftersom det kan köras på en lägre prestandanivå.

Ett exempel som är vanliga i SQL Server och som också gäller till Azure SQL Database är hur Frågeoptimeringen ”lyssnar” parametrar. Under kompilering utvärderar Frågeoptimeringen det aktuella värdet för en parameter för att avgöra om det kan generera en mer optimala frågeplan. Även om den här strategin ofta kan leda till en frågeplan är betydligt snabbare än en plan som kompileras utan kända parametervärden, för närvarande fungerar imperfectly både i SQL Server och i Azure SQL Database. Parametern är ibland inte någon lyssnar, och ibland parametern någon lyssnar men genererade planen är något sämre för en fullständig uppsättning parametervärden i en arbetsbelastning. Microsoft inkluderar frågetips (direktiven) så att du kan ange avsikt mer avsiktligt och åsidosätta standardbeteendet för parametern kontroll. Om du använder tips, ofta, kan du åtgärda fall där standardbeteendet för SQL Server eller Azure SQL Database är ofullständig för en viss kund-arbetsbelastning.

I nästa exempel visas hur frågeprocessorn kan generera en plan som är något sämre både för prestanda och resurskraven. Det här exemplet visar också att du kan minska Frågekörningen och resursen kraven för SQL-databasen om du använder en frågetipset:

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

Koden installationsprogrammet skapar en tabell som har förvrängd Datadistribution. Optimal frågeplanen skiljer sig åt beroende på vilken parameter är markerad. Planen funktionssätt för cachelagring inte tyvärr alltid kompilera om frågan baserat på de vanligaste parametervärdet. Det är därför möjligt för en något sämre plan ska cachelagras och användas för många värden, även om ett annat schema kan vara bra plan i genomsnitt. Sedan skapar frågeplanen två lagrade procedurer som är identiska, förutom att en har en särskild frågetipset.

**Exempel, del 1**

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

**Exempelvis del 2**

(Vi rekommenderar att du vänta minst 10 minuter innan du börjar del 2 av det här exemplet så att resultatet är distinkt i den resulterande telemetridata.)

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Varje del av det här exemplet försöker att köra parametriserade insert-instruktionen 1 000 gånger (om du vill generera en tillräcklig belastning som ska användas som en datauppsättning för test). När den körs lagrade procedurer, undersöker frågeprocessorn parametervärdet som skickas till proceduren under sin första kompilering (parametern ”identifiering”). Processorn cachelagrar planen och använder den för senare anrop, även om parametervärdet är olika. Optimal planen kan inte användas i samtliga fall. Ibland måste du guide optimering för att välja en plan som är bättre för genomsnittlig fallet i stället för specifika fall från när frågan kompilerades först. I det här exemplet genererar den ursprungliga planen en ”genomsökning” plan som läser alla rader för att söka efter varje värde som matchar parametern:

![Fråga justera med hjälp av en plan för sökning](./media/sql-database-performance-guidance/query_tuning_1.png)

Eftersom vi köra proceduren med hjälp av värdet 1 i planen är optimala för värdet 1 men har något sämre för alla andra värden i tabellen. Resultatet förmodligen inte vad du kan om du vill välja varje plan slumpmässigt eftersom planen utför långsammare och använder mer resurser.

Om du kör testet med `SET STATISTICS IO` inställd på `ON`, logiska genomsökning arbetet i det här exemplet utförs i bakgrunden. Du kan se att det finns 1,148 läsningar av planen (vilket är ineffektiv om genomsnittlig fallet är att returnera en rad):

![Fråga inställning genom att använda en logisk genomsökning](./media/sql-database-performance-guidance/query_tuning_2.png)

Den andra delen av exemplet används en frågetipset ska berätta för optimering för att använda ett specifikt värde under kompilering. I så fall tvingas frågeprocessorn att ignorera det värde som skickas som parameter, och i stället att anta `UNKNOWN`. Detta refererar till ett värde som har den genomsnittliga frekvensen i tabellen (ignoreras skeva). I planen är en plan med seek som är snabbare och använder färre resurser i genomsnitt än planen i del 1 i det här exemplet:

![Frågejusteringar med hjälp av en ledtråd för frågan](./media/sql-database-performance-guidance/query_tuning_3.png)

Du kan se effekten i den **sys.resource_stats** tabellen (det finns en fördröjning vid tiden för att köra testet och när data fylls i tabell). För det här exemplet, del 1 körs under tidsperioden 22:25:00 och del 2 utförs 22:35:00. Tidigare tidsfönstret används mer resurser under den tidsperioden än det senare (på grund av planen effektivitet förbättringar).

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Prestandajustering exempel frågeresultat](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Även om volymen i det här exemplet är avsiktligt liten kan effekten av något sämre parametrarna vara betydande, särskilt på större databaser. Skillnaden i extrema fall kan vara mellan sekunder för snabb fall och timmar för långsam fall.
> 
> 

Du kan undersöka **sys.resource_stats** att avgöra om resursen för ett test använder fler eller färre resurser än en annan test. När du jämför data separata tidtagningen av testerna så att de inte ingår i samma 5 minuter fönster i den **sys.resource_stats** vyn. Syftet med den här övningen är att minska den totala mängden resurser som används och inte för att minimera topp-resurser. I allmänhet Optimera ett kodavsnitt för fördröjning minskar också risken för förbrukning av nätverksresurser. Kontrollera att de ändringar du gör att ett program är nödvändiga, och att ändringarna inte negativt påverkar användarupplevelsen i någon som använder frågetips i programmet.

Om en arbetsbelastning har en uppsättning frågor upprepas, klokt ofta det att fånga och validera begränsningar av dina val för planen eftersom den styr minsta resurs storleksenheten som krävs för att vara värd för databasen. När du validera det ibland på nytt granska planer för att hjälpa dig att se till att de inte har degraderats. Du kan lära dig mer om [fråga tips (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Flera databaser horisontell partitionering
Eftersom Azure SQL Database körs på vanlig maskinvara, är kapacitetsbegränsningar för en enskild databas lägre än för en traditionella lokala SQL Server-installation. Vissa kunder använder tekniker för horisontell partitionering för att sprida databasåtgärder i flera databaser när åtgärderna inte ryms inom ramen för en enskild databas i Azure SQL Database. De flesta kunder som använder tekniker för horisontell partitionering i Azure SQL Database dela sina data på en enda dimension på flera databaser. För den här metoden måste du förstå att OLTP ofta programmens transaktioner som tillämpas på bara en rad eller en liten grupp av rader i schemat.

> [!NOTE]
> SQL-databasen innehåller nu ett bibliotek för hjälp vid delning. Mer information finns i [översikt över klientbibliotek för elastisk databas](sql-database-elastic-database-client-library.md).
> 
> 

Till exempel om en databas har kundnamn, ordning och orderinformationen (till exempel traditionella exempel Northwind-databasen som levereras med SQL Server), kan du dela dessa data i flera databaser genom att gruppera en kund med relaterade ordning och ordning detaljer information. Du kan garantera att kundens data förblir i en enskild databas. Programmet vill dela upp olika kunder på databaser, effektivt sprida belastningen över flera databaser. Kunder kan inte bara undvika storleksgränsen maximala databas med horisontell partitionering, men Azure SQL Database kan också bearbeta arbetsbelastningar som är betydligt större än gränserna för olika prestandanivåer så länge varje enskild databas passar in i dess DTU.

Horisontell partitionering databasen inte minska sammanställda resursdata kapacitet för en lösning, är men högeffektiv stöd för mycket stora lösningar som kan sträcka sig över flera databaser. Varje databas kan köra på en annan prestandanivå som stöd för mycket stora ”effektiva” databaser med höga resurskrav.

### <a name="functional-partitioning"></a>Funktionell partitionering
SQL Server-användare kombinera ofta många funktioner i en enskild databas. Till exempel om ett program har logik för att hantera lager för en butik, kanske databasen logiken som är associerad med inventering, spårning inköps, lagrade procedurer och indexerade eller materialiserade vyer som hanterar rapportering sista månad. Den här tekniken gör det lättare att administrera databasen för åtgärder som säkerhetskopiering, men det kräver att maskinvara för att hantera den extra belastningen över alla funktioner i ett program du också.

Om du använder en skalbar arkitektur i Azure SQL Database är en bra idé att dela olika funktioner för ett program till en annan databas. Med den här tekniken kan skalas varje program oberoende av varandra. Som ett program blir busier (och ökar belastningen på databasen), kan administratören välja oberoende prestandanivåer för varje funktion i programmet. Ett program kan vara större än en enskild vara dator kan hantera eftersom belastningen är fördelade på flera datorer på gränsen med den här arkitekturen.

### <a name="batch-queries"></a>Batch-frågor
För program som har åtkomst till data med hjälp av stora volymer, ofta ad hoc-frågor till en stor mängd svarstid kan hänföras nätverkskommunikation mellan program-nivå och Azure SQL Database-nivå. Även om både program- och Azure SQL-databas finns i samma datacenter, kan Nätverksfördröjningen mellan två förstoras av ett stort antal data åtkomståtgärder. Round på resor åtkomståtgärder för att minska nätverket, bör du använda alternativet att antingen batch ad hoc-frågor eller för att sammanställa dem som lagrade procedurer. Om du batch ad hoc-frågor, kan du skicka flera frågor som en stor batch i en enda resa till Azure SQL Database. Om du kompilerar ad hoc-frågor i en lagrad procedur kan du uppnå samma resultat som om du batch-dem. Använda en lagrad procedur ger dig även att kunna utnyttja ökar risken för cachelagring frågeplaner i Azure SQL Database så att du kan använda den lagrade proceduren igen.

Vissa program är processorintensiva skrivning. Ibland kan du minska den totala i/o-belastningen på en databas genom att beakta så grupperas skrivningar. Oftast är det enkelt med explicita transaktioner i stället för automatiskt genomförande transaktioner i lagrade procedurer och ad hoc-batchar. En utvärdering av olika metoder som du kan använda finns i [batchbearbetning tekniker för SQL Database-program i Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Experimentera med arbetsbelastningen att hitta rätt modellen för batchbearbetning. Se till att förstå att en modell kan ha lite olika transaktionella konsekvens garanterar. Hitta rätt arbetsbelastningen som minimerar Resursanvändning kräver att hitta rätt kombination av konsekvens och prestanda avvägningarna.

### <a name="application-tier-caching"></a>Programmet skikt cachelagring
Vissa databasprogram har läs-frekventa arbetsbelastningarna. Cachelagring lager kan minska belastningen på databasen och kan minska prestandanivå som krävs för att stödja en databas med hjälp av Azure SQL Database. Med [Azure Redis-Cache](https://azure.microsoft.com/services/cache/), om du har en Läs frekventa arbetsbelastning, kan du läsa data en gång (eller kanske en gång per program skikt dator, beroende på hur den är konfigurerad), och sedan lagra dessa data utanför din SQL-databas. Detta är ett sätt att minska databasbelastningen (processor och Läs IO), men det finns en effekt på transaktionskonsekvens eftersom de data som läses från cachen kanske är inte synkroniserade med data i databasen. Även om vissa andelen inkonsekvens är acceptabel för många program, som gäller inte för alla arbetsbelastningar. Du bör till fullo förstå kraven för application innan du implementerar en strategi för cachelagring av programmet skikt.

## <a name="next-steps"></a>Nästa steg
* Mer information om DTU-baserade tjänstnivåer finns [DTU-baserade inköpsmodell](sql-database-service-tiers-dtu.md).
* Läs mer om vCore-baserade tjänstnivåer [vCore-baserade inköpsmodell (förhandsgranskning)](sql-database-service-tiers-vcore.md).
* Läs mer om elastiska pooler [vad är en Azure elastisk pool?](sql-database-elastic-pool.md)
* Information om prestanda och elastiska pooler finns [när du ska överväga en elastisk pool](sql-database-elastic-pool-guidance.md)

