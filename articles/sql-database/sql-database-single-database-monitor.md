---
title: Övervaka databasprestanda i Azure SQL Database | Microsoft Docs
description: Läs mer om alternativ för att övervaka din databas med Azure-verktyg och dynamiska hanteringsvyer.
keywords: databasövervakning, molndatabasprestanda
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: carlrab
ms.openlocfilehash: dc04a9334b63656719a7633a8dd7154ed6cd6993
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092587"
---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Övervaka databasprestanda i Azure SQL Database
Prestandaövervakning för en SQL-databas i Azure startar med att övervaka resursutnyttjandet i förhållande till nivån på databasprestanda som du valt. Övervakning hjälper dig att avgöra om din databas har överflödig kapacitet eller har problem med eftersom resurserna är överutnyttjade ut och sedan bestämmer om det är dags att justera prestandanivån och tjänstnivåer för din databas i den [DTU-baserade inköpsmodell](sql-database-service-tiers-dtu.md) eller [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md). Du kan övervaka din databas med grafiska verktyg i [Azure-portalen](https://portal.azure.com) eller med SQL [dynamiska hanteringsvyer](https://msdn.microsoft.com/library/ms188754.aspx).

> [!TIP]
> Använd [smarta insikter för Azure SQL](sql-database-intelligent-insights.md) för automatisk övervakning av databasens prestanda. När ett prestandaproblem har identifierats genereras en diagnostiklogg med information om och rot orsak Analysis (RCA) av problemet. Rekommendation för förbättring av prestanda tillhandahålls när det är möjligt.
>

## <a name="monitor-databases-using-the-azure-portal"></a>Övervaka databaser med Azure-portalen
I [Azure-portalen](https://portal.azure.com/), kan du övervaka en enskild databas resursutnyttjning genom att välja din databas och klicka på **Övervaknings**-diagrammet. Det öppnar **Mått**-fönstret, vilket du kan ändra genom att klicka på **Redigera diagram**-knappen. Lägg till följande mått:

* CPU-procent
* DTU-procent
* Data IO-procent
* Databasstorlek i procent

När du har lagt till de här måtten, kan du fortsätta att visa dem i den **övervakning** diagram med mer information på den **mått** fönster. Alla fyra mätvärdena visar ett snittvärde för utnyttjandeprocent i förhållande till din databas **DTU:er**. Se den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) artiklar för mer information om tjänstnivåer.  

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

Du kan övervaka resource användning med [SQL Database Query Performance Insight](sql-database-query-performance.md) och [Query Store](https://msdn.microsoft.com/library/dn817826.aspx).

Du kan också övervaka användning med hjälp av dessa två vyer:

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

#### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
Du kan använda den [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) vyn i varje SQL-databas. Den **sys.dm_db_resource_stats** vyn visar de senaste användning resursdata i förhållande till tjänstnivån. Genomsnittlig procent för processor, data-i/o, skrivs loggen och minne registreras var 15: e sekund och bevaras i timmen.

Eftersom den här vyn ger en mer detaljerad titt på Resursanvändning, använda **sys.dm_db_resource_stats** första för alla aktuella tillstånd analys eller felsökning. Den här frågan visar till exempel den genomsnittliga och högsta resursanvändningen för den aktuella databasen under den senaste timman:

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data IO in percent',
        MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Andra frågor finns i exemplen i [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

#### <a name="sysresourcestats"></a>sys.resource_stats
Den [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) visa i den **master** databasen finns ytterligare information som hjälper dig att övervaka prestanda för din SQL-databas på dess specifika tjänstnivå och prestandanivå servicenivå. Data samlas in var femte minut och bibehålls för ungefär 14 dagar. Den här vyn är användbar för en mer långsiktiga historisk analys av hur din SQL-databas använder resurser.

I följande diagram visar CPU Resursanvändning för en Premium-databas med prestandanivå P2 för varje timme i en vecka. Det här diagrammet startas på en måndag visar 5 arbetsdagar och sedan visas en helg när mycket mindre sker på programmet.

![SQL database-Resursanvändning](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Från data, den här databasen har en högsta CPU-belastningen på strax över 50 procent CPU-användning i förhållande till prestandanivå P2 (kl tisdagen). Om Processorn är den dominerande faktorn i programprofilen resurs, kan du välja att P2 är rätt prestandanivån till garanterar att det alltid passar arbetsbelastningen. Om du förväntar dig ett program att växa med tiden är det en bra idé att ha en buffert extraresursen så att programmet inte någonsin uppnår sin gräns för prestandanivå. Om du ökar prestandanivån du undvika kunden visas fel som kan uppstå när en databas inte har tillräckligt med kraften att bearbeta begäranden effektivt, särskilt i miljöer för känslig. Ett exempel är en databas som har stöd för ett program som ritar webbsidor baserat på resultatet av databasanrop.

Andra programtyper kan tolka i samma diagram på olika sätt. Till exempel om ett program försöker bearbeta löneuppgifter data varje dag och har samma diagram, kan den här typen av ”batchjobb”-modellen göra bra vid prestandanivå P1. Prestandanivå P1 har 100 dtu: er jämfört med 200 dtu: er på prestandanivå P2. Prestandanivå P1 ger halva prestanda för prestandanivå P2. Därför är 50 procent av CPU-användning i P2 lika med 100 procent CPU-användning i P1. Om programmet inte har tidsgränser kan det ingen roll om ett jobb tar två timmar eller 2,5 timmar att slutföra, om det går idag. Ett program i den här kategorin kan förmodligen använda prestandanivå P1. Du kan dra nytta av det faktum att det är tidsperioder under dagen när Resursanvändning är lägre, så att alla ”big högsta” kan hamnar i någon av de fördjupningar försedda senare under dagen. Prestandanivå P1 kan vara bra för den typen av program och spara pengar, förutsatt att jobben kan slutföras på tid på dagen.

Azure SQL Database visar förbrukas resursinformation för varje aktiv databas i den **sys.resource_stats** vy av den **master** databasen i varje server. Data i tabellen sammanställs för 5 minuters intervall. Data kan ta mer än 5 minuter innan den visas i tabellen, så att dessa data är mer användbar för historisk analys i stället för analys i nära realtid med tjänstnivåer Basic, Standard och Premium. Fråga den **sys.resource_stats** visas för att se den senaste historiken för en databas och validera om reservationen du valde levereras önskad prestanda när det behövs.

> [!NOTE]
> Du måste vara ansluten till den **master** databasen för din logiska SQL database-server till frågan **sys.resource_stats** i följande exempel.
> 
> 

Det här exemplet visar hur data i den här vyn visas:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Katalogvy sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

I nästa exempel visar olika sätt som du kan använda den **sys.resource_stats** katalogvy att hämta information om hur SQL-databasen använder resurser:

1. Att titta på den senaste veckan resource använder för databas-userdb1 kan du köra den här frågan:
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. För att utvärdera hur väl prestandanivå som passar din arbetsbelastning kan du behöva granska nedåt i varje aspekt av mätvärden för resurs: CPU, läsningar, skrivningar, antal arbetare och antalet sessioner. Här är en reviderad ställa frågor med **sys.resource_stats** att rapportera genomsnittliga och högsta värden för de här måtten för resursen:
   
        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
3. Med den här informationen om de genomsnittliga och högsta värdena för varje Resursmått, kan du utvärdera hur väl din arbetsbelastning passar in i den prestandanivå som du har valt. Vanligtvis medelvärden från **sys.resource_stats** ger dig en god baslinje att använda mot målstorleken. Det bör vara primära mätning-minne. Exempelvis kanske du använder Standard-tjänstnivå med S2 prestandanivå. Medelvärdet använda procentandelar för processor- och i/o-läsningar och skrivningar är lägre än 40 procent, det genomsnittliga antalet arbetare är under 50 och det genomsnittliga antalet sessioner är under 200. Din arbetsbelastning kan passar in i S1-prestandanivå. Det är enkelt att se om din databas passar in i worker och sessionen gränserna. Om du vill se om en databas passar in i en lägre prestandanivå för processor, läser och skrivningar, delar DTU antalet lägre prestandanivå av DTU antalet din aktuella prestandanivå och sedan multiplicera resultatet med 100:
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    Resultatet är den relativa prestandaskillnaden mellan två prestandanivåer i procent. Om din Resursanvändning inte överstiger den mängden, kan arbetsbelastningen passar i lägre prestandanivå. Men behöver du titta på alla intervall för resursen Använd värden och procent avgör hur ofta din databas-arbetsbelastning skulle passa lägre prestandanivå. Följande fråga visar anpassa procentandelen per resursdimension, baserat på tröskelvärdet på 40 procent som vi har beräknats i det här exemplet:
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Baserat på din databas servicenivåmål (SLO) kan bestämma du om din arbetsbelastning passar in i lägre prestandanivå. Om din databas-arbetsbelastning Servicenivåmål är 99,9% och den föregående frågan returnerar värden som är större än 99,9 procent för alla tre resource dimensioner, passar din arbetsbelastning som sannolikt in lägre prestandanivå.
   
    Titta på Anpassa procentandelen får också inblick i om du ska flytta till nästa högre prestandanivå som uppfyller dina Servicenivåmål. Userdb1 visar till exempel följande CPU-användning för den senaste veckan:
   
   | Genomsnittlig CPU-procent | Högsta CPU-procent |
   | --- | --- |
   | 24.5 |100.00 |
   
    Den genomsnittliga CPU handlar om en fjärdedel av gränsen på den prestandanivå som skulle passar in i prestandanivå för databasen. Men det högsta värdet visar att databasen når gränsen på prestandanivån. Behöver du flytta till nästa högre prestandanivå? Titta på hur många gånger din arbetsbelastning når 100 procent och jämför den med din databas-arbetsbelastning Servicenivåmål.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Om den här frågan returnerar ett värde mindre än 99,9 procent av något av tre resursdimensioner bör du antingen flytta till nästa högre prestandanivå eller använda programmet justering teknik för att minska belastningen på SQL-databasen.
4. Den här övningen tar också hänsyn planerade arbetsbelastning-ökning i framtiden.

För elastiska pooler kan du övervaka individuella databaser i poolen med de tekniker som beskrivs i det här avsnittet. Men du kan också övervaka poolen som helhet. Mer information finns i [Övervaka och hantera en elastisk pool](sql-database-elastic-pool-manage-portal.md).


### <a name="maximum-concurrent-requests"></a>Högsta antal samtidiga begäranden
Om du vill se hur många samtidiga begäranden, kör du den här Transact-SQL-frågan på din SQL-databas:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Ändra den här frågan att filtrera på en viss databas som du vill analysera för att analysera arbetsbelastningen på en lokal SQL Server-databas. Till exempel om du har en lokal databas med namnet MyDatabase kan returnerar den här Transact-SQL-frågan antalet samtidiga begäranden i databasen:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Detta är bara en ögonblicksbild på ett ställe i tid. För att få en bättre förståelse för din arbetsbelastning och samtidig begäran krav, måste du samla in många exempel över tid.

### <a name="maximum-concurrent-logins"></a>Maximal samtidiga inloggningar
Du kan analysera dina användar- och mönster för att få en uppfattning om frekvensen för inloggningar. Du kan också köra verkliga belastningar i en testmiljö för att se till att du inte får det här eller andra begränsningar som beskrivs i den här artikeln. Det finns inte en enskild fråga eller dynamisk hanteringsvy (DMV) som kan visa samtidiga inloggningen räknar eller historik.

Om flera klienter använder samma anslutningssträng, autentiserar tjänsten varje inloggning. Om 10 användare samtidigt ansluta till en databas med samma användarnamn och lösenord, skulle det vara 10 samtidiga inloggningar. Den här gränsen gäller enbart för varaktigheten för inloggning och autentisering. Om samma 10 användare ansluta till databasen sekventiellt, blir antalet samtidiga inloggningar aldrig större än 1.

> [!NOTE]
> Den här begränsningen gäller för närvarande inte för databaser i elastiska pooler.
> 
> 

### <a name="maximum-sessions"></a>Maximalt antal sessioner
Om du vill se antalet aktuella aktiva sessioner, kör du den här Transact-SQL-frågan på din SQL-databas:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Om du analyserar en lokal SQL Server-arbetsbelastning kan du ändra frågan för att fokusera på en viss databas. Den här frågan hjälper dig att avgöra en session behov för databasen om du ska flytta den till Azure SQL Database.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

De här frågorna returnerar igen, point-in-time-värdet. Om du samlar in flera insamlingar över tid, får du bäst förståelse för din session använder.

För SQL Database-analys, kan du få historiska statistik på sessioner genom att fråga den [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) vy och granska de **active_session_count** kolumn. 

## <a name="next-steps"></a>Nästa steg

- Automatiskt finjustera databasindex och fråga med hjälp av körningsplaner [automatisk justering i Azure SQL Database](sql-database-automatic-tuning.md).
- Övervaka databasprestanda automatiskt med [smarta insikter för Azure SQL](sql-database-intelligent-insights.md). Den här funktionen innehåller diagnostikinformation och rotorsak analys av prestandaproblem.
