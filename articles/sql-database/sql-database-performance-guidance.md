---
title: Azure SQL Database prestandajusteringsvägledning | Microsoft Docs
description: Lär dig mer om hur du använder rekommendationer för att förbättra frågeprestanda för Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: carlrab
ms.openlocfilehash: 791fb4ba2f16a48441d489a888360912afa8fa11
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983233"
---
# <a name="tuning-performance-in-azure-sql-database"></a>Justera prestanda i Azure SQL Database

Azure SQL Database innehåller [rekommendationer](sql-database-advisor.md) att du kan använda för att förbättra databasens prestanda eller låta Azure SQL Database [automatiskt anpassas efter programmets](sql-database-automatic-tuning.md) och tillämpa ändringarna som förbättrar prestandan för din arbetsbelastning.

Du har inte några tillämpliga rekommendationer och du fortfarande har problem med prestanda, du kan använda följande metoder för att förbättra prestanda:
- Öka tjänstnivåerna i dina [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) eller din [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) att tillhandahålla mer resurser till din databas.
- Finjustera dina program och tillämpa några metodtips som kan förbättra prestanda. 
- Justera databasen genom att ändra index och frågor för att arbeta mer effektivt med data.

Det här är manuella metoder eftersom du måste bestämma mängden resurser som uppfyller dina behov. I annat fall skulle du behöva skriva om programmet eller databaskod och distribuera ändringarna.

## <a name="increasing-servicce-tier-of-your-database"></a>Öka servicce nivå för din databas

Azure SQL Database erbjuder två inköpschef modeller, en [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) och en [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) som du kan välja bland. Varje tjänstnivå isolerar enbart de resurser som din SQL-databas kan använda och garanterar förutsägbara prestanda för den tjänstnivån. Vi erbjuder vägledning som hjälper dig att välja tjänstnivå för ditt program i den här artikeln. Vi diskuterar också olika sätt att du kan finjustera dina program att få ut mest från Azure SQL Database.

> [!NOTE]
> Den här artikeln fokuserar på prestanda hos enskilda databaser i Azure SQL Database. Prestandavägledning som rör elastiska pooler finns i [pris- och prestandaöverväganden för elastiska pooler](sql-database-elastic-pool-guidance.md). Observera dock att du kan använda flera av justeringsrekommendationer i den här artikeln för databaser i en elastisk pool och få liknande prestandafördelarna.
> 

* **Grundläggande**: Basic service-nivån erbjuder bra förutsägbara prestanda för varje databas, timme under timmen. I en Basic-databas stöder tillräckligt med resurser för bra prestanda för en liten databas som inte har flera samtidiga begäranden. Typiska användningsexempel när du skulle använda grundläggande tjänstnivå är:
  * **Du precis har kommit igång med Azure SQL Database**. Program som är under utveckling ofta inte behöver hög-beräkning storlekar. Grundläggande databaser är en perfekt miljö för utveckling eller testning till ett lågt pris.
  * **Du har en databas till en enda användare**. Program som associerar en användare med en databas normalt har inte höga krav för samtidighet och prestanda. Dessa program lämpar sig för Basic tjänstnivån.
* **Standard**: Standard tjänstnivå erbjuder bättre prestanda, förutsägbarhet och ger bra prestanda för databaser som har flera samtidiga förfrågningar, som arbetsgrupp och webbprogram. När du väljer en Standard-nivån-tjänstdatabasen du kan ändra storlek på dina databasprogram baserat på förutsägbar prestanda, minut under minut.
  * **Databasen har flera samtidiga begäranden**. Program som betjäna flera användare i taget vanligtvis måste högre storlekar. Exempelvis är arbetsgrupps- eller webbappar program som har låg till medelhög i/o-trafikkrav som stöder flera samtidiga frågor bra kandidater för Standard-tjänstnivå.
