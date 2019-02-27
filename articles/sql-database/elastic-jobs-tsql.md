---
title: Skapa och hantera Azure SQL Elastic Database-jobb med hjälp av Transact-SQL (T-SQL) | Microsoft Docs
description: Köra skript över flera databaser med Elastic Database-jobb agent med hjälp av Transact-SQL (T-SQL).
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 56ad75bc0bed77c2107a49ab339368ab2a63c1fe
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56881268"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Använd Transact-SQL (T-SQL) för att skapa och hantera elastiska Databasjobb

Den här artikeln innehåller många exempelscenarier för att komma igång med elastiska jobb med T-SQL.

I exemplen används den [lagrade procedurer](#job-stored-procedures) och [vyer](#job-views) tillgängliga i den [ *jobbet databasen*](sql-database-job-automation-overview.md#job-database).

Transact-SQL (T-SQL) används för att skapa, konfigurera, köra och hantera jobb. Skapa elastiska jobbagenten stöds inte i T-SQL, så måste du först skapa en *-elastisk jobbagent* med hjälp av portalen eller [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).


## <a name="create-a-credential-for-job-execution"></a>Skapa autentiseringsuppgifter för jobbkörning

Autentiseringsuppgifterna används för att ansluta till dina måldatabaser hamnar för körning av skript. Autentiseringsuppgifterna måste ha rätt behörigheter på de databaser som anges av målgruppen, att köra skriptet har. När du använder en server och/eller pool mål medlem kan rekommenderar vi med hög att du skapar master-autentiseringsuppgifter används för att uppdatera autentiseringsuppgifterna före utökningen av servern och/eller pool vid tidpunkten för jobbkörningen. Databasbegränsade autentiseringsuppgifter skapas på jobbagentdatabas. Samma autentiseringsuppgifter måste användas för att *skapa en inloggning* och *skapa en användare från inloggning att bevilja databasen inloggningsbehörighet* på måldatabaserna.


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

I följande exempel visas hur du kör ett jobb mot alla databaser i en server.  
Ansluta till den [ *jobbet databasen* ](sql-database-job-automation-overview.md#job-database) och kör följande kommando:


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


## <a name="exclude-an-individual-database"></a>Undanta en individuell databas

I följande exempel visas hur du kör ett jobb mot alla databaser i en SQL Database-servern, förutom databasen med namnet *MappingDB*.  
Ansluta till den [ *jobbet databasen* ](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

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

I följande exempel visas hur du alla databaser i en eller flera elastiska pooler.  
Ansluta till den [ *jobbet databasen* ](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

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


## <a name="deploy-new-schema-to-many-databases"></a>Distribuera nya schemat till många databaser

I följande exempel visas hur du distribuerar nya schemat till alla databaser.  
Ansluta till den [ *jobbet databasen* ](sql-database-job-automation-overview.md#job-database) och kör följande kommando:


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


## <a name="data-collection-using-built-in-parameters"></a>Insamling av data med hjälp av inbyggda parametrar

I många scenarier för data-samling, kan det vara praktiskt att innehålla några av dessa skript variabler för att bearbeta efter resultatet av jobbet.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Till exempel om du vill gruppera alla resultat från samma jobbkörningen tillsammans, använda den *$(job_execution_id)* som visas i följande kommando:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Övervaka databasprestanda

I följande exempel skapas ett nytt jobb för att samla in prestandadata från flera databaser.

Som standard söker jobbagenten för att skapa tabell för att lagra de returnerade resultaten i. Därmed måste inloggningen som är associerade med de autentiseringsuppgifter som används för utdata-autentiseringsuppgifter ha tillräcklig behörighet att utföra detta. Om du vill skapa manuellt i tabellen i tid sedan måste den ha följande egenskaper:
1. Kolumner med rätt namn och datatyper för resultatuppsättningen.
2. Ytterligare en kolumn för internal_execution_id med datatypen uniqueidentifier.
3. Icke-grupperat index med namnet ”IX_<TableName>_Internal_Execution_ID” i kolumnen internal_execution_id.

Ansluta till den [ *jobbet databasen* ](sql-database-job-automation-overview.md#job-database) och kör följande kommandon:

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

I följande exempel visar hur du visar aktuella jobbdefinitioner.  
Ansluta till den [ *jobbet databasen* ](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

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


## <a name="begin-ad-hoc-execution-of-a-job"></a>Starta ad hoc-körningen av ett jobb

I följande exempel visar hur du startar ett jobb omedelbart.  
Ansluta till den [ *jobbet databasen* ](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

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


## <a name="schedule-execution-of-a-job"></a>Schemalägga körningen av ett jobb

I följande exempel visas hur du schemalägger ett jobb för framtida körning.  
Ansluta till den [ *jobbet databasen* ](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Övervaka status för körning av jobb

I följande exempel visar hur du visar information om körningen statusen för alla jobb.  
Ansluta till den [ *jobbet databasen* ](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

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

I följande exempel visar hur du avbryter ett jobb.  
Ansluta till den [ *jobbet databasen* ](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

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


## <a name="delete-old-job-history"></a>Ta bort gamla jobbhistorik

I följande exempel visas hur du tar bort jobbhistoriken före ett visst datum.  
Ansluta till den [ *jobbet databasen* ](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Ta bort ett jobb och alla dess jobbhistorik

I följande exempel visas hur du tar bort ett jobb och alla relaterade jobbets historik.  
Ansluta till den [ *jobbet databasen* ](sql-database-job-automation-overview.md#job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>Jobbet lagrade procedurer

Följande lagrade procedurer finns i den [jobb databasen](sql-database-job-automation-overview.md#job-database).



|Lagrad procedur  |Beskrivning  |
|---------|---------|
|[sp_add_job](#spaddjob)     |     Lägger till ett nytt jobb.    |
|[sp_update_job](#spupdatejob)    |      Uppdaterar ett befintligt jobb.   |
|[sp_delete_job](#spdeletejob)     |      Tar bort ett befintligt jobb.   |
|[sp_add_jobstep](#spaddjobstep)    |    Lägger till ett steg i ett jobb.     |
|[sp_update_jobstep](#spupdatejobstep)     |     Uppdaterar ett jobbsteg.    |
|[sp_delete_jobstep](#spdeletejobstep)     |     Tar bort ett jobbsteg.    |
|[sp_start_job](#spstartjob)    |  Startar köra ett jobb.       |
|[sp_stop_job](#spstopjob)     |     Stoppar en jobbkörning.   |
|[sp_add_target_group](#spaddtargetgroup)    |     Lägger till en målgrupp.    |
|[sp_delete_target_group](#spdeletetargetgroup)     |    Tar bort en målgrupp.     |
|[sp_add_target_group_member](#spaddtargetgroupmember)     |    Lägger till en databas eller en grupp med databaser i en målgrupp.     |
|[sp_delete_target_group_member](#spdeletetargetgroupmember)     |     Tar bort en mål medlem från en målgrupp.    |
|[sp_purge_jobhistory](#sppurgejobhistory)    |    Tar bort historikposter för ett jobb.     |





### <a name="spaddjob"></a>sp_add_job

Lägger till ett nytt jobb. 
  
#### <a name="syntax"></a>Syntax  
  

```sql
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

[  **@job_name =** ] 'job_name'  
Namnet på jobbet. Namnet måste vara unikt och får inte innehålla procent (%) tecken. job_name är nvarchar(128) med inget standardvärde.

[  **@description =** ] ”beskrivning”  
Beskrivning av jobbet. Beskrivningen är nvarchar(512) med standardvärdet NULL. Om beskrivningen utelämnas används en tom sträng.

[  **@enabled =** ] aktiverad  
Om den jobbschemat är aktiverat. Aktiverad bitar, med standardvärdet 0 (inaktiverad). Om värdet är 0 jobbet är inte aktiverad och kan inte köras enligt sitt schema; men kan det köras manuellt. Om 1, jobbet ska köras enligt sitt schema och kan även köras manuellt.

[  **@schedule_interval_type =**] schedule_interval_type  
Värdet anger när jobbet ska köras. schedule_interval_type är nvarchar(50) med ett standardvärde på en gång, och kan vara något av följande värden:
- ”En gång”,
- Minuter,
- Timmar,
- Dagar,
- Veckor,
- ”Månader”

[  **@schedule_interval_count =** ] schedule_interval_count  
Antalet schedule_interval_count perioder ska ske mellan varje körning av jobbet. schedule_interval_count är int, med standardvärdet 1. Värdet måste vara större än eller lika med 1.

[  **@schedule_start_time =** ] schedule_start_time  
Datum i vilket projekt körning kan börja. schedule_start_time är DATETIME2, med 0001-01-01 00:00:00.0000000 standardinställningen.

[  **@schedule_end_time =** ] schedule_end_time  
Datum i vilket projekt körning kan stoppa. schedule_end_time är DATETIME2, med standardinställningen 9999-12-31 11:59:59.0000000. 

[ **@job_id =** ] job_id OUTPUT  
Jobbet ID-nummer för jobbet om har skapats. job_id är en utdata-variabel av typen uniqueidentifier.

#### <a name="return-code-values"></a>Returkod för värden

0 (lyckades) eller 1 (misslyckades)

#### <a name="remarks"></a>Kommentarer
sp_add_job måste köras från jobbagentdatabas som anges när du skapar jobbagenten.
När sp_add_job har körts för att lägga till ett jobb, användas sp_add_jobstep för att lägga till steg som utför aktiviteterna för jobbet. Jobbets första versionsnumret är 0, vilket ökas till 1 när du lägger till det första steget.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. Du kan de hindra en användare om du bara vill övervaka jobb för att bevilja användaren som en del av rollen följande i jobbagentdatabas som anges när du skapar jobbagenten:

- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.

### <a name="spupdatejob"></a>sp_update_job

Uppdaterar ett befintligt jobb.

#### <a name="syntax"></a>Syntax

```sql
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
[  **@job_name =** ] 'job_name'  
Namnet på jobbet som ska uppdateras. job_name är nvarchar(128).

[  **@new_name =** ] 'nytt_namn'  
Det nya namnet på jobbet. new_name is nvarchar(128).

[  **@description =** ] ”beskrivning”  
Beskrivning av jobbet. Beskrivningen är nvarchar(512).

[  **@enabled =** ] aktiverad  
Anger om den jobbschemat är aktiverat (1) eller inte har aktiverats (0). Aktiverad är bit.

[  **@schedule_interval_type=** ] schedule_interval_type  
Värdet anger när jobbet ska köras. schedule_interval_type är nvarchar(50) och kan vara något av följande värden:

- ”En gång”,
- Minuter,
- Timmar,
- Dagar,
- Veckor,
- ”Månader”

[  **@schedule_interval_count=** ] schedule_interval_count  
Antalet schedule_interval_count perioder ska ske mellan varje körning av jobbet. schedule_interval_count är int, med standardvärdet 1. Värdet måste vara större än eller lika med 1.

[  **@schedule_start_time=** ] schedule_start_time  
Datum i vilket projekt körning kan börja. schedule_start_time är DATETIME2, med 0001-01-01 00:00:00.0000000 standardinställningen.

[  **@schedule_end_time=** ] schedule_end_time  
Datum i vilket projekt körning kan stoppa. schedule_end_time är DATETIME2, med standardinställningen 9999-12-31 11:59:59.0000000. 

#### <a name="return-code-values"></a>Returkod för värden
0 (lyckades) eller 1 (misslyckades)

#### <a name="remarks"></a>Kommentarer
När sp_add_job har körts för att lägga till ett jobb, användas sp_add_jobstep för att lägga till steg som utför aktiviteterna för jobbet. Jobbets första versionsnumret är 0, vilket ökas till 1 när du lägger till det första steget.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. Du kan de hindra en användare om du bara vill övervaka jobb för att bevilja användaren som en del av rollen följande i jobbagentdatabas som anges när du skapar jobbagenten:
- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.



### <a name="spdeletejob"></a>sp_delete_job

Tar bort ett befintligt jobb.

#### <a name="syntax"></a>Syntax

```sql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argument
[  **@job_name =** ] 'job_name'  
Namnet på jobbet som ska tas bort. job_name är nvarchar(128).

[  **@force =** ] tvinga  
Anger om du vill ta bort om jobbet har alla körningar pågår och Avbryt alla pågående körningar (1) eller misslyckas om någon jobbkörningar pågår (0). Framtvinga bit.

#### <a name="return-code-values"></a>Returkod för värden
0 (lyckades) eller 1 (misslyckades)

#### <a name="remarks"></a>Kommentarer
Jobbhistorik tas bort automatiskt när ett jobb har tagits bort.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. Du kan de hindra en användare om du bara vill övervaka jobb för att bevilja användaren som en del av rollen följande i jobbagentdatabas som anges när du skapar jobbagenten:
- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.



### <a name="spaddjobstep"></a>sp_add_jobstep

Lägger till ett steg i ett jobb.

#### <a name="syntax"></a>Syntax


```sql
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

[  **@job_name =** ] 'job_name'  
Namnet på jobbet du vill lägga till steget. job_name är nvarchar(128).

[  **@step_id =** ] step_id  
Identifiering sekvensnumret för jobbsteget. Steg identifikationsnummer börjar vid 1 och öka utan luckor. Om ett befintligt steg redan har detta id, är sedan att steget och alla efterföljande steg som har sitt id stegvis så att den här nya steg kan infogas i sekvensen. Om inte anges tilldelas i step_id automatiskt till den senaste i sekvens med steg. step_id är ett heltal.

[  **@step_name =** ] step_name  
Namnet på steget. Du måste ange, utom det första steget i ett jobb som har ett standardnamn för ”JobStep” (för att underlätta). step_name är nvarchar(128).

[  **@command_type =** ] 'command_type'  
Typ av kommandot som körs av den här jobstep. command_type är nvarchar(50) med ett standardvärde på TSql, vilket innebär att värdet för den @command_type parameter är ett T-SQL-skript.

Om anges måste värdet vara TSql.

[ **@command_source =** ] 'command_source'  
Typ av plats för lagringen av kommandot. command_source är nvarchar(50) med ett standardvärde för intern, vilket innebär att värdet för den @command_source parametern är literal texten för kommandot.

Om anges måste värdet vara infogad.

[  **@command =** ] 'kommandot'  
Kommandot måste vara giltig T-SQL-skript och körs sedan av det här jobbsteget. kommandot är nvarchar(max) med standardvärdet NULL.

[  **@credential_name =** ] 'credential_name'  
Namnet på databasen-omfattande autentisering i det här jobbet kontrolldatabas som används för att ansluta till alla måldatabaserna i målgruppen när det här steget körs. credential_name är nvarchar(128).

[  **@target_group_name =** ] ”mål-gruppnamn.  
Namnet på den målgrupp som innehåller måldatabaserna som jobbsteget körs på. target_group_name is nvarchar(128).

[  **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Fördröjning innan det första återförsöket försöker om jobbsteget misslyckas vid första körningen försöket. initial_retry_interval_seconds är int, med standardvärdet 1.

[  **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Max. fördröjning mellan försöken. Om fördröjningen mellan återförsök skulle bli större än det här värdet, är det begränsat till det här värdet i stället. maximum_retry_interval_seconds är int, med standardvärdet på 120.

[  **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Multiplikatorn som ska gälla för försök fördröjningen om flera jobb stegvis körning inloggningsförsök misslyckas. Till exempel om det första återförsöket hade en fördröjning på 5 sekund och backoff multiplikatorn är 2.0, sedan andra återförsök har en fördröjning på 10 sekunder och det tredje återförsöket har en fördröjning på 20 sekunder. retry_interval_backoff_multiplier är verkliga med standardvärdet 2.0.

[  **@retry_attempts =** ] retry_attempts  
Antal nya försök körningen om det första försöket misslyckas. Till exempel om retry_attempts-värdet är 10, så det kommer att vara 1 första återförsök försöket och 10 antal, vilket ger totalt 11 försök. Om den slutliga återförsök misslyckas avslutas jobbkörningen med livscykel misslyckades. retry_attempts är int, med standardvärdet 10.

[  **@step_timeout_seconds =** ] step_timeout_seconds  
Längsta tid som tillåts för steget för att köra. Om den här gången har överskridits avslutas jobbkörningen med en livscykeln för nådde sin tidsgräns. step_timeout_seconds är int, med standardvärdet 43 200 sekunder (12 timmar).

[  **@output_type =** ] 'output_type'  
Om inte värdet null, vilken typ av destination kommandots första resultatuppsättningens skrivs till. output_type är nvarchar(50) med standardvärdet NULL.

Om anges måste värdet vara SqlDatabase.

[ **@output_credential_name =** ] 'output_credential_name'  
Om inte värdet null, namnet på databasen-omfattande autentisering som används för att ansluta till måldatabasen utdata. Du måste ange om output_type är lika med SqlDatabase. output_credential_name är nvarchar(128) med standardvärdet NULL.

[ **@output_subscription_id =** ] 'output_subscription_id'  
Måste beskrivning.

[ **@output_resource_group_name =** ] 'output_resource_group_name'  
Måste beskrivning.

[ **@output_server_name =** ] 'output_server_name'  
Om inte null, fullständigt kvalificerade DNS-namnet på den server som innehåller utdata-måldatabasen. Du måste ange om output_type är lika med SqlDatabase. output_server_name är nvarchar(256) med standardvärdet NULL.

[ **@output_database_name =** ] 'output_database_name'  
Om inte null, namnet på databasen innehåller som utdata måltabellen. Du måste ange om output_type är lika med SqlDatabase. output_database_name är nvarchar(128) med standardvärdet NULL.

[ **@output_schema_name =** ] 'output_schema_name'  
Om inte null, namnet på SQL-schemat innehåller som utdata måltabellen. Om output_type är lika med SqlDatabase, är standardvärdet dbo. output_schema_name is nvarchar(128).

[  **@output_table_name =** ] 'output_table_name'  
Om inte värdet null, namnet på den tabell som kommandots första resultatuppsättningens ska skrivas till. Om tabellen inte redan finns, kommer den att skapas baserat på schemat för att returnera resultatuppsättningen. Du måste ange om output_type är lika med SqlDatabase. output_table_name är nvarchar(128) med standardvärdet NULL.

[ **@job_version =** ] job_version OUTPUT  
Output-parameter som ska tilldelas det nya versionsnumret för jobbet. job_version is int.

[  **@max_parallelism =** ] max_parallelism utdata  
Den maximala nivån för parallellitet per elastisk pool. Om anges kommer jobbsteget kommer att vara begränsad till endast köras på högst som många databaser per elastisk pool. Detta gäller för varje elastisk pool som ingår direkt i målgruppen eller som finns i en server som ingår i målgruppen. max_parallelism är heltal.


#### <a name="return-code-values"></a>Returkod för värden
0 (lyckades) eller 1 (misslyckades)

#### <a name="remarks"></a>Kommentarer
När sp_add_jobstep lyckas stegvis jobbets aktuella versionsnumret. Jobbet körs nästa gång kommer den nya versionen att användas. Om jobbet körs för tillfället, innehåller inte den körningen nytt steg.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. Du kan de hindra en användare om du bara vill övervaka jobb för att bevilja användaren som en del av rollen följande i jobbagentdatabas som anges när du skapar jobbagenten:  

- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.



### <a name="spupdatejobstep"></a>sp_update_jobstep

Uppdaterar ett jobbsteg.

#### <a name="syntax"></a>Syntax

```sql
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
[  **@job_name =** ] 'job_name'  
Namnet på jobbet som tillhör steget. job_name är nvarchar(128).

[  **@step_id =** ] step_id  
ID-nummer för steget som ska ändras. Step_id eller step_name måste anges. step_id är ett heltal.

[  **@step_name =** ] 'step_name'  
Namnet på steget som ska ändras. Step_id eller step_name måste anges. step_name är nvarchar(128).

[ **@new_id =** ] new_id  
Ny sekvens ID-numret för jobbsteget. Steg identifikationsnummer börjar vid 1 och öka utan luckor. Om ett steg om, sedan anpassas andra steg automatiskt.

[  **@new_name =** ] 'nytt_namn'  
Det nya namnet på steget. new_name is nvarchar(128).

[  **@command_type =** ] 'command_type'  
Typ av kommandot som körs av den här jobstep. command_type är nvarchar(50) med ett standardvärde på TSql, vilket innebär att värdet för den @command_type parameter är ett T-SQL-skript.

Om anges måste värdet vara TSql.

[ **@command_source =** ] 'command_source'  
Typ av plats för lagringen av kommandot. command_source är nvarchar(50) med ett standardvärde för intern, vilket innebär att värdet för den @command_source parametern är literal texten för kommandot.

Om anges måste värdet vara infogad.

[  **@command =** ] 'kommandot'  
I kommandot måste vara giltig T-SQL-skript och körs sedan av det här jobbsteget. kommandot är nvarchar(max) med standardvärdet NULL.

[  **@credential_name =** ] 'credential_name'  
Namnet på databasen-omfattande autentisering i det här jobbet kontrolldatabas som används för att ansluta till alla måldatabaserna i målgruppen när det här steget körs. credential_name är nvarchar(128).

[  **@target_group_name =** ] ”mål-gruppnamn.  
Namnet på den målgrupp som innehåller måldatabaserna som jobbsteget körs på. target_group_name is nvarchar(128).

[  **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Fördröjning innan det första återförsöket försöker om jobbsteget misslyckas vid första körningen försöket. initial_retry_interval_seconds är int, med standardvärdet 1.

[  **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Max. fördröjning mellan försöken. Om fördröjningen mellan återförsök skulle bli större än det här värdet, är det begränsat till det här värdet i stället. maximum_retry_interval_seconds är int, med standardvärdet på 120.

[  **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Multiplikatorn som ska gälla för försök fördröjningen om flera jobb stegvis körning inloggningsförsök misslyckas. Till exempel om det första återförsöket hade en fördröjning på 5 sekund och backoff multiplikatorn är 2.0, sedan andra återförsök har en fördröjning på 10 sekunder och det tredje återförsöket har en fördröjning på 20 sekunder. retry_interval_backoff_multiplier är verkliga med standardvärdet 2.0.

[  **@retry_attempts =** ] retry_attempts  
Antal nya försök körningen om det första försöket misslyckas. Till exempel om retry_attempts-värdet är 10, så det kommer att vara 1 första återförsök försöket och 10 antal, vilket ger totalt 11 försök. Om den slutliga återförsök misslyckas avslutas jobbkörningen med livscykel misslyckades. retry_attempts är int, med standardvärdet 10.

[  **@step_timeout_seconds =** ] step_timeout_seconds  
Längsta tid som tillåts för steget för att köra. Om den här gången har överskridits avslutas jobbkörningen med en livscykeln för nådde sin tidsgräns. step_timeout_seconds är int, med standardvärdet 43 200 sekunder (12 timmar).

[  **@output_type =** ] 'output_type'  
Om inte värdet null, vilken typ av destination kommandots första resultatuppsättningens skrivs till. Om du vill återställa värdet för output_type till NULL, värdet för den här parametern anges till ”(tom sträng). output_type är nvarchar(50) med standardvärdet NULL.

Om anges måste värdet vara SqlDatabase.

[ **@output_credential_name =** ] 'output_credential_name'  
Om inte värdet null, namnet på databasen-omfattande autentisering som används för att ansluta till måldatabasen utdata. Du måste ange om output_type är lika med SqlDatabase. Om du vill återställa värdet för output_credential_name till NULL, värdet för den här parametern anges till ”(tom sträng). output_credential_name är nvarchar(128) med standardvärdet NULL.

[ **@output_server_name =** ] 'output_server_name'  
Om inte null, fullständigt kvalificerade DNS-namnet på den server som innehåller utdata-måldatabasen. Du måste ange om output_type är lika med SqlDatabase. Om du vill återställa värdet för output_server_name till NULL, värdet för den här parametern anges till ”(tom sträng). output_server_name är nvarchar(256) med standardvärdet NULL.

[ **@output_database_name =** ] 'output_database_name'  
Om inte null, namnet på databasen innehåller som utdata måltabellen. Du måste ange om output_type är lika med SqlDatabase. Om du vill återställa värdet för output_database_name till NULL, värdet för den här parametern anges till ”(tom sträng). output_database_name är nvarchar(128) med standardvärdet NULL.

[ **@output_schema_name =** ] 'output_schema_name'  
Om inte null, namnet på SQL-schemat innehåller som utdata måltabellen. Om output_type är lika med SqlDatabase, är standardvärdet dbo. Om du vill återställa värdet för output_schema_name till NULL, värdet för den här parametern anges till ”(tom sträng). output_schema_name is nvarchar(128).

[  **@output_table_name =** ] 'output_table_name'  
Om inte värdet null, namnet på den tabell som kommandots första resultatuppsättningens ska skrivas till. Om tabellen inte redan finns, kommer den att skapas baserat på schemat för att returnera resultatuppsättningen. Du måste ange om output_type är lika med SqlDatabase. Om du vill återställa värdet för output_server_name till NULL, värdet för den här parametern anges till ”(tom sträng). output_table_name är nvarchar(128) med standardvärdet NULL.

[ **@job_version =** ] job_version OUTPUT  
Output-parameter som ska tilldelas det nya versionsnumret för jobbet. job_version is int.

[  **@max_parallelism =** ] max_parallelism utdata  
Den maximala nivån för parallellitet per elastisk pool. Om anges kommer jobbsteget kommer att vara begränsad till endast köras på högst som många databaser per elastisk pool. Detta gäller för varje elastisk pool som ingår direkt i målgruppen eller som finns i en server som ingår i målgruppen. Om du vill återställa värdet för max_parallelism till null, anger du värdet för den här parametern till-1. max_parallelism är heltal.


#### <a name="return-code-values"></a>Returkod för värden
0 (lyckades) eller 1 (misslyckades)

#### <a name="remarks"></a>Kommentarer
Alla pågående körningar av jobbet påverkas inte. När sp_update_jobstep lyckas stegvis versionsnumret för jobbets. Jobbet körs nästa gång kommer den nya versionen att användas.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. Du kan de hindra en användare om du bara vill övervaka jobb för att bevilja användaren som en del av rollen följande i jobbagentdatabas som anges när du skapar jobbagenten:

- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare




### <a name="spdeletejobstep"></a>sp_delete_jobstep

Tar bort ett steg från ett jobb.

#### <a name="syntax"></a>Syntax


```sql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argument
[  **@job_name =** ] 'job_name'  
Namnet på jobbet från vilken steget tas bort. job_name är nvarchar(128) med inget standardvärde.

[  **@step_id =** ] step_id  
ID-nummer för steget som ska tas bort. Step_id eller step_name måste anges. step_id är ett heltal.

[  **@step_name =** ] 'step_name'  
Namnet på steget som ska tas bort. Step_id eller step_name måste anges. step_name är nvarchar(128).

[ **@job_version =** ] job_version OUTPUT  
Output-parameter som ska tilldelas det nya versionsnumret för jobbet. job_version is int.

#### <a name="return-code-values"></a>Returkod för värden
0 (lyckades) eller 1 (misslyckades)

#### <a name="remarks"></a>Kommentarer
Alla pågående körningar av jobbet påverkas inte. När sp_update_jobstep lyckas stegvis versionsnumret för jobbets. Jobbet körs nästa gång kommer den nya versionen att användas.

Andra jobb steg numreras automatiskt så att den fyller luckan efter steget Borttaget jobb.
 
#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. Du kan de hindra en användare om du bara vill övervaka jobb för att bevilja användaren som en del av rollen följande i jobbagentdatabas som anges när du skapar jobbagenten:
- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.






### <a name="spstartjob"></a>sp_start_job

Startar köra ett jobb.

#### <a name="syntax"></a>Syntax


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argument
[  **@job_name =** ] 'job_name'  
Namnet på jobbet från vilken steget tas bort. job_name är nvarchar(128) med inget standardvärde.

[  **@job_execution_id =** ] job_execution_id utdata  
Utdataparameter som ska tilldelas den jobbkörning id. job_version är uniqueidentifier.

#### <a name="return-code-values"></a>Returkod för värden
0 (lyckades) eller 1 (misslyckades)

#### <a name="remarks"></a>Kommentarer
Ingen.
 
#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. Du kan de hindra en användare om du bara vill övervaka jobb för att bevilja användaren som en del av rollen följande i jobbagentdatabas som anges när du skapar jobbagenten:
- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.

### <a name="spstopjob"></a>sp_stop_job

Stoppar en jobbkörning.

#### <a name="syntax"></a>Syntax


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argument
[  **@job_execution_id =** ] job_execution_id  
ID-nummer för jobbkörningen att stoppa. job_execution_id är uniqueidentifier med standardvärdet NULL.

#### <a name="return-code-values"></a>Returkod för värden
0 (lyckades) eller 1 (misslyckades)

#### <a name="remarks"></a>Kommentarer
Ingen.
 
#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. Du kan de hindra en användare om du bara vill övervaka jobb för att bevilja användaren som en del av rollen följande i jobbagentdatabas som anges när du skapar jobbagenten:
- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.


### <a name="spaddtargetgroup"></a>sp_add_target_group

Lägger till en målgrupp.

#### <a name="syntax"></a>Syntax


```sql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Argument
[  **@target_group_name =** ] 'target_group_name'  
Namnet på målgruppen du skapar. target_group_name är nvarchar(128) med inget standardvärde.

[  **@target_group_id =** ] target_group_id utdata målet gruppen ID-nummer som tilldelas jobbet har skapats. target_group_id är en utdata-variabel av typen uniqueidentifier med standardvärdet NULL.

#### <a name="return-code-values"></a>Returkod för värden
0 (lyckades) eller 1 (misslyckades)

#### <a name="remarks"></a>Kommentarer
Målgrupper ger ett enkelt sätt att fokusera på ett jobb på en uppsättning databaser.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. Du kan de hindra en användare om du bara vill övervaka jobb för att bevilja användaren som en del av rollen följande i jobbagentdatabas som anges när du skapar jobbagenten:
- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.

### <a name="spdeletetargetgroup"></a>sp_delete_target_group

Tar bort en målgrupp.

#### <a name="syntax"></a>Syntax


```sql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Argument
[  **@target_group_name =** ] 'target_group_name'  
Namnet på målgruppen du ta bort. target_group_name är nvarchar(128) med inget standardvärde.

#### <a name="return-code-values"></a>Returkod för värden
0 (lyckades) eller 1 (misslyckades)

#### <a name="remarks"></a>Kommentarer
Ingen.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. Du kan de hindra en användare om du bara vill övervaka jobb för att bevilja användaren som en del av rollen följande i jobbagentdatabas som anges när du skapar jobbagenten:
- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.

### <a name="spaddtargetgroupmember"></a>sp_add_target_group_member

Lägger till en databas eller en grupp med databaser i en målgrupp.

#### <a name="syntax"></a>Syntax

```sql
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
[  **@target_group_name =** ] 'target_group_name'  
Namnet på den målgrupp som medlemmen kommer att läggas till. target_group_name är nvarchar(128) med inget standardvärde.

[  **@membership_type =** ] 'membership_type'  
Anger om gruppmedlem mål ska inkluderas eller uteslutas. target_group_name är nvarchar(128) med standardvärdet ”inkludera”. Giltiga värden för target_group_name är ”inkludera” eller ”undanta'.

[  **@target_type =** ] 'target_type'  
Typ av måldatabasen eller samling databaser, till exempel alla databaser i en server, alla databaser i en elastisk pool, alla databaser i en skärvkarta eller en individuell databas. target_type är nvarchar(128) med inget standardvärde. Giltiga värden för target_type är ”SqlServer', 'SqlElasticPool”, ”SqlDatabase” eller ”SqlShardMap”. 

[  **@refresh_credential_name =** ] 'refresh_credential_name'  
Namnet på SQL Database-servern. refresh_credential_name är nvarchar(128) med inget standardvärde.

[  **@server_name =** ] 'servernamn'  
Namnet på den SQL-databasserver som ska läggas till den angivna målgruppen. servernamn måste anges när target_type är ”SqlServer'. Servernamn är nvarchar(128) med inget standardvärde.

[  **@database_name =** ] 'database_name'  
Namnet på databasen som ska läggas till den angivna målgruppen. database_name bör anges när target_type är ”SqlDatabase”. database_name är nvarchar(128) med inget standardvärde.

[  **@elastic_pool_name =** ] 'elastic_pool_name'  
Namnet på den elastiska poolen som ska läggas till den angivna målgruppen. elastic_pool_name ska anges när target_type är 'SqlElasticPool'. elastic_pool_name är nvarchar(128) med inget standardvärde.

[  **@shard_map_name =** ] 'shard_map_name'  
Namnet på poolen fragment karta som ska läggas till den angivna målgruppen. elastic_pool_name ska anges när target_type är 'SqlSqlShardMap'. shard_map_name är nvarchar(128) med inget standardvärde.

[  **@target_id =** ] target_group_id utdata  
Mål-ID-nummer till gruppmedlem mål om skapat läggs till i målgruppen. target_id är en utdata-variabel av typen uniqueidentifier med standardvärdet NULL.
Returnera kodvärden 0 (lyckades) eller 1 (misslyckades)

#### <a name="remarks"></a>Kommentarer
Ett jobb körs på alla enskilda databaser i en SQL Database-server eller i en elastisk pool vid tidpunkten för körning när en SQL Database-server eller elastisk pool ingår i målgruppen.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. Du kan de hindra en användare om du bara vill övervaka jobb för att bevilja användaren som en del av rollen följande i jobbagentdatabas som anges när du skapar jobbagenten:
- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.

#### <a name="examples"></a>Exempel
I följande exempel läggs alla databaser i London och NewYork servrar i gruppen servrar underhålla kundinformation. Du måste ansluta till databasen för jobb som anges när du skapar jobbagenten i det här fallet ElasticJobs.

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

### <a name="spdeletetargetgroupmember"></a>sp_delete_target_group_member

Tar bort en mål medlem från en målgrupp.

#### <a name="syntax"></a>Syntax


```sql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] 'target_id']
```



Argument [ @target_group_name =] 'target_group_name'  
Namnet på målgruppen som ska ta bort gruppmedlem mål. target_group_name är nvarchar(128) med inget standardvärde.

[ @target_id =] target_id  
 Mål-ID-nummer till gruppmedlem mål som ska tas bort. target_id är en uniqueidentifier med standardvärdet NULL.

#### <a name="return-code-values"></a>Returkod för värden
0 (lyckades) eller 1 (misslyckades)

#### <a name="remarks"></a>Kommentarer
Målgrupper ger ett enkelt sätt att fokusera på ett jobb på en uppsättning databaser.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. Du kan de hindra en användare om du bara vill övervaka jobb för att bevilja användaren som en del av rollen följande i jobbagentdatabas som anges när du skapar jobbagenten:
- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.

#### <a name="examples"></a>Exempel
I följande exempel tar bort London-server från gruppen servrar underhålla kundinformation. Du måste ansluta till databasen för jobb som anges när du skapar jobbagenten i det här fallet ElasticJobs.

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

### <a name="sppurgejobhistory"></a>sp_purge_jobhistory

Tar bort historikposter för ett jobb.

#### <a name="syntax"></a>Syntax


```sql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argument
[  **@job_name =** ] 'job_name'  
Namnet på jobbet som du vill ta bort historikposter. job_name är nvarchar(128) med standardvärdet NULL. Du måste ange job_id eller job_name, men båda kan inte anges.

[ **@job_id =** ] job_id  
 Det ID-nummer för jobbet för jobbet för poster som ska tas bort. job_id är uniqueidentifier med standardvärdet NULL. Du måste ange job_id eller job_name, men båda kan inte anges.

[  **@oldest_date =** ] oldest_date  
 Äldsta posten ska sparas i historiken. oldest_date är DATETIME2, med standardvärdet NULL. När du anger oldest_date sp_purge_jobhistory bara tar bort poster som är äldre än det angivna värdet.

#### <a name="return-code-values"></a>Returkod för värden
0 (lyckades) eller 1 (misslyckades) Anmärkning-målgrupper ger ett enkelt sätt att fokusera på ett jobb på en uppsättning databaser.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den här lagrade proceduren. Du kan de hindra en användare om du bara vill övervaka jobb för att bevilja användaren som en del av rollen följande i jobbagentdatabas som anges när du skapar jobbagenten:
- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.

#### <a name="examples"></a>Exempel
I följande exempel läggs alla databaser i London och NewYork servrar i gruppen servrar underhålla kundinformation. Du måste ansluta till databasen för jobb som anges när du skapar jobbagenten i det här fallet ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Jobbet vyer

Följande vyer som är tillgängliga i den [jobb databasen](sql-database-job-automation-overview.md#job-database).


|Visa  |Beskrivning  |
|---------|---------|
|[jobs_executions](#jobsexecutions-view)     |  Visar jobbhistorik körning.      |
|[Jobb](#jobs-view)     |   Visar alla jobb.      |
|[job_versions](#jobversions-view)     |   Visar alla jobbversioner.      |
|[jobbsteg](#jobsteps-view)     |     Visar alla steg i den aktuella versionen av varje jobb.    |
|[jobstep_versions](#jobstepversions-view)     |     Visar alla steg i alla versioner av varje jobb.    |
|[target_groups](#targetgroups-view)     |      Visar alla målgrupper.   |
|[target_group_members](#targetgroups-view)     |   Visar alla medlemmar i alla målgrupper.      |


### <a name="jobsexecutions-view"></a>Visa jobs_executions

[jobs].[jobs_executions]

Visar jobbhistorik körning.


|Kolumnnamn|   Datatyp   |Beskrivning|
|---------|---------|---------|
|**job_execution_id**   |uniqueidentifier|  Unikt ID för en instans av en jobbkörning.
|**job_name**   |nvarchar(128)  |Namnet på jobbet.
|**job_id** |uniqueidentifier|  Unikt ID för jobbet.
|**job_version**    |int    |Version av jobbet (uppdateras automatiskt varje gång som jobbet har ändrats).
|**step_id**    |int|   Unika (för jobbets) identifieraren för steget. NULL anger det här är den överordnade jobbkörningen.
|**is_active**| bitars |Anger om informationen är aktiv eller inaktiv. 1 anger aktiva jobb och 0 indikerar inaktiva.
|**livscykel**| nvarchar(50)|Värde som anger status för jobbet: ”skapa”, ”pågår”, ”misslyckad”, ”lyckades”, ”överhoppade', 'SucceededWithSkipped'|
|**create_time**|   datetime2(7)|   Datum och tid då jobbet skapades.
|**start_time** |datetime2(7)|  Datum och tid som jobbet startade körningen. NULL om jobbet inte har körts ännu.
|**end_time**|  datetime2(7)    |Datum och tid som jobbet slutförts körning. NULL om jobbet inte har ännu körts eller har inte slutförts ännu körning.
|**current_attempts**   |int    |Antal gånger som steget återförsöktes. Överordnade jobb ska vara 0, underordnade jobbkörningar ska vara 1 eller större baserat på körningsprincipen.
|**current_attempt_start_time** |datetime2(7)|  Datum och tid som jobbet startade körningen. NULL anger det här är den överordnade jobbkörningen.
|**last_message**   |nvarchar(max)| Jobbet eller steg historik-meddelande. 
|**target_type**|   nvarchar(128)   |Typ av måldatabasen eller samling databaser, till exempel alla databaser i en server, alla databaser i en elastisk pool eller en databas. Giltiga värden för target_type är ”SqlServer', 'SqlElasticPool” eller ”SqlDatabase”. NULL anger det här är den överordnade jobbkörningen.
|**target_id**  |uniqueidentifier|  Unikt ID för mål-gruppmedlem.  NULL anger det här är den överordnade jobbkörningen.
|**target_group_name**  |nvarchar(128)  |Namnet på målgruppen. NULL anger det här är den överordnade jobbkörningen.
|**target_server_name**|    nvarchar(256)|  Namnet på SQL Database-server som ingår i målgruppen. Bara anges om target_type är ”SqlServer'. NULL anger det här är den överordnade jobbkörningen.
|**target_database_name**   |nvarchar(128)| Namnet på databasen i målgruppen. Ange bara när target_type är ”SqlDatabase”. NULL anger det här är den överordnade jobbkörningen.


### <a name="jobs-view"></a>Visa jobb

[jobb]. [jobb]

Visar alla jobb.

|Kolumnnamn|   Datatyp|  Beskrivning|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Namnet på jobbet.|
|**job_id**|    uniqueidentifier    |Unikt ID för jobbet.|
|**job_version**    |int    |Version av jobbet (uppdateras automatiskt varje gång som jobbet har ändrats).|
|**Beskrivning**    |nvarchar(512)| Beskrivning för jobbet. aktiverade biten anger om jobbet är aktiverat eller inaktiverat. 1 anger aktiverade jobb och 0 indikerar inaktiverade jobb.|
|**schedule_interval_type** |nvarchar(50)   |Värde som anger när jobbet ska köras: ”en gång”, ”minuter”, ”timmar”, ”dagar”, ”veckor”, ”månader”
|**schedule_interval_count**|   int|    Antalet schedule_interval_type perioder ska ske mellan varje körning av jobbet.|
|**schedule_start_time**    |datetime2(7)|  Datum och tid som jobbet har senaste startade körningen.|
|**schedule_end_time**| datetime2(7)|   Datum och tid som jobbet har senaste har slutförts.|


### <a name="jobversions-view"></a>job_versions view

[jobs].[job_versions]

Visar alla jobbversioner.

|Kolumnnamn|   Datatyp|  Beskrivning|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Namnet på jobbet.|
|**job_id**|    uniqueidentifier    |Unikt ID för jobbet.|
|**job_version**    |int    |Version av jobbet (uppdateras automatiskt varje gång som jobbet har ändrats).|


### <a name="jobsteps-view"></a>Visa jobbsteg

[jobb]. [jobbsteg]

Visar alla steg i den aktuella versionen av varje jobb.

|Kolumnnamn    |Datatyp| Beskrivning|
|------|------|-------|
|**job_name**   |nvarchar(128)| Namnet på jobbet.|
|**job_id** |uniqueidentifier   |Unikt ID för jobbet.|
|**job_version**|   int|    Version av jobbet (uppdateras automatiskt varje gång som jobbet har ändrats).|
|**step_id**    |int    |Unika (för jobbets) identifieraren för steget.|
|**step_name**  |nvarchar(128)  |(För jobbets) namnet för steget.|
|**command_type**   |nvarchar(50)   |Typ av kommando som ska köras i jobbsteget. För v1, värdet måste vara lika med till och standardvärdet är 'TSql'.|
|**command_source** |nvarchar(50)|  Platsen för kommandot. För v1 ”infogat-är standard och endast godkända värde.|
|**Kommandot**|   nvarchar(max)|  Kommandon som ska köras med elastiska jobb via command_type.|
|**credential_name**|   nvarchar(128)   |Namnet på databasbegränsade autentiseringsuppgifter används för att köra jobbet.|
|**target_group_name**| nvarchar(128)   |Namnet på målgruppen.|
|**target_group_id**|   uniqueidentifier|   Unikt ID för målgruppen.|
|**initial_retry_interval_seconds**|    int |Fördröjning före den första återförsök. Standardvärdet är 1.|
|**maximum_retry_interval_seconds** |int|   Max. fördröjning mellan försöken. Om fördröjningen mellan återförsök skulle bli större än det här värdet, är det begränsat till det här värdet i stället. Standardvärdet är 120.|
|**retry_interval_backoff_multiplier**  |verkliga|  Multiplikatorn som ska gälla för försök fördröjningen om flera jobb stegvis körning inloggningsförsök misslyckas. Standardvärdet är 2.0.|
|**retry_attempts** |int|   Antal försök försöker använda om det här steget misslyckas. Standardvärdet 10, vilket betyder att inga nya försök.|
|**step_timeout_seconds**   |int|   Hur lång tid i minuter mellan nya försök. Standardvärdet är 0, vilket anger en 0-minutersintervall.|
|**output_type**    |nvarchar(11)|  Platsen för kommandot. I den aktuella förhandsversionen 'infogat-är standard och endast godkända värde.|
|**output_credential_name**|    nvarchar(128)   |Ange namnet på autentiseringsuppgifterna som ska användas för att ansluta till målservern resultaten ska sparas.|
|**output_subscription_id**|    uniqueidentifier|   Unikt ID för prenumerationen för mål-server\database för resultatet från frågan.|
|**output_resource_group_name** |nvarchar(128)| Resursgruppens namn där målservern finns.|
|**output_server_name**|    nvarchar(256)   |Namnet på målservern för resultatuppsättningen.|
|**output_database_name**   |nvarchar(128)| Namnet på måldatabasen för resultatuppsättningen.|
|**output_schema_name** |nvarchar(max)| Namnet på målschema. Standardvärdet är dbo, om inte anges.|
|**output_table_name**| nvarchar(max)|  Namnet på tabellen för att lagra resultatet från resultatet av frågan. Tabellen skapas automatiskt baserat på schemat för resultat om den inte redan finns. Schemat måste matcha schemat för resultatuppsättningen.|
|**max_parallelism**|   int|    Det maximala antalet databaser per elastisk pool som jobbsteget ska köras i taget. Standardvärdet är NULL, vilket innebär att ingen gräns. |


### <a name="jobstepversions-view"></a>Visa jobstep_versions

[jobs].[jobstep_versions]

Visar alla steg i alla versioner av varje jobb. Schemat är identiska med [jobbsteg](#jobsteps-view).

### <a name="targetgroups-view"></a>Visa target_groups

[jobs].[target_groups]

Visar en lista över alla målgrupper.

|Kolumnnamn|Datatyp| Beskrivning|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |Namnet på målgruppen, en uppsättning databaser. 
|**target_group_id**    |uniqueidentifier   |Unikt ID för målgruppen.

### <a name="targetgroupsmembers-view"></a>Visa target_groups_members

[jobs].[target_groups_members]

Visar alla medlemmar i alla målgrupper.

|Kolumnnamn|Datatyp| Beskrivning|
|-----|-----|-----|
|**target_group_name**  |nvarchar (128|Namnet på målgruppen, en uppsättning databaser. |
|**target_group_id**    |uniqueidentifier   |Unikt ID för målgruppen.|
|**membership_type**    |int|   Anger om mål-gruppmedlem inkluderas eller exkluderas i målgruppen. Giltiga värden för target_group_name är ”inkludera” eller ”undanta'.|
|**target_type**    |nvarchar(128)| Typ av måldatabasen eller samling databaser, till exempel alla databaser i en server, alla databaser i en elastisk pool eller en databas. Giltiga värden för target_type är ”SqlServer', 'SqlElasticPool”, ”SqlDatabase” eller ”SqlShardMap”.|
|**target_id**  |uniqueidentifier|  Unikt ID för mål-gruppmedlem.|
|**refresh_credential_name**    |nvarchar(128)  |Namnet på databasen-omfattande autentisering som används för att ansluta till gruppmedlem mål.|
|**subscription_id**    |uniqueidentifier|  Unikt ID för prenumerationen.|
|**resource_group_name**    |nvarchar(128)| Namnet på resursgruppen där gruppmedlem target finns.|
|**server_name**    |nvarchar(128)  |Namnet på SQL Database-server som ingår i målgruppen. Bara anges om target_type är ”SqlServer'. |
|**databasnamn**  |nvarchar(128)  |Namnet på databasen i målgruppen. Ange bara när target_type är ”SqlDatabase”.|
|**elastic_pool_name**  |nvarchar(128)| Namnet på den elastiska poolen i målgruppen. Ange bara när target_type är 'SqlElasticPool'.|
|**shard_map_name** |nvarchar(128)| Namnet på fragmentkartan i målgruppen. Ange bara när target_type är 'SqlShardMap'.|


## <a name="resources"></a>Resurser

 - ![Avsnittet länkikon](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "avsnittet länkikon") [konventioner för Transact-SQL-Syntax](https://docs.microsoft.com/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  


## <a name="next-steps"></a>Nästa steg

- [Skapa och hantera elastiska jobb med PowerShell](elastic-jobs-powershell.md)
- [Auktorisering och behörigheter för SQLServer](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
  
