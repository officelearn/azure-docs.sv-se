---
title: Skapa och hantera elastiska databasjobb med Transact-SQL (T-SQL)
description: Kör skript i många databaser med elastisk databasjobbagent med Transact-SQL (T-SQL).
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
ms.date: 02/07/2020
ms.openlocfilehash: 740a42dc94cdfa8d5c5a91b32b58cbff4c1bcda0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687772"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Använda Transact-SQL (T-SQL) för att skapa och hantera elastiska databasjobb

Den här artikeln innehåller många exempelscenarier för att komma igång med elastiska jobb med T-SQL.

Exemplen använder de [lagrade procedurer](#job-stored-procedures) och [vyer](#job-views) som är tillgängliga i [*jobbdatabasen*](sql-database-job-automation-overview.md#job-database).

Transact-SQL (T-SQL) används för att skapa, konfigurera, köra och hantera jobb. Det går inte att skapa den elastiska jobbagenten i T-SQL, så du måste först skapa en *elastisk jobbagent* med portalen eller [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).


## <a name="create-a-credential-for-job-execution"></a>Skapa en autentiseringstillstånd för jobbkörning

Autentiseringsuppgifterna används för att ansluta till dina måldatabaser för skriptkörning. Autentiseringsuppgifterna kräver lämpliga behörigheter, på de databaser som anges av målgruppen, för att kunna köra skriptet. När du använder en server- och/eller poolmålgruppsmedlem föreslås det starkt att du skapar en huvudautentiseringsidentiell för användning för att uppdatera autentiseringsuppgifterna före expansionen av servern och/eller poolen vid tidpunkten för jobbkörningen. Databasens begränsade autentiseringsuppgifter skapas i jobbagentdatabasen. Samma autentiseringsuppgifter måste användas för att *skapa en inloggning* och skapa en användare från inloggning för att bevilja *inloggningsdatabasbehörigheter* på måldatabaserna.


```sql
--Connect to the job database specified when creating the job agent

-- Create a db master key if one does not already exist, using your own password.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';  
  
-- Create a database scoped credential.  
CREATE DATABASE SCOPED CREDENTIAL myjobcred WITH IDENTITY = 'jobcred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO

-- Create a database scoped credential for the master database of server1.
CREATE DATABASE SCOPED CREDENTIAL mymastercred WITH IDENTITY = 'mastercred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO
```

## <a name="create-a-target-group-servers"></a>Skapa en målgrupp (servrar)

I följande exempel visas hur du kör ett jobb mot alla databaser på en server.  
Anslut till [*jobbdatabasen*](sql-database-job-automation-overview.md#job-database) och kör följande kommando:


```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net'

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```


## <a name="exclude-an-individual-database"></a>Utesluta en enskild databas

I följande exempel visas hur du kör ett jobb mot alla databaser i en SQL Database-server, förutom databasen *MappingDB*.  
Anslut till [*jobbdatabasen*](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name='server2.database.windows.net'
GO

--Exclude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name =N'MappingDB'
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```


## <a name="create-a-target-group-pools"></a>Skapa en målgrupp (pooler)

I följande exempel visas hur du inriktar alla databaser i en eller flera elastiska pooler.  
Anslut till [*jobbdatabasen*](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup'

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net',
@elastic_pool_name='ElasticPool-1'

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```


## <a name="deploy-new-schema-to-many-databases"></a>Distribuera nytt schema till många databaser

I följande exempel visas hur du distribuerar nytt schema till alla databaser.  
Anslut till [*jobbdatabasen*](sql-database-job-automation-overview.md#job-database) och kör följande kommando:


```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name='CreateTableTest', @description='Create Table Test'

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name='CreateTableTest',
@command=N'IF NOT EXISTS (SELECT * FROM sys.tables 
            WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name='myjobcred',
@target_group_name='PoolGroup'
```


## <a name="data-collection-using-built-in-parameters"></a>Datainsamling med hjälp av inbyggda parametrar

I många scenarier för datainsamling kan det vara användbart att inkludera några av dessa skriptvariabler för att hjälpa till att efterbehandla resultatet av jobbet.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Om du till exempel vill gruppera alla resultat från samma jobbkörning tillsammans använder du *$(job_execution_id)* som visas i följande kommando:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Övervaka databasprestanda

I följande exempel skapas ett nytt jobb för att samla in prestandadata från flera databaser.

Som standard skapar jobbagenten utdatatabellen för att lagra returnerade resultat. Därför måste databasens huvudnamn som är associerad med utdataautentiseringsautentiseringen minst `SELECT`ha `INSERT` `DELETE` följande behörigheter: `CREATE TABLE` i `ALTER`databasen, , , i utdatatabellen eller dess schema och `SELECT` i [katalogvyn sys.indexes.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql)

Om du vill skapa tabellen manuellt i förväg måste den ha följande egenskaper:
1. Kolumner med rätt namn och datatyper för resultatuppsättningen.
2. Ytterligare kolumn för internal_execution_id med datatypen uniqueidentifier.
3. Ett icke-grupperat `IX_<TableName>_Internal_Execution_ID` index som namnges i kolumnen internal_execution_id.
4. Alla behörigheter som anges `CREATE TABLE` ovan utom för behörighet i databasen.

Anslut till [*jobbdatabasen*](sql-database-job-automation-overview.md#job-database) och kör följande kommandon:

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsJob',
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name='myjobcred',
@target_group_name='PoolGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='<resultsdb>',
@output_table_name='<resutlstable>'
Create a job to monitor pool performance
--Connect to the job database specified when creating the job agent

-- Add a target group containing master database
EXEC jobs.sp_add_target_group 'MasterGroup'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name='MasterGroup',
@target_type='SqlDatabase',
@server_name='server1.database.windows.net',
@database_name='master'

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name='ResultsPoolsJob',
@description='Demo: Collection Performance data from all pools',
@schedule_interval_type='Minutes',
@schedule_interval_count=15

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsPoolsJob',
@command=N'declare @now datetime
DECLARE @startTime datetime
DECLARE @endTime datetime
DECLARE @poolLagMinutes datetime
DECLARE @poolStartTime datetime
DECLARE @poolEndTime datetime
SELECT @now = getutcdate ()
SELECT @startTime = dateadd(minute, -15, @now)
SELECT @endTime = @now
SELECT @poolStartTime = dateadd(minute, -30, @startTime)
SELECT @poolEndTime = dateadd(minute, -30, @endTime)

SELECT elastic_pool_name , end_time, elastic_pool_dtu_limit, avg_cpu_percent, avg_data_io_percent, avg_log_write_percent, max_worker_percent, max_session_percent,
        avg_storage_percent, elastic_pool_storage_limit_mb FROM sys.elastic_pool_resource_stats
        WHERE end_time > @poolStartTime and end_time <= @poolEndTime;
'),
@credential_name='myjobcred',
@target_group_name='MasterGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='resultsdb',
@output_table_name='resutlstable'
```


## <a name="view-job-definitions"></a>Visa jobbdefinitioner

I följande exempel visas hur du visar aktuella jobbdefinitioner.  
Anslut till [*jobbdatabasen*](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

-- View all jobs
SELECT * FROM jobs.jobs

-- View the steps of the current version of all jobs
SELECT js.* FROM jobs.jobsteps js
JOIN jobs.jobs j 
  ON j.job_id = js.job_id AND j.job_version = js.job_version

-- View the steps of all versions of all jobs
select * from jobs.jobsteps
```


## <a name="begin-ad-hoc-execution-of-a-job"></a>Påbörja ad hoc-körning av ett jobb

I följande exempel visas hur du startar ett jobb direkt.  
Anslut till [*jobbdatabasen*](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

-- Execute the latest version of a job
EXEC jobs.sp_start_job 'CreateTableTest'

-- Execute the latest version of a job and receive the execution id
declare @je uniqueidentifier
exec jobs.sp_start_job 'CreateTableTest', @job_execution_id = @je output
select @je

select * from jobs.job_executions where job_execution_id = @je

-- Execute a specific version of a job (e.g. version 1)
exec jobs.sp_start_job 'CreateTableTest', 1
```


## <a name="schedule-execution-of-a-job"></a>Schemalägga körning av ett jobb

I följande exempel visas hur du schemalägger ett jobb för framtida körning.  
Anslut till [*jobbdatabasen*](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Övervaka status för körning av jobb

I följande exempel visas hur du visar körningsstatusinformation för alla jobb.  
Anslut till [*jobbdatabasen*](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named 'ResultsPoolJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named 'ResultsPoolsJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions 
WHERE is_active = 1
ORDER BY start_time DESC
```


## <a name="cancel-a-job"></a>Avbryta ett jobb

I följande exempel visas hur du avbryter ett jobb.  
Anslut till [*jobbdatabasen*](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

-- View all active executions to determine job execution id
SELECT * FROM jobs.job_executions 
WHERE is_active = 1 AND job_name = 'ResultPoolsJob'
ORDER BY start_time DESC
GO

-- Cancel job execution with the specified job execution id
EXEC jobs.sp_stop_job '01234567-89ab-cdef-0123-456789abcdef'
```


## <a name="delete-old-job-history"></a>Ta bort gammal jobbhistorik

I följande exempel visas hur du tar bort jobbhistorik före ett visst datum.  
Anslut till [*jobbdatabasen*](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Ta bort ett jobb och all jobbhistorik

I följande exempel visas hur du tar bort ett jobb och all relaterad jobbhistorik.  
Anslut till [*jobbdatabasen*](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>Jobb lagrade procedurer

Följande lagrade procedurer finns i [jobbdatabasen](sql-database-job-automation-overview.md#job-database).



|Lagrad procedur  |Beskrivning  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     Lägger till ett nytt jobb.    |
|[sp_update_job](#sp_update_job)    |      Uppdaterar ett befintligt jobb.   |
|[sp_delete_job](#sp_delete_job)     |      Tar bort ett befintligt jobb.   |
|[sp_add_jobstep](#sp_add_jobstep)    |    Lägger till ett steg i ett jobb.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     Uppdaterar ett jobbsteg.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     Tar bort ett jobbsteg.    |
|[sp_start_job](#sp_start_job)    |  Börjar köra ett jobb.       |
|[sp_stop_job](#sp_stop_job)     |     Stoppar en jobbkörning.   |
|[sp_add_target_group](#sp_add_target_group)    |     Lägger till en målgrupp.    |
|[sp_delete_target_group](#sp_delete_target_group)     |    Tar bort en målgrupp.     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    Lägger till en databas eller grupp av databaser i en målgrupp.     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     Tar bort en målgruppsmedlem från en målgrupp.    |
|[sp_purge_jobhistory](#sp_purge_jobhistory)    |    Tar bort historikposterna för ett jobb.     |





### <a name="sp_add_job"></a><a name="sp_add_job"></a>sp_add_job

Lägger till ett nytt jobb. 
  
#### <a name="syntax"></a>Syntax  
  

```syntaxsql
[jobs].sp_add_job [ @job_name = ] 'job_name'  
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
    [ , [ @job_id = ] job_id OUTPUT ]
```

  
#### <a name="arguments"></a>Argument  

[ ** \@job_name =** ] "job_name"  
Namnet på jobbet. Namnet måste vara unikt och får inte innehålla procent (%) Karaktär. job_name är nvarchar(128), utan standard.

[ ** \@beskrivning =** ] "beskrivning"  
Beskrivningen av jobbet. är nvarchar(512), med standard null. Om beskrivning utelämnas används en tom sträng.

[ ** \@aktiverad =** ] aktiverad  
Om jobbets schema är aktiverat. Aktiverad är bit, med en standard på 0 (inaktiverad). Om 0 är jobbet inte aktiverat och körs inte enligt schemat. Det kan dock köras manuellt. Om 1 körs jobbet enligt schemat och kan även köras manuellt.

[ ** \@schedule_interval_type =**] schedule_interval_type  
Värdet anger när jobbet ska utföras. schedule_interval_type är nvarchar(50), med standardinställningen Once, och kan vara något av följande värden:
- "En gång",
- "Protokoll".
- "Timmar".
- "Dagar".
- "Veckor".
- "Månader"

[ ** \@schedule_interval_count =** ] schedule_interval_count  
Antal schedule_interval_count perioder som ska inträffa mellan varje körning av jobbet. schedule_interval_count är int, med en standard på 1. Värdet måste vara större än eller lika med 1.

[ ** \@schedule_start_time =** ] schedule_start_time  
Datum då jobbkörningen kan påbörjas. schedule_start_time är DATETIME2, med standardvärdet 0001-01-01 00:00:00.000000000.

[ ** \@schedule_end_time =** ] schedule_end_time  
Datum då jobbkörningen kan stoppas. schedule_end_time är DATETIME2, med standardvärdet 9999-12-31 11:59:59.0000000. 

[ ** \@job_id =** ] job_id PRODUKTION  
Det jobbidentifikationsnummer som tilldelats jobbet om det skapas. job_id är en utdatavariabel av typen uniqueidentifier.

#### <a name="return-code-values"></a>Värden för returkod

0 (framgång) eller 1 (misslyckande)

#### <a name="remarks"></a>Anmärkningar
sp_add_job måste köras från jobbagentdatabasen som anges när jobbagenten skapas.
När sp_add_job har körts för att lägga till ett jobb kan sp_add_jobstep användas för att lägga till steg som utför aktiviteterna för jobbet. Jobbets ursprungliga versionsnummer är 0, vilket ökas till 1 när det första steget läggs till.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. De begränsar en användare att bara kunna övervaka jobb, du kan ge användaren att vara en del av följande databasroll i jobbagentdatabasen som anges när jobbagenten skapas:

- jobs_reader

Mer information om behörigheterna för dessa roller finns i avsnittet Behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

### <a name="sp_update_job"></a><a name="sp_update_job"></a>sp_update_job

Uppdaterar ett befintligt jobb.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_update_job [ @job_name = ] 'job_name'  
    [ , [ @new_name = ] 'new_name' ]
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
```

#### <a name="arguments"></a>Argument
[ ** \@job_name =** ] "job_name"  
Namnet på det jobb som ska uppdateras. job_name är nvarchar(128).

[ ** \@new_name =** ] "new_name"  
Det nya namnet på jobbet. new_name är nvarchar(128).

[ ** \@beskrivning =** ] "beskrivning"  
Beskrivningen av jobbet. är nvarchar(512).

[ ** \@aktiverad =** ] aktiverad  
Anger om jobbets schema är aktiverat (1) eller inte aktiverat (0). Aktiverad är bit.

[ ** \@schedule_interval_type =** ] schedule_interval_type  
Värdet anger när jobbet ska utföras. schedule_interval_type är nvarchar(50) och kan vara något av följande värden:

- "En gång",
- "Protokoll".
- "Timmar".
- "Dagar".
- "Veckor".
- "Månader"

[ ** \@schedule_interval_count =** ] schedule_interval_count  
Antal schedule_interval_count perioder som ska inträffa mellan varje körning av jobbet. schedule_interval_count är int, med en standard på 1. Värdet måste vara större än eller lika med 1.

[ ** \@schedule_start_time =** ] schedule_start_time  
Datum då jobbkörningen kan påbörjas. schedule_start_time är DATETIME2, med standardvärdet 0001-01-01 00:00:00.000000000.

[ ** \@schedule_end_time =** ] schedule_end_time  
Datum då jobbkörningen kan stoppas. schedule_end_time är DATETIME2, med standardvärdet 9999-12-31 11:59:59.0000000. 

#### <a name="return-code-values"></a>Värden för returkod
0 (framgång) eller 1 (misslyckande)

#### <a name="remarks"></a>Anmärkningar
När sp_add_job har körts för att lägga till ett jobb kan sp_add_jobstep användas för att lägga till steg som utför aktiviteterna för jobbet. Jobbets ursprungliga versionsnummer är 0, vilket ökas till 1 när det första steget läggs till.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. De begränsar en användare att bara kunna övervaka jobb, du kan ge användaren att vara en del av följande databasroll i jobbagentdatabasen som anges när jobbagenten skapas:
- jobs_reader

Mer information om behörigheterna för dessa roller finns i avsnittet Behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.



### <a name="sp_delete_job"></a><a name="sp_delete_job"></a>sp_delete_job

Tar bort ett befintligt jobb.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argument
[ ** \@job_name =** ] "job_name"  
Namnet på det jobb som ska tas bort. job_name är nvarchar(128).

[ ** \@kraft =** ] kraft  
Anger om jobbet ska tas bort och alla pågående körningar (1) avbryts eller misslyckas om några jobbkörningar pågår (0). kraft är bit.

#### <a name="return-code-values"></a>Värden för returkod
0 (framgång) eller 1 (misslyckande)

#### <a name="remarks"></a>Anmärkningar
Jobbhistoriken tas automatiskt bort när ett jobb tas bort.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. De begränsar en användare att bara kunna övervaka jobb, du kan ge användaren att vara en del av följande databasroll i jobbagentdatabasen som anges när jobbagenten skapas:
- jobs_reader

Mer information om behörigheterna för dessa roller finns i avsnittet Behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.



### <a name="sp_add_jobstep"></a><a name="sp_add_jobstep"></a>sp_add_jobstep

Lägger till ett steg i ett jobb.

#### <a name="syntax"></a>Syntax


```syntaxsql
[jobs].sp_add_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] step_name ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_subscription_id = ] 'output_subscription_id' ]   
     [ , [ @output_resource_group_name = ] 'output_resource_group_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argument

[ ** \@job_name =** ] "job_name"  
Namnet på det jobb som steget ska läggas till. job_name är nvarchar(128).

[ ** \@step_id =** ] step_id  
Sekvens-ID-numret för jobbsteget. Stegidentifieringsnummer börjar vid 1 och öka utan mellanrum. Om ett befintligt steg redan har det här id:t kommer det steget och alla följande steg att ökas id:t så att det nya steget kan infogas i sekvensen. Om inget anges tilldelas step_id automatiskt den sista i stegsekvensen. step_id är en int.

[ ** \@step_name =** ] step_name  
Namnet på steget. Måste anges, med undantag för det första steget i ett jobb som (för enkelhetens skull) har standardnamnet "JobStep". step_name är nvarchar(128).

[ ** \@command_type =** ] "command_type"  
Den typ av kommando som körs av det här jobbsteget. command_type är nvarchar(50), med ett standardvärde på TSql, @command_type vilket innebär att parameterns värde är ett T-SQL-skript.

Om det anges måste värdet vara TSql.

[ ** \@command_source =** ] "command_source"  
Den typ av plats där kommandot lagras. command_source är nvarchar(50), med standardvärdet Infogat, vilket innebär att @command_source parameterns värde är kommandots bokstavliga text.

Om det anges måste värdet vara Infogat.

[ ** \@kommando =** ] "kommando"  
Kommandot måste vara giltigt T-SQL-skript och körs sedan av det här jobbsteget. är nvarchar(max), med standardvärdet NULL.

[ ** \@credential_name =** ] "credential_name"  
Namnet på den databasomfångade autentiseringsuppgifter som lagras i den här jobbkontrolldatabasen och som används för att ansluta till var och en av måldatabaserna i målgruppen när det här steget körs. credential_name är nvarchar(128).

[ ** \@target_group_name =** ] "mål-group_name"  
Namnet på den målgrupp som innehåller måldatabaserna som jobbsteget ska utföras på. target_group_name är nvarchar(128).

[ ** \@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Fördröjningen före det första försöket att försöka igen, om jobbsteget misslyckas på det första körningsförsöket. initial_retry_interval_seconds är int, med standardvärdet 1.

[ ** \@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Den maximala fördröjningen mellan försök till nytt försök. Om fördröjningen mellan återförsök skulle växa sig större än det här värdet begränsas den till det här värdet i stället. maximum_retry_interval_seconds är int, med standardvärdet 120.

[ ** \@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Multiplikatorn som ska tillämpas på fördröjningen för återförsök om körningsförsök i flera jobbsteg misslyckas. Om det första återförsöket till exempel hade en fördröjning på 5 sekunder och backoff-multiplikatorn är 2,0, kommer det andra återförsöket att få en fördröjning på 10 sekunder och det tredje återförsöket kommer att ha en fördröjning på 20 sekunder. retry_interval_backoff_multiplier är verklig, med standardvärdet 2.0.

[ ** \@retry_attempts =** ] retry_attempts  
Antalet gånger för att försöka utföra körningen igen om det första försöket misslyckas. Om värdet retry_attempts till exempel är 10 kommer det att finnas 1 inledande försök och 10 försök att försöka igen, vilket ger totalt 11 försök. Om det slutliga försöket om försök misslyckas avslutas jobbkörningen med en livscykel misslyckades. retry_attempts är int, med standardvärdet 10.

[ ** \@step_timeout_seconds =** ] step_timeout_seconds  
Den maximala tid som tillåts för steget att köras. Om den här tiden överskrids avslutas jobbkörningen med en livscykel med TimedOut. step_timeout_seconds är int, med standardvärdet 43 200 sekunder (12 timmar).

[ ** \@output_type =** ] "output_type"  
Om inte null skrivs den typ av mål som kommandots första resultatuppsättning skrivs till. output_type är nvarchar(50), med standard null.

Om det anges måste värdet vara SqlDatabase.

[ ** \@output_credential_name =** ] "output_credential_name"  
Om inte null, namnet på databasen scoped autentiseringsuppgifter som används för att ansluta till utdata måldatabasen. Måste anges om output_type är lika med SqlDatabase. output_credential_name är nvarchar(128), med standardvärdet NULL.

[ ** \@output_subscription_id =** ] "output_subscription_id"  
Behöver beskrivning.

[ ** \@output_resource_group_name =** ] "output_resource_group_name"  
Behöver beskrivning.

[ ** \@output_server_name =** ] "output_server_name"  
Om inte null, det fullständigt kvalificerade DNS-namnet på den server som innehåller utdatamåldatabasen. Måste anges om output_type är lika med SqlDatabase. output_server_name är nvarchar(256), med standard null.

[ ** \@output_database_name =** ] "output_database_name"  
Om inte null, namnet på den databas som innehåller utdatamåltabellen. Måste anges om output_type är lika med SqlDatabase. output_database_name är nvarchar(128), med standard null.

[ ** \@output_schema_name =** ] "output_schema_name"  
Om inte null, namnet på SQL-schemat som innehåller utdatamåltabellen. Om output_type är lika med SqlDatabase är standardvärdet dbo. output_schema_name är nvarchar(128).

[ ** \@output_table_name =** ] "output_table_name"  
Om inte null skrivs namnet på tabellen som kommandots första resultatuppsättning skrivs till. Om tabellen inte redan finns skapas den baserat på schemat för den returnerande resultatuppsättningen. Måste anges om output_type är lika med SqlDatabase. output_table_name är nvarchar(128), med standardvärdet NULL.

[ ** \@job_version =** ] job_version PRODUKTION  
Utdataparameter som tilldelas det nya jobbversionsnumret. job_version är int.

[ ** \@max_parallelism =** ] max_parallelism PRODUKTION  
Den maximala nivån av parallellism per elastisk pool. Om den är inställd begränsas jobbsteget till att endast köras på högst så många databaser per elastisk pool. Detta gäller för varje elastisk pool som antingen ingår direkt i målgruppen eller som finns i en server som ingår i målgruppen. max_parallelism är int.


#### <a name="return-code-values"></a>Värden för returkod
0 (framgång) eller 1 (misslyckande)

#### <a name="remarks"></a>Anmärkningar
När sp_add_jobstep lyckas ökas jobbets aktuella versionsnummer. Nästa gång jobbet körs används den nya versionen. Om jobbet körs innehåller körningen inte det nya steget.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. De begränsar en användare att bara kunna övervaka jobb, du kan ge användaren att vara en del av följande databasroll i jobbagentdatabasen som anges när jobbagenten skapas:  

- jobs_reader

Mer information om behörigheterna för dessa roller finns i avsnittet Behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.



### <a name="sp_update_jobstep"></a><a name="sp_update_jobstep"></a>sp_update_jobstep

Uppdaterar ett jobbsteg.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_update_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @new_id = ] new_id ]   
     [ , [ @new_name = ] 'new_name' ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argument
[ ** \@job_name =** ] "job_name"  
Namnet på det jobb som steget hör till. job_name är nvarchar(128).

[ ** \@step_id =** ] step_id  
Id-numret för jobbsteget som ska ändras. Antingen step_id eller step_name måste anges. step_id är en int.

[ ** \@step_name =** ] "step_name"  
Namnet på det steg som ska ändras. Antingen step_id eller step_name måste anges. step_name är nvarchar(128).

[ ** \@new_id =** ] new_id  
Det nya sekvens-ID-numret för jobbsteget. Stegidentifieringsnummer börjar vid 1 och öka utan mellanrum. Om ett steg ordnas om numreras andra steg automatiskt om.

[ ** \@new_name =** ] "new_name"  
Det nya namnet på steget. new_name är nvarchar(128).

[ ** \@command_type =** ] "command_type"  
Den typ av kommando som körs av det här jobbsteget. command_type är nvarchar(50), med ett standardvärde på TSql, @command_type vilket innebär att parameterns värde är ett T-SQL-skript.

Om det anges måste värdet vara TSql.

[ ** \@command_source =** ] "command_source"  
Den typ av plats där kommandot lagras. command_source är nvarchar(50), med standardvärdet Infogat, vilket innebär att @command_source parameterns värde är kommandots bokstavliga text.

Om det anges måste värdet vara Infogat.

[ ** \@kommando =** ] "kommando"  
Kommandona måste vara giltiga T-SQL-skript och körs sedan av det här jobbsteget. är nvarchar(max), med standardvärdet NULL.

[ ** \@credential_name =** ] "credential_name"  
Namnet på den databasomfångade autentiseringsuppgifter som lagras i den här jobbkontrolldatabasen och som används för att ansluta till var och en av måldatabaserna i målgruppen när det här steget körs. credential_name är nvarchar(128).

[ ** \@target_group_name =** ] "mål-group_name"  
Namnet på den målgrupp som innehåller måldatabaserna som jobbsteget ska utföras på. target_group_name är nvarchar(128).

[ ** \@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Fördröjningen före det första försöket att försöka igen, om jobbsteget misslyckas på det första körningsförsöket. initial_retry_interval_seconds är int, med standardvärdet 1.

[ ** \@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Den maximala fördröjningen mellan försök till nytt försök. Om fördröjningen mellan återförsök skulle växa sig större än det här värdet begränsas den till det här värdet i stället. maximum_retry_interval_seconds är int, med standardvärdet 120.

[ ** \@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Multiplikatorn som ska tillämpas på fördröjningen för återförsök om körningsförsök i flera jobbsteg misslyckas. Om det första återförsöket till exempel hade en fördröjning på 5 sekunder och backoff-multiplikatorn är 2,0, kommer det andra återförsöket att få en fördröjning på 10 sekunder och det tredje återförsöket kommer att ha en fördröjning på 20 sekunder. retry_interval_backoff_multiplier är verklig, med standardvärdet 2.0.

[ ** \@retry_attempts =** ] retry_attempts  
Antalet gånger för att försöka utföra körningen igen om det första försöket misslyckas. Om värdet retry_attempts till exempel är 10 kommer det att finnas 1 inledande försök och 10 försök att försöka igen, vilket ger totalt 11 försök. Om det slutliga försöket om försök misslyckas avslutas jobbkörningen med en livscykel misslyckades. retry_attempts är int, med standardvärdet 10.

[ ** \@step_timeout_seconds =** ] step_timeout_seconds  
Den maximala tid som tillåts för steget att köras. Om den här tiden överskrids avslutas jobbkörningen med en livscykel med TimedOut. step_timeout_seconds är int, med standardvärdet 43 200 sekunder (12 timmar).

[ ** \@output_type =** ] "output_type"  
Om inte null skrivs den typ av mål som kommandots första resultatuppsättning skrivs till. Om du vill återställa värdet för output_type tillbaka till NULL anger du den här parameterns värde på '' (tom sträng). output_type är nvarchar(50), med standard null.

Om det anges måste värdet vara SqlDatabase.

[ ** \@output_credential_name =** ] "output_credential_name"  
Om inte null, namnet på databasen scoped autentiseringsuppgifter som används för att ansluta till utdata måldatabasen. Måste anges om output_type är lika med SqlDatabase. Om du vill återställa värdet för output_credential_name tillbaka till NULL anger du den här parameterns värde på '' (tom sträng). output_credential_name är nvarchar(128), med standardvärdet NULL.

[ ** \@output_server_name =** ] "output_server_name"  
Om inte null, det fullständigt kvalificerade DNS-namnet på den server som innehåller utdatamåldatabasen. Måste anges om output_type är lika med SqlDatabase. Om du vill återställa värdet för output_server_name tillbaka till NULL anger du den här parameterns värde på '' (tom sträng). output_server_name är nvarchar(256), med standard null.

[ ** \@output_database_name =** ] "output_database_name"  
Om inte null, namnet på den databas som innehåller utdatamåltabellen. Måste anges om output_type är lika med SqlDatabase. Om du vill återställa värdet för output_database_name tillbaka till NULL anger du den här parameterns värde på '' (tom sträng). output_database_name är nvarchar(128), med standard null.

[ ** \@output_schema_name =** ] "output_schema_name"  
Om inte null, namnet på SQL-schemat som innehåller utdatamåltabellen. Om output_type är lika med SqlDatabase är standardvärdet dbo. Om du vill återställa värdet för output_schema_name tillbaka till NULL anger du den här parameterns värde på '' (tom sträng). output_schema_name är nvarchar(128).

[ ** \@output_table_name =** ] "output_table_name"  
Om inte null skrivs namnet på tabellen som kommandots första resultatuppsättning skrivs till. Om tabellen inte redan finns skapas den baserat på schemat för den returnerande resultatuppsättningen. Måste anges om output_type är lika med SqlDatabase. Om du vill återställa värdet för output_server_name tillbaka till NULL anger du den här parameterns värde på '' (tom sträng). output_table_name är nvarchar(128), med standardvärdet NULL.

[ ** \@job_version =** ] job_version PRODUKTION  
Utdataparameter som tilldelas det nya jobbversionsnumret. job_version är int.

[ ** \@max_parallelism =** ] max_parallelism PRODUKTION  
Den maximala nivån av parallellism per elastisk pool. Om den är inställd begränsas jobbsteget till att endast köras på högst så många databaser per elastisk pool. Detta gäller för varje elastisk pool som antingen ingår direkt i målgruppen eller som finns i en server som ingår i målgruppen. Om du vill återställa värdet för max_parallelism tillbaka till null anger du den här parameterns värde till -1. max_parallelism är int.


#### <a name="return-code-values"></a>Värden för returkod
0 (framgång) eller 1 (misslyckande)

#### <a name="remarks"></a>Anmärkningar
Eventuella pågående körningar av jobbet kommer inte att påverkas. När sp_update_jobstep lyckas ökas jobbets versionsnummer. Nästa gång jobbet körs används den nya versionen.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. De begränsar en användare att bara kunna övervaka jobb, du kan ge användaren att vara en del av följande databasroll i jobbagentdatabasen som anges när jobbagenten skapas:

- jobs_reader

Mer information om behörigheterna för dessa roller finns i avsnittet Behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare




### <a name="sp_delete_jobstep"></a><a name="sp_delete_jobstep"></a>sp_delete_jobstep

Tar bort ett jobbsteg från ett jobb.

#### <a name="syntax"></a>Syntax


```syntaxsql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argument
[ ** \@job_name =** ] "job_name"  
Namnet på det jobb som steget ska tas bort från. job_name är nvarchar(128), utan standard.

[ ** \@step_id =** ] step_id  
Id-numret för jobbsteget som ska tas bort. Antingen step_id eller step_name måste anges. step_id är en int.

[ ** \@step_name =** ] "step_name"  
Namnet på det steg som ska tas bort. Antingen step_id eller step_name måste anges. step_name är nvarchar(128).

[ ** \@job_version =** ] job_version PRODUKTION  
Utdataparameter som tilldelas det nya jobbversionsnumret. job_version är int.

#### <a name="return-code-values"></a>Värden för returkod
0 (framgång) eller 1 (misslyckande)

#### <a name="remarks"></a>Anmärkningar
Eventuella pågående körningar av jobbet kommer inte att påverkas. När sp_update_jobstep lyckas ökas jobbets versionsnummer. Nästa gång jobbet körs används den nya versionen.

De andra jobbstegen numreras automatiskt om för att fylla det mellanrum som återstår av det borttagna jobbsteget.
 
#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. De begränsar en användare att bara kunna övervaka jobb, du kan ge användaren att vara en del av följande databasroll i jobbagentdatabasen som anges när jobbagenten skapas:
- jobs_reader

Mer information om behörigheterna för dessa roller finns i avsnittet Behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.






### <a name="sp_start_job"></a><a name="sp_start_job"></a>sp_start_job

Börjar köra ett jobb.

#### <a name="syntax"></a>Syntax


```syntaxsql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argument
[ ** \@job_name =** ] "job_name"  
Namnet på det jobb som steget ska tas bort från. job_name är nvarchar(128), utan standard.

[ ** \@job_execution_id =** ] job_execution_id PRODUKTION  
Utdataparameter som tilldelas jobbkörningens ID. job_version är unikidentifierare.

#### <a name="return-code-values"></a>Värden för returkod
0 (framgång) eller 1 (misslyckande)

#### <a name="remarks"></a>Anmärkningar
Inga.
 
#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. De begränsar en användare att bara kunna övervaka jobb, du kan ge användaren att vara en del av följande databasroll i jobbagentdatabasen som anges när jobbagenten skapas:
- jobs_reader

Mer information om behörigheterna för dessa roller finns i avsnittet Behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

### <a name="sp_stop_job"></a><a name="sp_stop_job"></a>sp_stop_job

Stoppar en jobbkörning.

#### <a name="syntax"></a>Syntax


```syntaxsql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argument
[ ** \@job_execution_id =** ] job_execution_id  
Id-numret för jobbkörningen som ska stoppas. job_execution_id är uniqueidentifier, med standard NULL.

#### <a name="return-code-values"></a>Värden för returkod
0 (framgång) eller 1 (misslyckande)

#### <a name="remarks"></a>Anmärkningar
Inga.
 
#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. De begränsar en användare att bara kunna övervaka jobb, du kan ge användaren att vara en del av följande databasroll i jobbagentdatabasen som anges när jobbagenten skapas:
- jobs_reader

Mer information om behörigheterna för dessa roller finns i avsnittet Behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.


### <a name="sp_add_target_group"></a><a name="sp_add_target_group"></a>sp_add_target_group

Lägger till en målgrupp.

#### <a name="syntax"></a>Syntax


```syntaxsql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Argument
[ ** \@target_group_name =** ] "target_group_name"  
Namnet på den målgrupp som ska skapas. target_group_name är nvarchar(128), utan standard.

[ ** \@target_group_id =** ] target_group_id UTDATA Identifieringsnumret för målgruppen som tilldelats jobbet om det skapas. target_group_id är en utdatavariabel av typen uniqueidentifier, med standard null.

#### <a name="return-code-values"></a>Värden för returkod
0 (framgång) eller 1 (misslyckande)

#### <a name="remarks"></a>Anmärkningar
Målgrupper är ett enkelt sätt att rikta ett jobb mot en samling databaser.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. De begränsar en användare att bara kunna övervaka jobb, du kan ge användaren att vara en del av följande databasroll i jobbagentdatabasen som anges när jobbagenten skapas:
- jobs_reader

Mer information om behörigheterna för dessa roller finns i avsnittet Behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

### <a name="sp_delete_target_group"></a><a name="sp_delete_target_group"></a>sp_delete_target_group

Tar bort en målgrupp.

#### <a name="syntax"></a>Syntax


```syntaxsql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Argument
[ ** \@target_group_name =** ] "target_group_name"  
Namnet på den målgrupp som ska tas bort. target_group_name är nvarchar(128), utan standard.

#### <a name="return-code-values"></a>Värden för returkod
0 (framgång) eller 1 (misslyckande)

#### <a name="remarks"></a>Anmärkningar
Inga.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. De begränsar en användare att bara kunna övervaka jobb, du kan ge användaren att vara en del av följande databasroll i jobbagentdatabasen som anges när jobbagenten skapas:
- jobs_reader

Mer information om behörigheterna för dessa roller finns i avsnittet Behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

### <a name="sp_add_target_group_member"></a><a name="sp_add_target_group_member"></a>sp_add_target_group_member

Lägger till en databas eller grupp av databaser i en målgrupp.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] 'membership_type' ]   
        [ , [ @target_type = ] 'target_type' ]   
        [ , [ @refresh_credential_name = ] 'refresh_credential_name' ]   
        [ , [ @server_name = ] 'server_name' ]   
        [ , [ @database_name = ] 'database_name' ]   
        [ , [ @elastic_pool_name = ] 'elastic_pool_name' ]   
        [ , [ @shard_map_name = ] 'shard_map_name' ]   
        [ , [ @target_id = ] 'target_id' OUTPUT ]
```

#### <a name="arguments"></a>Argument
[ ** \@target_group_name =** ] "target_group_name"  
Namnet på den målgrupp som medlemmen ska läggas till i. target_group_name är nvarchar(128), utan standard.

[ ** \@membership_type =** ] "membership_type"  
Anger om målgruppsmedlemmen ska inkluderas eller uteslutas. target_group_name är nvarchar(128), med standard "Inkludera". Giltiga värden för target_group_name är "Inkludera" eller "Uteslut".

[ ** \@target_type =** ] "target_type"  
Typ av måldatabas eller samling av databaser, inklusive alla databaser i en server, alla databaser i en elastisk pool, alla databaser i en fragmentkarta eller en enskild databas. target_type är nvarchar(128), utan standard. Giltiga värden för target_type är "SqlServer", "SqlElasticPool", "SqlDatabase" eller "SqlShardMap". 

[ ** \@refresh_credential_name =** ] "refresh_credential_name"  
Namnet på SQL Database-servern. refresh_credential_name är nvarchar(128), utan standard.

[ ** \@server_name =** ] "server_name"  
Namnet på SQL Database-servern som ska läggas till i den angivna målgruppen. server_name bör anges när target_type är "SqlServer". server_name är nvarchar(128), utan standard.

[ ** \@database_name =** ] "database_name"  
Namnet på databasen som ska läggas till i den angivna målgruppen. database_name bör anges när target_type är "SqlDatabase". database_name är nvarchar(128), utan standard.

[ ** \@elastic_pool_name =** ] "elastic_pool_name"  
Namnet på den elastiska poolen som ska läggas till i den angivna målgruppen. elastic_pool_name bör anges när target_type är "SqlElasticPool". elastic_pool_name är nvarchar(128), utan standard.

[ ** \@shard_map_name =** ] "shard_map_name"  
Namnet på fragmentmappningspoolen som ska läggas till i den angivna målgruppen. elastic_pool_name bör anges när target_type är "SqlSqlShardMap". shard_map_name är nvarchar(128), utan standard.

[ ** \@target_id =** ] target_group_id PRODUKTION  
Målidentifieringsnumret som tilldelats målgruppsmedlemmen om det skapas läggs till i målgruppen. target_id är en utdatavariabel av typen uniqueidentifier, med standard null.
Returnera kodvärden 0 (lyckad) eller 1 (fel)

#### <a name="remarks"></a>Anmärkningar
Ett jobb körs på alla enskilda databaser i en SQL Database-server eller i en elastisk pool vid tidpunkten för körningen, när en SQL Database-server eller elastisk pool ingår i målgruppen.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. De begränsar en användare att bara kunna övervaka jobb, du kan ge användaren att vara en del av följande databasroll i jobbagentdatabasen som anges när jobbagenten skapas:
- jobs_reader

Mer information om behörigheterna för dessa roller finns i avsnittet Behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

#### <a name="examples"></a>Exempel
I följande exempel läggs alla databaser i London- och NewYork-servrarna till gruppen Servrar som underhåller kundinformation. Du måste ansluta till jobbdatabasen som angavs när jobbagenten skapas, i det här fallet ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group @target_group_name =  N'Servers Maintaining Customer Information'
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net' ;
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'NewYork.database.windows.net' ;
GO

--View the recently added members to the target group
SELECT * FROM [jobs].target_group_members WHERE target_group_name= N'Servers Maintaining Customer Information';
GO
```

### <a name="sp_delete_target_group_member"></a><a name="sp_delete_target_group_member"></a>sp_delete_target_group_member

Tar bort en målgruppsmedlem från en målgrupp.

#### <a name="syntax"></a>Syntax


```syntaxsql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] 'target_id']
```



Argument @target_group_name [ = ] "target_group_name"  
Namnet på målgruppen som målgruppsmedlemmen ska tas bort från. target_group_name är nvarchar(128), utan standard.

[ @target_id = ] target_id  
 Målidentifieringsnumret som tilldelats den målgruppsmedlem som ska tas bort. target_id är en uniqueidentifier, med standard null.

#### <a name="return-code-values"></a>Värden för returkod
0 (framgång) eller 1 (misslyckande)

#### <a name="remarks"></a>Anmärkningar
Målgrupper är ett enkelt sätt att rikta ett jobb mot en samling databaser.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. De begränsar en användare att bara kunna övervaka jobb, du kan ge användaren att vara en del av följande databasroll i jobbagentdatabasen som anges när jobbagenten skapas:
- jobs_reader

Mer information om behörigheterna för dessa roller finns i avsnittet Behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

#### <a name="examples"></a>Exempel
I följande exempel tas London-servern bort från gruppen Servrar som underhåller kundinformation. Du måste ansluta till jobbdatabasen som angavs när jobbagenten skapas, i det här fallet ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Retrieve the target_id for a target_group_members
declare @tid uniqueidentifier
SELECT @tid = target_id FROM [jobs].target_group_members WHERE target_group_name = 'Servers Maintaining Customer Information' and server_name = 'London.database.windows.net'

-- Remove a target group member of type server
EXEC jobs.sp_delete_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_id = @tid
GO
```

### <a name="sp_purge_jobhistory"></a><a name="sp_purge_jobhistory"></a>sp_purge_jobhistory

Tar bort historikposterna för ett jobb.

#### <a name="syntax"></a>Syntax


```syntaxsql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argument
[ ** \@job_name =** ] "job_name"  
Namnet på det jobb som historikposterna ska tas bort för. job_name är nvarchar(128), med standard null. Antingen job_id eller job_name måste anges, men båda kan inte anges.

[ ** \@job_id =** ] job_id  
 Jobb-ID-numret för jobbet för de poster som ska tas bort. job_id är uniqueidentifier, med standard NULL. Antingen job_id eller job_name måste anges, men båda kan inte anges.

[ ** \@oldest_date =** ] oldest_date  
 Den äldsta posten att behålla i historien. oldest_date är DATETIME2, med standard null. När oldest_date anges tar sp_purge_jobhistory bara bort poster som är äldre än det angivna värdet.

#### <a name="return-code-values"></a>Värden för returkod
0 (framgång) eller 1 (misslyckande) Anmärkningar Målgrupper ger ett enkelt sätt att rikta ett jobb på en samling databaser.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. De begränsar en användare att bara kunna övervaka jobb, du kan ge användaren att vara en del av följande databasroll i jobbagentdatabasen som anges när jobbagenten skapas:
- jobs_reader

Mer information om behörigheterna för dessa roller finns i avsnittet Behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attributen för jobb som ägs av andra användare.

#### <a name="examples"></a>Exempel
I följande exempel läggs alla databaser i London- och NewYork-servrarna till gruppen Servrar som underhåller kundinformation. Du måste ansluta till jobbdatabasen som angavs när jobbagenten skapas, i det här fallet ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Jobbvyer

Följande vyer är tillgängliga i [jobbdatabasen](sql-database-job-automation-overview.md#job-database).


|Visa  |Beskrivning  |
|---------|---------|
|[job_executions](#job_executions-view)     |  Visar jobbkörningshistorik.      |
|[Jobb](#jobs-view)     |   Visar alla jobb.      |
|[job_versions](#job_versions-view)     |   Visar alla jobbversioner.      |
|[jobbsteg](#jobsteps-view)     |     Visar alla steg i den aktuella versionen av varje jobb.    |
|[jobstep_versions](#jobstep_versions-view)     |     Visar alla steg i alla versioner av varje jobb.    |
|[target_groups](#target_groups-view)     |      Visar alla målgrupper.   |
|[target_group_members](#target_group_members-view)     |   Visar alla medlemmar i alla målgrupper.      |


### <a name="job_executions-view"></a><a name="job_executions-view"></a>job_executions vy

[jobb]. - Jag vet inte vad du job_executions.

Visar jobbkörningshistorik.


|Kolumnnamn|   Datatyp   |Beskrivning|
|---------|---------|---------|
|**job_execution_id**   |uniqueidentifier|  Unikt ID för en instans av en jobbkörning.
|**job_name**   |nvarchar(128)  |Jobbets namn.
|**job_id** |uniqueidentifier|  Unikt ID för jobbet.
|**job_version**    |int    |Version av jobbet (uppdateras automatiskt varje gång jobbet ändras).
|**step_id**    |int|   Unik (för det här jobbet) identifierare för steget. NULL anger att det här är den överordnade jobbkörningen.
|**is_active**| bit |Anger om informationen är aktiv eller inaktiv. 1 anger aktiva jobb och 0 anger inaktiva.
|**Livscykel**| nvarchar(50)|Värde som anger jobbets status:'Skapad', "Pågår", "Misslyckades", "lyckades", "Hoppas över", "lyckadesMedSkipped"|
|**create_time**|   datumtid2(7)|   Datum och tid då jobbet skapades.
|**start_time** |datumtid2(7)|  Datum och tid då jobbet startade körningen. NULL om jobbet ännu inte har utförts.
|**end_time**|  datumtid2(7)    |Datum och tid då jobbet har körts. NULL om jobbet ännu inte har utförts eller ännu inte har slutfört körningen.
|**current_attempts**   |int    |Antal gånger steget gjordes på nytt. Det överordnade jobbet kommer att vara 0, underordnade jobbkörningar blir 1 eller högre baserat på körningsprincipen.
|**current_attempt_start_time** |datumtid2(7)|  Datum och tid då jobbet startade körningen. NULL anger att det här är den överordnade jobbkörningen.
|**last_message**   |nvarchar(max)| Jobb- eller steghistorikmeddelande. 
|**target_type**|   nvarchar(128)   |Typ av måldatabas eller samling av databaser, inklusive alla databaser på en server, alla databaser i en elastisk pool eller en databas. Giltiga värden för target_type är "SqlServer", "SqlElasticPool" eller "SqlDatabase". NULL anger att det här är den överordnade jobbkörningen.
|**target_id**  |uniqueidentifier|  Unikt ID för målgruppsmedlemmen.  NULL anger att det här är den överordnade jobbkörningen.
|**target_group_name**  |nvarchar(128)  |Målgruppens namn. NULL anger att det här är den överordnade jobbkörningen.
|**target_server_name**|    nvarchar(256)|  Namn på SQL Database-servern som finns i målgruppen. Anges endast om target_type är "SqlServer". NULL anger att det här är den överordnade jobbkörningen.
|**target_database_name**   |nvarchar(128)| Namnet på databasen i målgruppen. Anges endast när target_type är "SqlDatabase". NULL anger att det här är den överordnade jobbkörningen.


### <a name="jobs-view"></a>jobbvy

[jobb]. - Jag vet inte vad du ska ta på dig.

Visar alla jobb.

|Kolumnnamn|   Datatyp|  Beskrivning|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Jobbets namn.|
|**job_id**|    uniqueidentifier    |Unikt ID för jobbet.|
|**job_version**    |int    |Version av jobbet (uppdateras automatiskt varje gång jobbet ändras).|
|**Beskrivning**    |nvarchar(512)| Beskrivning för jobbet. aktiverad bit Anger om jobbet är aktiverat eller inaktiverat. 1 anger aktiverade jobb och 0 anger inaktiverade jobb.|
|**schedule_interval_type** |nvarchar(50)   |Värde som anger när jobbet ska utföras:"En gång", "Minuter", "Timmar", "Dagar", "Veckor", "Månader"
|**schedule_interval_count**|   int|    Antal schedule_interval_type perioder som ska inträffa mellan varje körning av jobbet.|
|**schedule_start_time**    |datumtid2(7)|  Datum och tid då jobbet senast startades.|
|**schedule_end_time**| datumtid2(7)|   Datum och tid då jobbet senast slutfördes.|


### <a name="job_versions-view"></a><a name="job_versions-view"></a>job_versions vy

[jobb]. - Jag vet job_versions inte vad du går för.

Visar alla jobbversioner.

|Kolumnnamn|   Datatyp|  Beskrivning|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Jobbets namn.|
|**job_id**|    uniqueidentifier    |Unikt ID för jobbet.|
|**job_version**    |int    |Version av jobbet (uppdateras automatiskt varje gång jobbet ändras).|


### <a name="jobsteps-view"></a>jobbstegsvy

[jobb]. - Jag vet inte vad du ska gå.

Visar alla steg i den aktuella versionen av varje jobb.

|Kolumnnamn    |Datatyp| Beskrivning|
|------|------|-------|
|**job_name**   |nvarchar(128)| Jobbets namn.|
|**job_id** |uniqueidentifier   |Unikt ID för jobbet.|
|**job_version**|   int|    Version av jobbet (uppdateras automatiskt varje gång jobbet ändras).|
|**step_id**    |int    |Unik (för det här jobbet) identifierare för steget.|
|**step_name**  |nvarchar(128)  |Unikt (för det här jobbet) för steget.|
|**command_type**   |nvarchar(50)   |Typ av kommando som ska köras i jobbsteget. För v1 måste värdet vara lika med och som standard "TSql".|
|**command_source** |nvarchar(50)|  Platsen för kommandot. För v1 är 'Infoga' standardvärdet och endast accepterat värde.|
|**command**|   nvarchar(max)|  Kommandona som ska utföras av elastiska jobb genom command_type.|
|**credential_name**|   nvarchar(128)   |Namn på den databasscopen som används för att utföra jobbet.|
|**target_group_name**| nvarchar(128)   |Målgruppens namn.|
|**target_group_id**|   uniqueidentifier|   Unikt ID för målgruppen.|
|**initial_retry_interval_seconds**|    int |Fördröjningen före det första försöket för ett nytt försök. Standardvärdet är 1.|
|**maximum_retry_interval_seconds** |int|   Den maximala fördröjningen mellan försök till nytt försök. Om fördröjningen mellan återförsök skulle växa sig större än det här värdet begränsas den till det här värdet i stället. Standardvärdet är 120.|
|**retry_interval_backoff_multiplier**  |real|  Multiplikatorn som ska tillämpas på fördröjningen för återförsök om körningsförsök i flera jobbsteg misslyckas. Standardvärdet är 2,0.|
|**retry_attempts** |int|   Antalet försök att använda igen om det här steget misslyckas. Standard 10, vilket indikerar inga försök att försöka igen.|
|**step_timeout_seconds**   |int|   Hur lång tid i minuter mellan försöken att försöka igen. Standardvärdet är 0, vilket indikerar ett intervall på 0 minuter.|
|**output_type**    |nvarchar(11)|  Platsen för kommandot. I den aktuella förhandsgranskningen är "Infogad" standardvärdet och det enda accepterade värdet.|
|**output_credential_name**|    nvarchar(128)   |Namnet på de autentiseringsuppgifter som ska användas för att ansluta till målservern för att lagra resultatuppsättningen.|
|**output_subscription_id**|    uniqueidentifier|   Unikt ID för prenumerationen på målservern\databas för resultatuppsättningen från frågekörningen.|
|**output_resource_group_name** |nvarchar(128)| Resursgruppsnamn där målservern finns.|
|**output_server_name**|    nvarchar(256)   |Namn på målservern för resultatuppsättningen.|
|**output_database_name**   |nvarchar(128)| Namn på måldatabasen för resultatuppsättningen.|
|**output_schema_name** |nvarchar(max)| Namn på målschemat. Standard till dbo, om det inte anges.|
|**output_table_name**| nvarchar(max)|  Namn på tabellen för att lagra resultatuppsättningen från frågeresultaten. Tabellen skapas automatiskt baserat på schemat för resultatuppsättningen om den inte redan finns. Schemat måste matcha schemat för resultatuppsättningen.|
|**max_parallelism**|   int|    Det maximala antalet databaser per elastisk pool som jobbsteget körs på åt gången. Standardvärdet är NULL, vilket innebär ingen gräns. |


### <a name="jobstep_versions-view"></a><a name="jobstep_versions-view"></a>jobstep_versions vy

[jobb]. - Jag vet inte vad du jobstep_versions.

Visar alla steg i alla versioner av varje jobb. Schemat är identiskt med [jobbsteg](#jobsteps-view).

### <a name="target_groups-view"></a><a name="target_groups-view"></a>target_groups vy

[jobb]. - Jag vet inte vad du target_groups.

Visar alla målgrupper.

|Kolumnnamn|Datatyp| Beskrivning|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |Namnet på målgruppen, en samling databaser. 
|**target_group_id**    |uniqueidentifier   |Unikt ID för målgruppen.

### <a name="target_group_members-view"></a><a name="target_group_members-view"></a>target_group_members vy

[jobb]. - Jag vet inte vad du target_group_members.

Visar alla medlemmar i alla målgrupper.

|Kolumnnamn|Datatyp| Beskrivning|
|-----|-----|-----|
|**target_group_name**  |nvarchar(128)|Namnet på målgruppen, en samling databaser. |
|**target_group_id**    |uniqueidentifier   |Unikt ID för målgruppen.|
|**membership_type**    |int|   Anger om målgruppsmedlemmen inkluderas eller utesluts i målgruppen. Giltiga värden för target_group_name är "Inkludera" eller "Uteslut".|
|**target_type**    |nvarchar(128)| Typ av måldatabas eller samling av databaser, inklusive alla databaser på en server, alla databaser i en elastisk pool eller en databas. Giltiga värden för target_type är "SqlServer", "SqlElasticPool", "SqlDatabase" eller "SqlShardMap".|
|**target_id**  |uniqueidentifier|  Unikt ID för målgruppsmedlemmen.|
|**refresh_credential_name**    |nvarchar(128)  |Namn på den databasscopen som används för att ansluta till målgruppsmedlemmen.|
|**subscription_id**    |uniqueidentifier|  Unikt ID för prenumerationen.|
|**resource_group_name**    |nvarchar(128)| Namn på den resursgrupp där målgruppsmedlemmen finns.|
|**Servernamn**    |nvarchar(128)  |Namn på SQL Database-servern som finns i målgruppen. Anges endast om target_type är "SqlServer". |
|**database_name**  |nvarchar(128)  |Namnet på databasen i målgruppen. Anges endast när target_type är "SqlDatabase".|
|**elastic_pool_name**  |nvarchar(128)| Namnet på den elastiska poolen i målgruppen. Anges endast när target_type är "SqlElasticPool".|
|**shard_map_name** |nvarchar(128)| Namnet på fragmentkartan som finns i målgruppen. Anges endast när target_type är "SqlShardMap".|


## <a name="resources"></a>Resurser

 - ![Ikon för ämneslänk](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "Ikon för ämneslänk") [Transact-SQL-syntax](https://docs.microsoft.com/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  


## <a name="next-steps"></a>Nästa steg

- [Skapa och hantera elastiska jobb med PowerShell](elastic-jobs-powershell.md)
- [SQL Server för auktorisering och behörigheter](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