* **Premium**: The premiumnivån ger förutsägbar prestanda, andra över andra för varje Premium- eller affärskritisk databas. När du väljer Premium-tjänstnivån, du kan ändra storlek databasen programmet baserat på toppbelastningen för den här databasen. Planen tar du bort fall i vilka varians orsaka små frågor tar längre tid än förväntat i känslig åtgärder. Den här modellen förenklar utveckling och produkten verifiering cykler för program som kräver att stark uttalanden om resursbehov för högsta prestanda varians eller svarstid. De flesta Premium-nivån används serviceärenden har en eller flera av följande egenskaper:
  * **Hög belastning**. Ett program som kräver betydande CPU, minne eller indata/utdata (I/O) att slutföra dessa åtgärder kräver en dedikerad, hög-compute-storlek. En databasåtgärd kända för att använda flera processorkärnor en längre tid är till exempel en kandidat för Premium-tjänstnivån.
  * **Många samtidiga begäranden**. Vissa databasprogram tjänsten många samtidiga begäranden, till exempel när du hanterar en webbplats som har en hög belastning på volym. Basic och Standard tjänstnivåer begränsa antalet samtidiga begäranden per databas. Program som kräver fler anslutningar måste välja en lämplig reservationsstorlek för att hantera det maximala antalet nödvändiga begäranden.
  * **Låg latens**. Vissa program behöver att garantera ett svar från databasen på minimal tid. Om en specifik lagrad procedur anropas som en del av en bredare kund-åtgärd, kanske ett krav att ha en returnera från anropet i fler än 20 millisekunder, 99 procent av tiden. Den här typen av program dra nytta av Premium-tjänstnivån, kontrollera att nödvändiga databearbetningsfunktioner är tillgänglig.

Tjänstenivå som du behöver för din SQL-databas är beroende av högsta belastning krav för varje resursdimension. Vissa program använder en trivial del av en enskild resurs, men har betydande krav för andra resurser.

### <a name="service-tier-capabilities-and-limits"></a>Tjänstfunktioner och begränsningar

På varje tjänstnivå och ange beräkningsstorleken, så att du har flexibiliteten att betala bara för den kapacitet du behöver. Du kan [justera kapacitet](sql-database-service-tiers-dtu.md), upp eller ned, när arbetsbelastningen ändras. Om din databas-arbetsbelastning är hög under perioder som jul den tillbaka till skolan, kan du till exempel öka beräkningsstorleken för databasen för en viss tid, juli via September. Du kan minska det när din högsta säsongen avslutas. Du kan minimera det du betalar för genom att optimera din molnmiljö till säsongsvärdet för din verksamhet. Den här modellen fungerar bra för programvara utgivningscykler för produkten. Ett test-team kan allokera kapacitet medan den gör testa körningar och släpp den kapaciteten när de är klara testning. I ett prispaket av begäran betalar du för kapacitet du behöver det och undvika utgifterna på dedikerade resurser som du kan använda för sällan.

### <a name="why-service-tiers"></a>Varför tjänstnivåer?
Varje databas-arbetsbelastning kan variera, är syftet med tjänstnivåer att tillhandahålla förutsägbara prestanda i olika storlekar. Kunder med storskaliga databasen resurskrav kan arbeta i en mer dedikerade datormiljö.

## <a name="tune-your-application"></a>Finjustera dina program
I traditionella lokala SQL Server, är processen inledande kapacitetsplanering ofta avgränsade från processen att köra ett program i produktion. Maskin- och licenser som köps först och prestandajustering görs efteråt. När du använder Azure SQL Database är en bra idé att interweave processen med att köra en App och anpassar den. Med modellen för att betala för kapacitet vid behov kan finjustera du programmet så att de lägsta resurser som krävs nu, i stället för att skaffa stora resurser på maskinvara, utifrån gissningar för framtida tillväxt planer för ett program, vilket ofta är felaktiga. Vissa kunder kan välja att inte Finjustera ett program och i stället välja att därför överetablerar maskinvaruresurser. Den här metoden kan vara bra om du inte vill ändra ett viktiga program under en upptagen period. Men justering ett program kan minimera resurskraven och lägre månatliga fakturor när du använder tjänstnivåerna i Azure SQL Database.

### <a name="application-characteristics"></a>Programegenskaper
Även om Azure SQL Database-servicenivåerna är utformade för att förbättra stabiliteten för prestanda och förutsägbarhet för ett program, några rekommendationer kan hjälpa dig att finjustera programmets bättre dra nytta av resurser i en beräkning storlek. Även om många program har betydande prestandavinster genom att byta till ett högre beräkna storleken eller tjänstnivån, vissa program behöver ytterligare justering för att gynnas av en högre säkerhetsnivå för tjänsten. Överväg att ytterligare justering för program som har följande egenskaper för bättre prestanda:

