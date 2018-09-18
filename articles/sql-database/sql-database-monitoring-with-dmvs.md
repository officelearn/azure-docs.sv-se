---
title: Övervaka Azure SQL Database med dynamiska hanteringsvyer | Microsoft Docs
description: Lär dig att identifiera och diagnostisera vanliga prestandaproblem med dynamiska hanteringsvyer för att övervaka Microsoft Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: carlrab
ms.openlocfilehash: 8750670f2acc41cd712254ba11b4d2ec20aa58aa
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981855"
---
# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Övervaka Azure SQL Database med dynamiska hanteringsvyer
Microsoft Azure SQL Database gör det möjligt för en delmängd av dynamiska hanteringsvyer att diagnostisera problem med prestanda, vilket kan orsakas av blockerade eller långvariga frågor, flaskhalsar för resurser, dålig frågeplaner och så vidare. Det här avsnittet innehåller information om hur du identifierar vanliga prestandaproblem med dynamiska hanteringsvyer.

SQL Database stöder delvis tre kategorier av dynamiska hanteringsvyer:

* Databasrelaterade dynamiska hanteringsvyer.
* Körning-relaterade dynamiska hanteringsvyer.
* Transaktionen att göra dynamiska hanteringsvyer.

Detaljerad information om dynamiska hanteringsvyer finns [Dynamic Management Views och Functions (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) i SQL Server Books Online.

## <a name="permissions"></a>Behörigheter
I SQL-databas, frågar en dynamisk hanteringsvy kräver **visa DATABASTILLSTÅND** behörigheter. Den **visa DATABASTILLSTÅND** behörighet returnerar information om alla objekt i den aktuella databasen.
Att bevilja de **visa DATABASTILLSTÅND** behörighet till en viss databasanvändare, kör följande fråga:

```GRANT VIEW DATABASE STATE TO database_user; ```

I en instans av en lokal SQL Server returnera dynamiska hanteringsvyer server statusinformation. Returnerar information om din aktuella logiska databasen endast i SQL-databas.

## <a name="calculating-database-size"></a>Beräkning av databasens storlek
Följande fråga returnerar storleken på din databas (i megabyte):

```
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

Följande fråga returnerar storleken på enskilda objekt (i megabyte) i databasen:

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Övervakar anslutningar
Du kan använda den [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) vy för att hämta information om anslutningar till en specifik Azure SQL Database-server och information om varje anslutning. Dessutom kan den [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) vyn är användbar vid hämtning av information om alla aktiva användaranslutningar och interna aktiviteter.
Följande fråga hämtar information om den aktuella anslutningen:

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> När du kör den **sys.dm_exec_requests** och **sys.dm_exec_sessions vyer**, om du har **visa DATABASTILLSTÅND** behörighet på databasen, visas alla körning sessioner på databasen. Annars ser du bara den aktuella sessionen.
> 
> 

## <a name="monitor-resource-use"></a>Övervaka Resursanvändning

Du kan övervaka resource användning med [SQL Database Query Performance Insight](sql-database-query-performance.md) och [Query Store](https://msdn.microsoft.com/library/dn817826.aspx).

Du kan också övervaka användning med hjälp av dessa två vyer:

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
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

### <a name="sysresourcestats"></a>sys.resource_stats
Den [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) visa i den **master** databasen finns ytterligare information som hjälper dig att övervaka prestanda för din SQL-databas på dess specifika tjänstnivå och beräkna storleken. Data samlas in var femte minut och bibehålls för ungefär 14 dagar. Den här vyn är användbar för en mer långsiktiga historisk analys av hur din SQL-databas använder resurser.

I följande diagram visar CPU Resursanvändning för en Premium-databas med P2 beräkningsstorleken för varje timme i en vecka. Det här diagrammet startas på en måndag visar 5 arbetsdagar och sedan visas en helg när mycket mindre sker på programmet.

![SQL database-Resursanvändning](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Från data, den här databasen har en hög CPU-belastning på strax över 50 procent CPU-användning i förhållande till P2 compute storlek (kl tisdagen). Om Processorn är den dominerande faktorn i programprofilen resurs, kan du välja att P2 är rätt beräkningsstorleken för att garantera att arbetsbelastningen alltid får plats. Om du förväntar dig ett program att växa med tiden är det en bra idé att ha en buffert extraresursen så att programmet inte någonsin uppnår sin gräns för prestandanivå. Om du ökar beräkningsstorleken kan du undvika kunden visas fel som kan uppstå när en databas inte har tillräckligt med kraften att bearbeta begäranden effektivt, särskilt i miljöer för känslig. Ett exempel är en databas som har stöd för ett program som ritar webbsidor baserat på resultatet av databasanrop.

Andra programtyper kan tolka i samma diagram på olika sätt. Till exempel om ett program försöker bearbeta löneuppgifter data varje dag och har samma diagram, kan den här typen av ”batchjobb”-modellen göra bra vid en P1 beräkningsstorleken. P1-beräkningsstorleken har 100 dtu: er jämfört med 200 dtu: er på P2 compute storlek. P1-beräkningsstorleken innehåller halva prestanda för P2 olika storlek. Därför är 50 procent av CPU-användning i P2 lika med 100 procent CPU-användning i P1. Om programmet inte har tidsgränser kan det ingen roll om ett jobb tar två timmar eller 2,5 timmar att slutföra, om det går idag. Ett program i den här kategorin kan förmodligen använda en P1 beräkningsstorleken. Du kan dra nytta av det faktum att det är tidsperioder under dagen när Resursanvändning är lägre, så att alla ”big högsta” kan hamnar i någon av de fördjupningar försedda senare under dagen. P1-beräkningsstorleken kan vara bra för den typen av program och spara pengar, förutsatt att jobben kan slutföras på tid på dagen.

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
2. För att utvärdera hur väl beräkningsstorleken som passar din arbetsbelastning kan du behöva granska nedåt i varje aspekt av mätvärden för resurs: CPU, läsningar, skrivningar, antal arbetare och antalet sessioner. Här är en reviderad ställa frågor med **sys.resource_stats** att rapportera genomsnittliga och högsta värden för de här måtten för resursen:
   
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
3. Med den här informationen om de genomsnittliga och högsta värdena för varje Resursmått, kan du utvärdera hur väl din arbetsbelastning passar in i beräkningsstorleken som du har valt. Vanligtvis medelvärden från **sys.resource_stats** ger dig en god baslinje att använda mot målstorleken. Det bör vara primära mätning-minne. Du kanske använder Standard-tjänstnivå med S2 beräkningsstorleken för ett exempel. Medelvärdet använda procentandelar för processor- och i/o-läsningar och skrivningar är lägre än 40 procent, det genomsnittliga antalet arbetare är under 50 och det genomsnittliga antalet sessioner är under 200. Din arbetsbelastning kan passar beräkningsstorleken S1. Det är enkelt att se om din databas passar in i worker och sessionen gränserna. För att se om en databas passar in i en lägre beräkningsstorleken för CPU, läsningar och skrivningar division DTU-numret på nedre compute storlek efter DTU antalet beräkningsstorleken på din aktuella och sedan multiplicera resultatet med 100:
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    Resultatet är den relativa prestandaskillnaden mellan två compute storlekar i procent. Om din Resursanvändning inte överstiger den mängden, kan arbetsbelastningen passar i lägre beräkningsstorleken. Men behöver du titta på alla intervall för resursen Använd värden och procent avgör hur ofta din databas-arbetsbelastning skulle passa lägre beräkningsstorleken. Följande fråga visar anpassa procentandelen per resursdimension, baserat på tröskelvärdet på 40 procent som vi har beräknats i det här exemplet:
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Baserat på din tjänstenivå för databasen kan avgöra du om din arbetsbelastning passar in i lägre beräkningsstorleken. Om ditt mål för databas-arbetsbelastning är 99,9 procent av föregående fråga returnerar värden som är större än 99,9 procent för alla tre resource dimensioner, passar din arbetsbelastning som sannolikt in lägre beräkningsstorleken.
   
    Titta på Anpassa procentandelen får också inblick i om du ska flytta till nästa högre beräkningsstorleken att uppfylla dina mål. Userdb1 visar till exempel följande CPU-användning för den senaste veckan:
   
   | Genomsnittlig CPU-procent | Högsta CPU-procent |
   | --- | --- |
   | 24.5 |100.00 |
   
    Den genomsnittliga CPU handlar om en fjärdedel av gränsen på beräkningsstorleken, vilket skulle passar in i beräkningsstorleken för databasen. Men det högsta värdet visar att databasen når gränsen på beräkningsstorleken. Behöver du flytta till nästa högre beräkningsstorleken? Titta på hur många gånger din arbetsbelastning når 100 procent och jämför den med ditt mål för databas-arbetsbelastning.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Om den här frågan returnerar ett värde mindre än 99,9 procent av något av tre resursdimensioner bör du antingen flytta till nästa högre beräkningsstorleken eller använda programmet justering teknik för att minska belastningen på SQL-databasen.
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

## <a name="monitoring-query-performance"></a>Övervaka prestanda för frågor
Långsamt eller länge körning av frågor kan du använda betydande systemresurser. Det här avsnittet visar hur du använder dynamiska hanteringsvyer för att identifiera några prestandaproblem för vanliga frågor. En äldre, men fortfarande användbart referens för felsökning, är den [Felsöka prestandaproblem i SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) artikel på Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Hitta främsta frågor
I följande exempel returnerar information om de översta fem frågorna rangordnade med Genomsnittlig CPU-tid. Det här exemplet aggregerar frågor enligt deras fråge-hash, så att logiskt motsvarande frågor grupperas efter deras kumulativa resursförbrukningen.

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
    MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
    ((CASE statement_end_offset
        WHEN -1 THEN DATALENGTH(ST.text)
        ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
     FROM sys.dm_exec_query_stats AS QS
     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Övervaka blockerade frågor
Långsam eller långvariga frågor kan bidra till abnorm resursförbrukning och var en följd av blockerade frågor. Orsaken till att den blockerar kan vara dålig programdesign, felaktig frågeplaner, bristen på användbara index och så vidare. Du kan använda sys.dm_tran_locks vyn för att hämta information om den aktuella låsning aktiviteten i din Azure SQL Database. Exempelkod, se [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) i SQL Server Books Online.

### <a name="monitoring-query-plans"></a>Övervakningsprogram för fråga
En ineffektiv frågeplanen kan också minska CPU-förbrukning. I följande exempel används den [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) vyn för att avgöra vilken fråga använder mest kumulativa Processorn.

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Se också
[Introduktion till SQL Database](sql-database-technical-overview.md)

