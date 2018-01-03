---
title: "Övervaka databasprestanda i Azure SQL Database | Microsoft Docs"
description: "Läs mer om alternativ för att övervaka din databas med Azure-verktyg och dynamiska hanteringsvyer."
keywords: "databasövervakning, molndatabasprestanda"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: a2e47475-c955-4a8d-a65c-cbef9a6d9b9f
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 09/20/2017
ms.author: carlrab
ms.openlocfilehash: 8513ace2589056387d8a1959c5727ee6bd5674cd
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Övervaka databasprestanda i Azure SQL Database
Prestandaövervakning för en SQL-databas i Azure startar med att övervaka resursutnyttjandet i förhållande till nivån på databasprestanda som du valt. Övervakning hjälper dig att avgöra om din databas har överflödig kapacitet eller har problem på grund av att resurserna är överutnyttjade och därefter bestämma när det är dags att justera prestandanivån och [tjänstnivån](sql-database-service-tiers.md) för databasen. Du kan övervaka din databas med grafiska verktyg i [Azure-portalen](https://portal.azure.com) eller med SQL [dynamiska hanteringsvyer](https://msdn.microsoft.com/library/ms188754.aspx).

> [!TIP]
> Använd [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) för automatisk övervakning av databasens prestanda. När ett prestandaproblem har identifierats genereras en diagnostiska logg med information och rot-orsaken analys (RCA) till problemet. Prestanda förbättras rekommendation tillhandahålls när det är möjligt.
>

## <a name="monitor-databases-using-the-azure-portal"></a>Övervaka databaser med Azure-portalen
I [Azure-portalen](https://portal.azure.com/), kan du övervaka en enskild databas resursutnyttjning genom att välja din databas och klicka på **Övervaknings**-diagrammet. Det öppnar **Mått**-fönstret, vilket du kan ändra genom att klicka på **Redigera diagram**-knappen. Lägg till följande mått:

* CPU-procent
* DTU-procent
* Data IO-procent
* Databasstorlek i procent

När du väl lagt till de här måtten, kan du fortsätta att se dem i **Övervaknings**-diagrammet med mer information i **Mått**-fönstret. Alla fyra mätvärdena visar ett snittvärde för utnyttjandeprocent i förhållande till din databas **DTU:er**. Se artikeln [tjänstnivåer](sql-database-service-tiers.md) för mer information om DTU:er.

![Tjänstnivå-övervakning av databasprestanda.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Du kan också konfigurera aviseringar på prestandamåtten. Klicka på knappen **Lägg till avisering** i **mått**-fönstret. Följ guiden för att konfigurera aviseringen. Du har möjlighet att avisera om måttet överskrider ett visst tröskelvärde eller om måttet faller under ett visst tröskelvärde.

Om du exempelvis förväntar dig att arbetsbelastningen på din databas kommer att öka, kan du välja att konfigurera en e-postavisering när din databas kommer upp i 80 % för något av prestandamåtten. Du kan använda det här som en tidig varningssignal för att veta när du kan behöva byta till en högre prestandanivå.

Prestandamåtten kan också hjälpa dig att bestämma om kan nedgradera till en lägre prestandanivå. Anta att du använder en Standard S2-databas och alla prestandamått visar att databasen i snitt inte använder mer än 10 % vid något tillfälle. Det är då troligt att databasen skulle fungera bra i Standard S1. Var dock medveten om arbetsbelastningar som varierar kraftigt innan du beslutar att flytta till en lägre prestandanivå.

## <a name="monitor-databases-using-dmvs"></a>Övervaka databaser med hjälp av DMV:er
Samma mått som exponeras i portalen, går också att få fram i systemvyer: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) i den logiska **huvud**-databasen på din server, samt [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) i användardatabasen. Använd **sys.resource_stats** om du behöver övervaka mindre detaljerad data över en längre tidsperiod. Använd **sys.dm_db_resource_stats** om du behöver övervaka mer detaljerad data inom ett kortare tidsintervall. Mer information finns i [Azure SQL Database-prestandaråd](sql-database-single-database-monitor.md#monitor-resource-use).

> [!NOTE]
> **sys.dm_db_resource_stats** returnerar en tom resultatuppsättning när den används i databaser av Webb- och Business-utgåva som är föråldrade.
>
>

### <a name="monitor-resource-use"></a>Övervaka Resursanvändning

Du kan övervaka resurs användning med [SQL Database Query Performance Insight](sql-database-query-performance.md) och [Frågearkivet](https://msdn.microsoft.com/library/dn817826.aspx).

Du kan också övervaka användningen med hjälp av dessa två vyer:

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

#### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
Du kan använda den [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) vyn i varje SQL-databas. Den **sys.dm_db_resource_stats** vyn visar de senaste Använd resursdata i förhållande till tjänstnivån. Genomsnittlig procent för processor, data i/o, skrivs loggen och minne registreras var 15: e sekund och bevaras i timmen.

Eftersom den här vyn innehåller en mer detaljerad titt på Resursanvändning, använda **sys.dm_db_resource_stats** första för alla aktuella tillstånd analys eller felsökning. Den här frågan visar till exempel genomsnittliga och högsta resursanvändningen för den aktuella databasen under den senaste timmen:

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data I/O in percent',
        MAX(avg_data_io_percent) AS 'Maximum data I/O in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Andra frågor finns i exemplen i [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

#### <a name="sysresourcestats"></a>sys.resource_stats
Den [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) visa i den **master** databasen innehåller ytterligare information som kan hjälpa dig att övervaka prestanda för SQL-databasen på den specifika tjänstnivå och prestandanivå nivån. Data samlas in var femte minut och underhålls i cirka 35 dagar. Den här vyn är användbar för en långsiktiga historiska analys av hur SQL-databasen använder resurser.

Följande diagram visar CPU resursanvändningen för en Premium-databas med P2 prestandanivå för varje timme under en vecka. Det här diagrammet startar på en måndag, visar 5 arbetsdagar och sedan visas en helger, när mycket mindre sker på programmet.

![Resursanvändning för SQL-databas](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Från data, har den här databasen för närvarande en CPU-belastning bara över 50% CPU-användning i förhållande till P2 prestandanivå (mitt på dagen tisdagen). Om Processorn är företag faktor i programprofilen resurs, kan du välja att P2 är rätt prestandanivån uppdateras till att garantera att arbetsbelastningen alltid får plats. Om du förväntar dig ett program kan växa med tiden är det bra att ha en buffert som extra resurs så att programmet inte någonsin nå prestandanivå gränsen. Om du ökar prestanda, kan du hjälpa att undvika kunden visas fel som kan uppstå när en databas inte har tillräckligt med ström för att bearbeta begäranden effektivt, särskilt i miljöer med känslig för fördröjningar. Ett exempel är en databas som har stöd för ett program som återger webbsidor baserat på resultatet av databasanrop.

Andra programtyper av kan tolka samma diagram på olika sätt. Till exempel om ett program försöker bearbeta löneuppgifter data varje dag och har samma diagram, kan den här typen av ”batchjobb” modell göra att vid prestandanivå P1. Prestandanivå P1 har 100 Dtu jämfört med 200 dtu: er på nivån P2 prestanda. Prestandanivå P1 ger halva prestanda för prestandanivåerna P2. Därför är 50 procent av CPU-användning i P2 lika med 100 procent CPU-användning i P1. Om programmet inte har tidsgränser kan det ingen roll om ett jobb tar 2 timmar eller 2,5 timmar att slutföra, om den hämtar gjort idag. Ett program i den här kategorin kan förmodligen använda prestandanivå P1. Du kan dra nytta av det faktum att tidsperioder under dagen när Resursanvändning är lägre, så att alla ”stor belastning” kan hamnar i en av tvättställ senare under dagen. Prestandanivå P1 kan vara bra för den typen av program, och spara pengar, förutsatt att jobben kan avslutas i tid varje dag.

Azure SQL Database visar förbrukas resursinformation för varje aktiv databas i den **sys.resource_stats** vy av den **master** databasen i varje server. Data i tabellen sammanställs för 5 minuters mellanrum. Data kan ta mer än 5 minuter att visas i tabellen, så att dessa data är mer användbar för historisk analys i stället för nära realtid analys med tjänstnivåerna Basic, Standard och Premium. Frågan i **sys.resource_stats** visa om du vill visa den senaste historiken för en databas och för att verifiera om reservationen du valde levereras önskad prestanda när det behövs.

> [!NOTE]
> Du måste vara ansluten till den **master** databasen för din logiska SQL database-server till frågan **sys.resource_stats** i följande exempel.
> 
> 

Det här exemplet visar hur data i den här vyn visas:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Katalogvyn sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

I nästa exempel visas olika sätt som du kan använda den **sys.resource_stats** vyn för att hämta information om hur SQL-databasen använder resurser katalog:

1. Titta på den senaste veckan resurs för databasen userdb1, kan du köra den här frågan:
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. För att utvärdera hur väl som passar din arbetsbelastning prestandanivå du behöver detaljnivån i varje aspekt av resursen mått: processor, läsningar, skrivningar, antalet arbetare och antalet sessioner. Här är en reviderade fråga med **sys.resource_stats** att rapportera genomsnittliga och högsta värden för de här måtten för resursen:
   
        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data I/O use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data I/O use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
3. Med den här informationen om de genomsnittliga och högsta värdena för varje resurs mått bedöma du hur bra din arbetsbelastning passar in i prestandanivå du valt. Vanligtvis medelvärden från **sys.resource_stats** ger dig en bra baslinje att använda mot målstorleken. Det bör vara primär mätning-minne. Exempelvis kan du använda Standard-tjänstnivå med S2 prestandanivå. Medelvärdet använder procent för processor- och i/o-läsningar och skrivningar är lägre än 40 procent, det genomsnittliga antalet anställda understiger 50 och det genomsnittliga antalet sessioner är under 200. Din arbetsbelastning kan passar in i S1-prestandanivå. Det är enkelt att se om databasen passar in i worker och session-gränser. Om du vill se om en databas som passar in i en lägre prestandanivå med avseende på CPU, läser och skrivningar, dividera antalet lägre prestandanivå med din nuvarande prestandanivå DTU antalet DTU och multiplicera resultatet med 100:
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    Resultatet är den relativa prestandaskillnaden mellan två prestandanivåer i procent. Om din Resursanvändning inte överskrider den här mängden, kanske passar din arbetsbelastning i lägre prestandanivå. Men behöver du titta på alla intervall med värden för användning av resursen och bestämma i procent hur ofta arbetsbelastningen databasen passar in i lägre prestandanivå. Följande fråga matar ut anpassa procent per resursdimension, baserat på tröskelvärdet på 40 procent som vi har beräknats i det här exemplet:
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Baserat på din databas servicenivåmål (SLO), kan du bestämma om din arbetsbelastning passar in i lägre prestandanivå. Om din databas arbetsbelastning Servicenivåmål är 99,9 procent och den föregående frågan returnerar värden som är större än 99,9 procent för alla tre resursdimensioner, din arbetsbelastning som sannolikt passar in i lägre prestandanivå.
   
    Titta på Anpassa procentandelen ger dig även insikt om bör du flytta till nästa högre prestandanivå att uppfylla dina Servicenivåmål. Userdb1 visar till exempel följande CPU-användning under den gångna veckan:
   
   | Genomsnittlig CPU-procent | Högsta CPU-procent |
   | --- | --- |
   | 24.5 |100.00 |
   
    Genomsnittlig CPU handlar om ett kvartal av gränsen på nivån prestanda skulle passar in i databasen prestandanivå. Men det högsta värdet visar att databasen når gränsen för prestandanivå. Behöver du flyttar till nästa högre prestandanivå? Titta på hur många gånger din arbetsbelastning når 100 procent och jämför den med din databas arbetsbelastning Servicenivåmål.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data I/O fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Om den här frågan returnerar ett värde antingen flytta till nästa högre prestandanivå minst 99,9% för någon av tre resurs dimensionerna eller använda programmet mottagningsfönster metoder för att minska belastningen på SQL-databasen.
4. Den här övningen även tas hänsyn till dina planerade arbetsbelastning ökning i framtiden.

För elastiska pooler kan du övervaka individuella databaser i poolen med de tekniker som beskrivs i det här avsnittet. Men du kan också övervaka poolen som helhet. Mer information finns i [Övervaka och hantera en elastisk pool](sql-database-elastic-pool-manage-portal.md).


### <a name="maximum-concurrent-requests"></a>Högsta antal samtidiga begäranden
Om du vill se hur många samtidiga begäranden, kör du den här Transact-SQL-frågan på din SQL-databas:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Ändra frågan för att filtrera på en viss databas som du vill analysera för att analysera arbetsbelastningen på en lokal SQL Server-databas. Till exempel om du har en lokal databas med namnet mindatabas returnerar den här Transact-SQL-frågan antalet samtidiga begäranden i databasen:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Detta är bara en ögonblicksbild på ett ställe i tid. För att få en bättre förståelse för din arbetsbelastning och krav för antal samtidiga begäranden, behöver du samla in många prover över tid.

### <a name="maximum-concurrent-logins"></a>Maximal samtidiga inloggningar
Du kan analysera dina användar- och mönster för att få en uppfattning om frekvensen för inloggningar. Du kan också köra verkliga belastningar i en testmiljö och kontrollera att du inte träffa detta eller andra begränsningar diskuterar vi i den här artikeln. Det finns inte en enskild fråga eller dynamisk hanteringsvy (DMV) som kan du visa samtidiga inloggningen räknar eller tidigare.

Om flera klienter använder samma anslutningssträng, autentiserar tjänsten för varje inloggning. Om 10 användare samtidigt ansluta till en databas med samma användarnamn och lösenord, är det 10 samtidiga inloggningar. Den här gränsen gäller enbart för varaktighet för inloggning och autentisering. Om samma 10 användare ansluta till databasen sekventiellt är antal samtidiga inloggningar aldrig större än 1.

> [!NOTE]
> Den här gränsen gäller för närvarande inte för databaser i elastiska pooler.
> 
> 

### <a name="maximum-sessions"></a>Maximalt antal sessioner
Kör den här Transact-SQL-frågan om du vill se antalet aktuella aktiva sessioner på SQL-databasen:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Ändra frågan för att fokusera på en viss databas om du analyserar en lokal SQL Server-arbetsbelastning. Den här frågan hjälper dig att fastställa en session måste för databasen om du funderar på att flytta den till Azure SQL Database.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

De här frågorna returnera igen, en tidpunkt i antal. Om du samlar in flera insamlingar över tid, har du den bästa förståelsen för din session använder.

SQL Database-analys, du kan få historiska statistik på sessioner genom att fråga den [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) visa och granska den **active_session_count** kolumn. 

## <a name="next-steps"></a>Nästa steg

- Automatiskt finjustera-databasindex och fråga planer för körning med hjälp av [Azure SQL Database automatisk justering](sql-database-automatic-tuning.md).
- Övervaka databasprestanda automatiskt med hjälp av [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md). Den här funktionen innehåller diagnostikinformation och rotorsak analys av prestandaproblem.