* **Program som har långsam prestanda på grund av ”trafikintensiva” beteende**. Trafikintensiva program drar onödigt stora datamängder åtkomståtgärder som är känsliga för fördröjningar i nätverket. Du kan behöva ändra dessa typer av program för att minska antalet åtgärder för dataåtkomst till SQL-databasen. Du kan till exempel förbättra programmets prestanda med hjälp av teknik som batchbearbetning ad hoc-frågor eller flytta frågorna till lagrade procedurer. Mer information finns i [Batch-frågor](#batch-queries).
* **Databaser med en intensiv arbetsbelastning som inte stöds av en hel enkel dator**. Databaser som överskrider resurser på den högsta Premium compute storlek kan ha nytta av att skala ut arbetsbelastningen. Mer information finns i [databasöverskridande horisontell partitionering](#cross-database-sharding) och [funktionell partitionering](#functional-partitioning).
* **Program som har icke-optimal frågor**. Program, särskilt de som finns i dataåtkomstlagret som har dåligt anpassad frågor kan inte dra nytta av en högre beräkningsstorleken. Detta inkluderar frågor som saknar en WHERE-sats, saknar index eller vara inaktuell statistik. Dessa program dra nytta av standard-prestandajustering frågetekniker. Mer information finns i [saknas index](#identifying-and-adding-missing-indexes) och [fråga justering och bl a](#query-tuning-and-hinting).
* **Program som har icke-optimal data åt design**. Program som har inbyggd data samtidighet åtkomstproblem, till exempel deadlocking kan inte dra nytta av en högre beräkningsstorleken. Överväg att minska sändningar mot Azure SQL-databas av cachelagring på klientsidan med tjänsten Azure Caching eller en annan cachelagringsteknik. Se [programmet nivån cachelagring](#application-tier-caching).

## <a name="tune-your-database"></a>Finjustera din databas
I det här avsnittet ska titta vi på vissa tekniker som du kan använda för att finjustera Azure SQL Database för att få bästa möjliga prestanda för ditt program och köra den på den lägsta möjliga beräkningsstorleken. Vissa av dessa metoder matchar den traditionella SQL Server justering metodtips, men andra är specifika för Azure SQL Database. I vissa fall kan undersöka du förbrukade resurser för en databas att hitta områden för att ytterligare finjustera och utöka traditionella tekniker för SQL Server att fungera i Azure SQL Database.

### <a name="identify-performance-issues-using-azure-portal"></a>Identifiera prestandaproblem med hjälp av Azure portal
Följande verktyg i Azure-portalen kan hjälpa dig att analysera och korrigera prestandaproblem med SQL-databasen:

* [Query Performance Insight](sql-database-query-performance.md)
* [SQL Database Advisor](sql-database-advisor.md)

Azure portal finns mer information om båda dessa verktyg och hur de används. För att effektivt diagnostisera och åtgärda problem, rekommenderar vi att du försöker först använda verktygen i Azure-portalen. Vi rekommenderar att du använder den manuella justering metoder som vi går igenom sedan efter saknade index och frågejusteringar i specialfall.

Mer information om hur du identifierar problem i Azure SQL Database på [prestandaövervakning i Azure-portalen](sql-database-monitor-tune-overview.md) och [övervaka databaser med DMV: er](sql-database-monitoring-with-dmvs.md) artiklar.

### <a name="identifying-and-adding-missing-indexes"></a>Identifiera och lägga till index som saknas
Ett vanligt problem i OLTP databasprestanda relaterar till den fysiska databasen-design. Ofta databasscheman designas och levereras utan att testa i skala (antingen belastning eller datavolym). Prestanda för en frågeplan kan tyvärr vara godtagbar i liten skala men försämras avsevärt under produktionsnivån datavolymer. Den vanligaste orsaken till problemet är bristen på rätt index för att uppfylla filter eller andra begränsningar i en fråga. Saknas index manifest som en tabell skanna ofta när ett index Målsökning kunde räcker.

I det här exemplet använder den valda frågeplanen en genomsökning när en Målsökning skulle räcker:

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

Azure SQL Database kan hjälpa dig att hitta och åtgärda vanliga saknas index-villkor. DMV: er som är inbyggda i Azure SQL Database titta på frågekompileringar där skulle ett index avsevärt minska den uppskattade kostnaden för att köra en fråga. Under frågekörningskoden spårar SQL Database hur ofta varje frågeplan körs och spårar uppskattade mellanrummet mellan verkställande frågeplanen och det imagined där som indexerar fanns. Du kan använda dessa DMV: er för att snabbt gissa vilka ändringar av utformningen av den fysiska databasen kan förbättra den övergripande arbetsbelastningen kostnaden för en databas och dess faktiska arbetsbelastningen.

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

Frågan resulterade i den här förslag i det här exemplet:

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

När den har skapats, hämtar ett annat schema, som använder en Målsökning i stället för en genomsökning och utför planen mer effektivt att samma SELECT-instruktion:

![En frågeplan med korrigerad index](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

Viktig information är att i/o-kapaciteten för ett delat, vanlig system är mer begränsad än den som en dedikerad server-dator. Det finns en premium på minimerar onödig i/o för att dra maximal nytta av systemet i DTU för varje beräkningsstorleken för Azure SQL Database-tjänstnivåer. Lämplig fysiska databasdesign val kan avsevärt förbättra svarstiden för enskilda frågor, förbättra dataflödet för samtidiga begäranden som hanteras per skalningsenhet och minska kostnaderna som krävs för att uppfylla frågan. Mer information om indexet som saknas DMV: er finns i [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Frågejusteringar och bl a
Frågeoptimeringen i Azure SQL Database liknar traditionella SQL Server-Frågeoptimeringen. De flesta av de bästa metoderna för att anpassa frågor och förstå resonemang modellen begränsningar för Frågeoptimeringen gäller även för Azure SQL Database. Om du justera frågor i Azure SQL Database, kan du få den ytterligare fördelen av att minska behovet av sammanställda resursdata. Ditt program kan eventuellt för till en lägre kostnad än untuned motsvarande eftersom det kan köras på en lägre beräkningsstorleken.

Ett exempel som är vanliga i SQL Server och som gäller även för Azure SQL Database är hur Frågeoptimeringen ”lyssnar” parametrar. Under kompilering utvärderar Frågeoptimeringen det aktuella värdet för en parameter för att avgöra om det kan generera en mer optimala frågeplan. Även om den här strategin kan ofta leda till en frågeplan som är betydligt snabbare än en plan som kompileras utan kända parametervärden, fungerar för närvarande den imperfectly både i SQL Server och i Azure SQL Database. Parametern är ibland inte någon lyssnar, och ibland parametern någon lyssnar men genererade planen är optimal för den fullständiga uppsättningen parametervärden i en arbetsbelastning. Microsoft omfattar frågetips (direktiv) så att du kan ange avsikt mer avsiktligt och åsidosätta standardbeteendet för parametern kontroll. Om du använder tips kan åtgärda du ofta fall där standardbeteendet för SQL Server eller Azure SQL Database är perfekt för en viss kund-arbetsbelastning.

I nästa exempel visas hur frågeprocessorn kan generera en plan som är icke-optimal både för prestanda- och resursbehov. Det här exemplet visar också att du kan minska Frågekörningen och krav för SQL-databasen om du använder en frågetipset:

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

Installationsprogrammet koden skapar en tabell som har förvrängd Datadistribution. Den optimala frågeplanen skiljer sig beroende på vilken parameter är markerad. Planen funktionssätt för cachelagring inte tyvärr alltid kompilera om frågan baserat på de vanligaste parametervärdet. Det är därför möjligt för en icke-optimal plan för att cachelagras och används för många värden, även om ett annat schema kan vara ett bättre alternativ för planen i genomsnitt. Frågeplanen skapas två lagrade procedurer som är identiska, förutom att en har en särskild frågetipset.

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

**Exempel, del 2**

(Vi rekommenderar att du väntar minst 10 minuter innan du börjar del 2 av det här exemplet så att resultatet är distinkt i den resulterande telemetridata.)

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Varje del av det här exemplet försöker att köra en parametriserade insert-instruktionen 1 000 gånger (om du vill generera en tillräcklig belastning ska användas som en datauppsättning för testning). När lagrade procedurer körs, undersöker frågeprocessorn parametervärdet som skickas till proceduren under sin första kompilering (parametern ”identifiering”). Processorn cachelagrar planen och använder den för senare anrop, även om parametervärdet är olika. En optimal plan kan inte användas i samtliga fall. Ibland behöver att vägleda optimering för att välja en plan som är bättre för genomsnittlig fallet i stället för specifika fallet från när frågan kompilerades först. I det här exemplet genererar den ursprungliga planen en ”genomsökning”-plan som läser alla rader om du vill hitta alla värden som matchar parametern:

![Fråga efter justering med hjälp av en plan för genomsökning](./media/sql-database-performance-guidance/query_tuning_1.png)

Eftersom vi körde proceduren med hjälp av värdet 1 i planen var optimala för värdet 1 men var icke-optimal för alla andra värden i tabellen. Resultatet förmodligen inte vad du vill ha om du skulle välja varje plan slumpmässigt, eftersom planen som utför långsammare och använder mer resurser.

Om du kör testet med `SET STATISTICS IO` inställd `ON`, logiska genomsökning arbetet i det här exemplet utförs i bakgrunden. Du kan se att det inte finns 1,148 läsningar som utförs av planen (vilket är ineffektiv, om den genomsnittliga är att returnera bara en rad):

![Fråga efter justering genom att använda en logisk sökning](./media/sql-database-performance-guidance/query_tuning_2.png)

Den andra delen av exemplet använder en frågetipset ska berätta för optimering att använda ett specifikt värde under kompilering. I så fall tvingas frågeprocessorn att ignorera det värde som skickas som parameter, och i stället att anta `UNKNOWN`. Detta refererar till ett värde som har genomsnittlig frekvens i tabellen (ignorera skeva). I planen är en plan med Målsökning som är snabbare och använder färre resurser i genomsnitt än planen i del 1 av det här exemplet:

![Frågejusteringar med hjälp av en frågetipset](./media/sql-database-performance-guidance/query_tuning_3.png)

Du kan se effekten i den **sys.resource_stats** tabell (det finns en fördröjning från det att du kör testet och när data fylls i tabell). För det här exemplet, del 1 körs under tidsperioden för 22:25:00 och del 2 som körs kl. 22:35:00. Tidigare tidsfönstret används mer resurser under den tidsperioden än det senare (på grund av plan prestationsförmågan).

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Exempel på resultat om frågejustering](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Även om volymen i det här exemplet är avsiktligt liten kan effekten av icke-optimal parametrar vara betydande, särskilt på större databaser. Skillnaden i extrema fall kan vara mellan sekunder för snabb fall och timmar för långsam fall.
> 
> 

Du kan undersöka **sys.resource_stats** att avgöra om resursen för ett prov använder fler eller färre resurser än en annan virtuell test. När du jämför data avgränsa tidtagningen av testerna så att de inte finns i samma 5 minuter fönster i den **sys.resource_stats** vy. Målet med den här övningen är att minimera den totala mängden resurser som används och inte för att minimera de högsta resurserna. I allmänhet kan minskar optimera en typ av kod för svarstid också resursförbrukning. Se till att de ändringar du gör i ett program är nödvändiga, och att ändringarna inte negativt påverkar användarupplevelsen i någon som använder frågetips i programmet.

Om en arbetsbelastning har en uppsättning frågor, är det ofta meningsfullt att avbilda och validera begränsningar av valen plan eftersom den styr den minsta resource storleksenheten som krävs för att vara värd för databasen. När du har validerat ibland på nytt granska planer för att hjälpa dig att kontrollera att de inte har nedgraderats. Du kan läsa mer om [fråga tips (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Horisontell partitionering över flera databaser
Eftersom Azure SQL Database körs på vanlig maskinvara, är kapacitetsbegränsningarna för en enskild databas lägre än för en traditionella lokala SQL Server-installation. Vissa kunder använda horisontell partitionering för att sprida databasåtgärder över flera databaser när åtgärderna inte ryms inom ramen för en enskild databas i Azure SQL Database. De flesta kunder som använder tekniker för horisontell partitionering i Azure SQL Database dela sina data på en enda dimension över flera databaser. För den här metoden måste du förstå att OLTP ofta programmens transaktioner som gäller för bara en rad eller till en liten grupp av rader i schemat.

> [!NOTE]
> SQL-databasen innehåller nu ett bibliotek som hjälper till med horisontell partitionering. Mer information finns i [översikt över Elastic Database-klientbibliotek](sql-database-elastic-database-client-library.md).
> 
> 

Till exempel om en databas har kundens namn, ordning och beställningsinformation (till exempel traditionella exemplet Northwind-databasen som levereras med SQL Server), du kan dela upp dessa data i flera databaser genom att gruppera en kund med relaterade ordning och ordning information information. Du kan garantera att kundens data förblir i en enda databas. Programmet kan dela upp olika kunder på databaser som effektivt kan sprida belastningen över flera databaser. Med horisontell partitionering, kunder inte bara kan undvika den maximala storleksgränsen, men Azure SQL Database kan också bearbeta arbetsbelastningar som är betydligt större än gränserna för de olika instansstorlekarna, förutsatt att varje enskild databas passar in i dess DTU.

Database sharding inte minska sammanställda resursdata kapaciteten för en lösning, är det mycket effektiva för att stödja mycket stora lösningar som är fördelade över flera databaser. Varje databas kan köra på en annan beräkningsstorleken för mycket stora ”effektiva” databaser med höga resurskrav.

### <a name="functional-partitioning"></a>Funktionell partitionering
SQL Server-användare kombinera ofta många funktioner i en enskild databas. Till exempel om ett program har logik för att hantera lager för en butik, kanske den databasen logiken som är associerad med inventeringssamling inköpsorder, lagrade procedurer och indexerade eller materialiserade vyer som hanterar rapportering i slutet av månaden. Den här tekniken blir det lättare att administrera databasen för åtgärder som säkerhetskopiering, men också måste du ändra storlek på maskinvara för att hantera den extra belastningen över alla funktioner i ett program.

Om du använder en skalbar arkitektur i Azure SQL Database är en bra idé att dela upp olika funktioner i ett program i olika databaser. Med den här tekniken kan varje program kan skalas oberoende av varandra. Som ett program blir ju mer upptagen och ökar belastningen på databasen, kan administratören välja oberoende storlekar för varje funktion i programmet. Ett program kan vara större än en enda vanlig dator kan hantera eftersom belastningen sprids över flera datorer på gränsen med den här arkitekturen.

### <a name="batch-queries"></a>Batch-frågor
För program som har åtkomst till data med hjälp av stora volymer, frekventa, ad hoc-frågor kan avsevärt lång svarstid kan hänföras nätverkskommunikation mellan programnivån och Azure SQL Database-nivå. Även om både program- och Azure SQL Database finns i samma datacenter, kan Nätverksfördröjningen mellan två förstoras med ett stort antal åtgärder för dataåtkomst. För att minska nätverkets round sätts för åtgärder för dataåtkomst, Överväg att använda alternativet att antingen batch ad hoc-frågor eller för att sammanställa dem som lagrade procedurer. Om du batch ad hoc-frågor kan du skicka flera frågor som en stor grupp i en enda resa till Azure SQL Database. Om du kompilera ad hoc-frågor i en lagrad procedur kan du uppnå samma resultat som om du batch-dem. Använda en lagrad procedur ger dig också att kunna utnyttja ökar risken för cachelagring frågeplaner i Azure SQL Database så att du kan använda den lagrade proceduren igen.

Vissa program är skrivningsintensiva. Ibland kan du minska den totala i/o-belastningen på en databas genom att fundera över hur du batch-skrivningar tillsammans. Ofta är det lika enkelt som att använda explicita transaktioner i stället för automatiskt genomförande transaktioner i lagrade procedurer och ad hoc-batchar. En utvärdering av olika metoder som du kan använda finns i [batchbearbetning tekniker för SQL Database-program i Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Experimentera med din egen arbetsbelastning för att hitta rätt modellen för batchbearbetning. Var noga med att förstå att en modell kan ha något annorlunda transaktionell konsekvensgarantier. Hitta rätt arbetsbelastningen som minimerar Resursanvändning kräver att hitta rätt kombination av konsekvens och prestanda med de.

### <a name="application-tier-caching"></a>Programnivå cachelagring
Vissa databasprogram har läs-tunga arbetsbelastningar. Cachelagring lager kan minska belastningen på databasen och kan minska beräkningsstorleken som krävs för att stödja en databas med hjälp av Azure SQL Database. Med [Azure Redis Cache](https://azure.microsoft.com/services/cache/), om du har en Läs-intensiv arbetsbelastning, kan du läsa data en gång (eller kanske en gång på programnivå dator, beroende på hur det är konfigurerat), och sedan lagras utanför din SQL-databas. Detta är ett sätt att minska databasbelastningen (processor och Läs i/o), men det finns en effekt på transaktionell konsekvens eftersom de data som läses från cachen kanske är inte synkroniserade med data i databasen. Även om vissa andelen inkonsekvens i många program är godkänd, som gäller inte för alla arbetsbelastningar. Läs noggrant igenom alla programkrav innan du implementerar en cahelagringsstrategi hittar programmet-nivå.

## <a name="next-steps"></a>Nästa steg
* Mer information om DTU-baserade tjänstnivåer finns i [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md).
* Mer information om vCore-baserade tjänstnivåer finns i [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).
* Mer information om elastiska pooler finns i [vad är en elastisk pool i Azure?](sql-database-elastic-pool.md)
* Information om prestanda och elastiska pooler finns i [när du ska tänka på en elastisk pool](sql-database-elastic-pool-guidance.md)

