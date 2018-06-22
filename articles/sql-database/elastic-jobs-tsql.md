---
title: Skapa och hantera Azure SQL elastisk databas jobb med hjälp av Transact-SQL (T-SQL) | Microsoft Docs
description: Kör skript över flera databaser med elastiska databasen jobbet agenten med hjälp av Transact-SQL (T-SQL).
services: sql-database
author: jaredmoo
manager: craigg
ms.service: sql-database
ms.topic: article
ms.date: 06/14/2018
ms.author: jaredmoo
ms.openlocfilehash: fb6e4ebd635d8afa8e679ee5bb0f5646f28f887b
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313341"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Använda Transact-SQL (T-SQL) för att skapa och hantera jobb för elastisk databas

Den här artikeln innehåller många exempelscenarier för att komma igång med elastiska jobb med hjälp av T-SQL.

Exemplen använder den [lagrade procedurer](#job-stored-procedures) och [vyer](#job-views) tillgängliga i den [ *jobbet databasen*](elastic-jobs-overview.md#job-database).

Transact-SQL (T-SQL) används för att skapa, konfigurera, köra och hantera jobb. Skapa elastiska jobb-agenten stöds inte i T-SQL, så måste du först skapa en *elastiska jobb agent* med hjälp av portalen eller [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).


## <a name="create-a-credential-for-job-execution"></a>Skapa autentiseringsuppgifter för jobbkörningen

Autentiseringsuppgifterna används för att ansluta till din måldatabaserna för körning av skript. Autentiseringsuppgifter behöver rätt behörigheter på de databaser som anges av målgruppen för att köra skriptet. När du använder en server och/eller pool mål gruppmedlemmen hög du rekommenderas för att skapa en master autentiseringsuppgifter används för att uppdatera autentiseringsuppgifter innan expandering av servern och/eller pool vid tidpunkten för jobbkörningen. Databasbegränsade autentiseringsuppgifter skapas på jobbet agent-databasen. Samma autentiseringsuppgifter måste användas för att *skapar en inloggning* och *skapar du en användare logga in på bevilja inloggningsbehörighet för databasen* på måldatabaserna som.


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

I följande exempel visas hur du kör ett jobb för alla databaser på en server.  
Ansluta till den [ *jobbet databasen* ](elastic-jobs-overview.md#job-database) och kör följande kommando:


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


## <a name="exclude-a-single-database"></a>Undanta en enskild databas

I följande exempel visas hur du kör ett jobb för alla databaser i en server, förutom databasens namngivna *MappingDB*.  
Ansluta till den [ *jobbet databasen* ](elastic-jobs-overview.md#job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group = N'ServerGroup'
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

--Excude a database target member from the server target group
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

I följande exempel visas hur du kan arbeta alla databaser i en eller flera elastiska pooler.  
Ansluta till den [ *jobbet databasen* ](elastic-jobs-overview.md#job-database) och kör följande kommando:

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

I följande exempel visas hur du distribuerar nya schemat för alla databaser.  
Ansluta till den [ *jobbet databasen* ](elastic-jobs-overview.md#job-database) och kör följande kommando:


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

I många scenarier för data-samling, kan det vara praktiskt att lägga till några av dessa skript variabler för att bearbeta resultaten av jobbet efter.

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
Ansluta till den [ *jobbet databasen* ](elastic-jobs-overview.md#job-database) och kör följande kommandon:

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
@output_credential_name=’myjobcred’,
@output_server_name=’server1.database.windows.net',
@output_database_name=’<resultsdb>',
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
@output_server_name=’server1.database.windows.net',
@output_database_name=’resultsdb',
@output_table_name='resutlstable'
```


## <a name="view-job-definitions"></a>Visa jobbdefinitioner

I följande exempel visas hur du visar aktuella jobbdefinitioner.  
Ansluta till den [ *jobbet databasen* ](elastic-jobs-overview.md#job-database) och kör följande kommando:

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

I följande exempel visas hur du startar ett jobb omedelbart.  
Ansluta till den [ *jobbet databasen* ](elastic-jobs-overview.md#job-database) och kör följande kommando:

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
Ansluta till den [ *jobbet databasen* ](elastic-jobs-overview.md#job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Övervaka status för körning av jobbet

I följande exempel visas hur du visar information om körning av statusen för alla jobb.  
Ansluta till den [ *jobbet databasen* ](elastic-jobs-overview.md#job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named ‘ResultsPoolJob’
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named ‘ResultsPoolsJob’
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions 
WHERE is_active = 1
ORDER BY start_time DESC
```


## <a name="cancel-a-job"></a>Avbryta ett jobb

I följande exempel visar hur du avbryta ett jobb.  
Ansluta till den [ *jobbet databasen* ](elastic-jobs-overview.md#job-database) och kör följande kommando:

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

I följande exempel visas hur du tar bort jobbhistorik före ett visst datum.  
Ansluta till den [ *jobbet databasen* ](elastic-jobs-overview.md#job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Ta bort ett jobb och alla dess jobbhistorik

I följande exempel visas hur du tar bort ett jobb och alla relaterade jobbhistorik.  
Ansluta till den [ *jobbet databasen* ](elastic-jobs-overview.md#job-database) och kör följande kommando:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>Jobbet lagrade procedurer

Följande lagrade procedurer finns i den [jobb databasen](elastic-jobs-overview.md#job-database).



|Lagrad procedur  |Beskrivning  |
|---------|---------|
|[sp_add_job](#spaddjob)     |     Lägger till ett nytt jobb.    |
|[sp_update_job ](#spupdatejob)    |      Uppdaterar ett befintligt jobb.   |
|[sp_delete_job](#spdeletejob)     |      Tar bort ett befintligt jobb.   |
|[sp_add_jobstep](#spaddjobstep)    |    Lägger till ett steg till ett jobb.     |
|[sp_update_jobstep](#spupdatejobstep)     |     Uppdaterar jobbsteg.    |
|[sp_delete_jobstep](#spdeletejobstep)     |     Tar bort ett steg.    |
|[sp_start_job](#spstartjob)    |  Startar ett jobb körs.       |
|[sp_stop_job](#spstopjob)     |     Stoppar ett jobbkörningen.   |
|[sp_add_target_group](#spaddtargetgroup)    |     Lägger till en målgrupp.    |
|[sp_delete_target_group](#spdeletetargetgroup)     |    Tar bort en målgrupp.     |
|[sp_add_target_group_member](#spaddtargetgroupmember)     |    Lägger till en databas eller en grupp databaser i en målgrupp.     |
|[sp_delete_target_group_member](#spdeletetargetgroupmember)     |     Tar bort en mål medlem från en målgrupp.    |
|[sp_purge_jobhistory ](#sppurgejobhistory)    |    Tar bort tidigare poster för ett jobb.     |





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
Namnet på jobbet. Namnet måste vara unikt och får inte innehålla tecknet procent (%). job_name är nvarchar(128) med inget standardvärde.

[  **@description =** ] 'beskrivning'  
Beskrivning av jobbet. Beskrivningen är nvarchar(512) med standardvärdet NULL. Om beskrivningen utelämnas används en tom sträng.

[  **@enabled =** ] aktiverad  
Om den jobbschemat är aktiverat. Aktiverad bitar, med ett standardvärde på 0 (inaktiverad). Om värdet är 0 jobbet har inte aktiverats och kan köras inte enligt sitt schema; men kan det köras manuellt. Om den 1 jobbet ska köras enligt sitt schema och kan även köras manuellt.

[  **@schedule_interval_type =**] schedule_interval_type  
Värdet anger när jobbet ska köras. schedule_interval_type är nvarchar(50) med en standard på en gång, och kan vara något av följande värden:
- ”En gång”,
- Minuter,
- Timmar,
- Dagar.
- Veckor,
- ”Månader”

[  **@schedule_interval_count =** ] schedule_interval_count  
Antal schedule_interval_count perioder som sker mellan varje körning av jobbet. schedule_interval_count är int, med standardvärdet 1. Värdet måste vara större än eller lika med 1.

[  **@schedule_start_time =** ] schedule_start_time  
Datum vilket jobb som körning kan börja. schedule_start_time är DATETIME2, med 0001-01-01 00:00:00.0000000 standardinställningen.

[  **@schedule_end_time =** ] schedule_end_time  
Datum i vilket projekt körning kan stoppa. schedule_end_time är DATETIME2, med 9999-12-31 standardinställningen 11:59:59.0000000. 

[  **@job_id =** ] job_id utdata  
Jobbet ID-nummer i jobbet om har skapats. job_id är en utgående variabel av typen uniqueidentifier.

#### <a name="return-code-values"></a>Returkod för värden

0 (klart) eller 1 (fel)

#### <a name="remarks"></a>Kommentarer
sp_add_job måste köras från jobbet agent databasen anges när du skapar jobbet agenten.
När sp_add_job har körts för att lägga till ett jobb, användas sp_add_jobstep för att lägga till steg som utför aktiviteterna för jobbet. Jobbets första versionsnumret är 0, vilket ökas till 1 när det första steget läggs.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den lagrade proceduren. Inställningen begränsa en användare att de bara att kunna övervaka jobb, kan du bevilja användaren som en del av följande databasrollen i jobbet agent databasen anges när du skapar jobbet agenten:

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
Det nya namnet för jobbet. nytt_namn är nvarchar(128).

[  **@description =** ] 'beskrivning'  
Beskrivning av jobbet. Beskrivningen är nvarchar(512).

[  **@enabled =** ] aktiverad  
Anger om den jobbschemat är aktiverat (1) eller inte har aktiverats (0). Aktiverad är bit.

[  **@schedule_interval_type=** ] schedule_interval_type  
Värdet anger när jobbet ska köras. schedule_interval_type är nvarchar(50) och kan vara något av följande värden:

- ”En gång”,
- Minuter,
- Timmar,
- Dagar.
- Veckor,
- ”Månader”

[  **@schedule_interval_count=** ] schedule_interval_count  
Antal schedule_interval_count perioder som sker mellan varje körning av jobbet. schedule_interval_count är int, med standardvärdet 1. Värdet måste vara större än eller lika med 1.

[  **@schedule_start_time=** ] schedule_start_time  
Datum vilket jobb som körning kan börja. schedule_start_time är DATETIME2, med 0001-01-01 00:00:00.0000000 standardinställningen.

[  **@schedule_end_time=** ] schedule_end_time  
Datum i vilket projekt körning kan stoppa. schedule_end_time är DATETIME2, med 9999-12-31 standardinställningen 11:59:59.0000000. 

#### <a name="return-code-values"></a>Returkod för värden
0 (klart) eller 1 (fel)

#### <a name="remarks"></a>Kommentarer
När sp_add_job har körts för att lägga till ett jobb, användas sp_add_jobstep för att lägga till steg som utför aktiviteterna för jobbet. Jobbets första versionsnumret är 0, vilket ökas till 1 när det första steget läggs.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den lagrade proceduren. Inställningen begränsa en användare att de bara att kunna övervaka jobb, kan du bevilja användaren som en del av följande databasrollen i jobbet agent databasen anges när du skapar jobbet agenten:
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
Anger om du vill ta bort om förloppet för jobbet har alla körningar och Avbryt alla pågående körningar (1) eller misslyckas om alla jobb körningar pågår (0). Tvingad bitar.

#### <a name="return-code-values"></a>Returkod för värden
0 (klart) eller 1 (fel)

#### <a name="remarks"></a>Kommentarer
Jobbhistorik tas bort automatiskt när ett jobb har tagits bort.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den lagrade proceduren. Inställningen begränsa en användare att de bara att kunna övervaka jobb, kan du bevilja användaren som en del av följande databasrollen i jobbet agent databasen anges när du skapar jobbet agenten:
- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.



### <a name="spaddjobstep"></a>sp_add_jobstep

Lägger till ett steg till ett jobb.

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
Namnet på jobbet som du vill lägga till steget. job_name är nvarchar(128).

[  **@step_id =** ] step_id  
Identifiering sekvensnumret för jobbsteg. Steg identifikationsnummer börjar vid 1 och öka utan luckor. Om du redan har ett befintligt steg detta id, är sedan att steg och alla följande steg kommer att ha sina id stegvis så att den här nya steg kan infogas i sekvensen. Om den inte anges ska i step_id tilldelas automatiskt till den senaste i följd av steg. step_id är ett heltal.

[  **@step_name =** ] step_name  
Namnet på steget. Måste anges, utom det första steget i ett jobb som har ett standardnamn av 'Jobbsteg' (i informationssyfte). step_name är nvarchar(128).

[  **@command_type =** ] 'command_type'  
Typ av kommando som körs av den här jobbsteg. command_type är nvarchar(50) med ett standardvärde på TSql, vilket innebär att värdet för den @command_type parametern är en T-SQL-skript.

Om anges måste värdet vara TSql.

[  **@command_source =** ] 'command_source'  
Typ av plats där kommandot lagras. command_source är nvarchar(50) med ett standardvärde för infogade, vilket innebär att värdet för den @command_source parameter är literal texten för kommandot.

Om anges måste värdet vara infogad.

[  **@command =** ], kommando:  
Kommandot måste vara giltig T-SQL-skript och sedan körs av det här steget för jobbet. kommandot är nvarchar(max) med standardvärdet NULL.

[  **@credential_name =** ] 'credential_name'  
Namnet på databasen-omfattande autentisering i det här jobbet kontrollen databas som används för att ansluta till varje måldatabaserna inom målgruppen när det här steget körs. credential_name är nvarchar(128).

[  **@target_group_name =** ] 'target gruppnamn'  
Namnet på målgruppen som innehåller måldatabaserna steget jobbet ska köras på. target_group_name är nvarchar(128).

[  **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Fördröjning innan det första förnyade försöker om steget jobb misslyckas på det första körningen försöket. initial_retry_interval_seconds är int, med standardvärdet 1.

[  **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maximal fördröjning mellan försök. Om fördröjningen mellan försök skulle bli större än det här värdet, är det begränsat till det här värdet i stället. maximum_retry_interval_seconds är int, med standardvärdet på 120.

[  **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Multiplikatorn ska gälla för fördröjning av försök igen om flera jobb steg körningen försöker misslyckas. Till exempel om det första förnyade hade en fördröjning på 5 andra och backoff multiplikatorn är 2.0, sedan andra försök har en fördröjning på 10 sekunder och tredje försök har en fördröjning på 20 sekunder. retry_interval_backoff_multiplier är verkliga med standardvärdet 2.0.

[  **@retry_attempts =** ] retry_attempts  
Antal gånger att försöka köra om det första försöket misslyckas. Till exempel om retry_attempts-värdet är 10, tas 1 inledande omförsök försöket och 10, vilket ger totalt 11 försök. Om det slutliga försöket misslyckas avslutas jobbkörningen med en livscykel misslyckades. retry_attempts är int, med standardvärdet 10.

[  **@step_timeout_seconds =** ] step_timeout_seconds  
Längsta tid det tar att utföra steget. Om den här tiden har överskridits avslutas jobbkörningen med en livscykel av för lång tid. step_timeout_seconds är int, med standardvärdet 43 200 sekunder (12 timmar).

[  **@output_type =** ] 'output_type'  
Om inte null, vilken typ av destination kommandots första resultatuppsättningen skrivs till. output_type är nvarchar(50) med standardvärdet NULL.

Om anges måste värdet vara SqlDatabase.

[  **@output_credential_name =** ] 'output_credential_name'  
Om inte null begränsad namnet på databasen autentiseringsuppgifter som används för att ansluta till måldatabasen utdata. Måste anges om output_type är lika med SqlDatabase. output_credential_name är nvarchar(128) med standardvärdet NULL.

[  **@output_subscription_id =** ] 'output_subscription_id'  
Måste beskrivning.

[  **@output_resource_group_name =** ] 'output_resource_group_name'  
Måste beskrivning.

[  **@output_server_name =** ] 'output_server_name'  
Om inte null, det fullständigt kvalificerade DNS-namnet på den server som innehåller måldatabasen utdata. Måste anges om output_type är lika med SqlDatabase. output_server_name är nvarchar(256) med standardvärdet NULL.

[  **@output_database_name =** ] 'output_database_name'  
Om inte är null, namnet på databasen innehåller som måltabellen utdata. Måste anges om output_type är lika med SqlDatabase. output_database_name är nvarchar(128) med standardvärdet NULL.

[  **@output_schema_name =** ] 'output_schema_name'  
Om inte är null, namnet på SQL-schemat innehåller som måltabellen utdata. Om output_type är lika med SqlDatabase, är standardvärdet dbo. output_schema_name är nvarchar(128).

[  **@output_table_name =** ] 'output_table_name'  
Om inte null, namnet på den tabell som det kommandot första resultatuppsättningen ska skrivas till. Om tabellen inte redan finns, kommer den att skapas baserat på schemat för att returnera resultatuppsättningen. Måste anges om output_type är lika med SqlDatabase. output_table_name är nvarchar(128) med standardvärdet NULL.

[  **@job_version =** ] job_version utdata  
Output-parameter som kommer att tilldelas nya jobb versionsnumret. job_version är heltal.

[  **@max_parallelism =** ] max_parallelism utdata  
Den maximala nivån för parallellitet per elastisk pool. Om uppsättningen och sedan steget jobbet kommer att vara begränsad till endast köras på högst som många databaser per elastisk pool. Detta gäller för varje elastisk pool som ingår direkt i målgruppen eller inuti en server som ingår i målgruppen. max_parallelism är heltal.


#### <a name="return-code-values"></a>Returkod för värden
0 (klart) eller 1 (fel)

#### <a name="remarks"></a>Kommentarer
När sp_add_jobstep lyckas stegvis jobbets aktuella versionsnumret. Jobbet körs nästa gång används den nya versionen. Om jobbet körs, innehåller att körningen inte nytt steg.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den lagrade proceduren. Inställningen begränsa en användare att de bara att kunna övervaka jobb, kan du bevilja användaren som en del av följande databasrollen i jobbet agent databasen anges när du skapar jobbet agenten:  

- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.



### <a name="spupdatejobstep"></a>sp_update_jobstep

Uppdaterar jobbsteg.

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
Namnet på jobbet som steget tillhör. job_name är nvarchar(128).

[  **@step_id =** ] step_id  
ID-numret för steget som ska ändras. Step_id eller step_name måste anges. step_id är ett heltal.

[  **@step_name =** ] 'step_name'  
Namnet på steget som ska ändras. Step_id eller step_name måste anges. step_name är nvarchar(128).

[  **@new_id =** ] new_id  
Ny sekvens ID-numret för steget jobb. Steg identifikationsnummer börjar vid 1 och öka utan luckor. Om ett steg om, sedan anpassas andra åtgärder automatiskt.

[  **@new_name =** ] 'nytt_namn'  
Det nya namnet på steget. nytt_namn är nvarchar(128).

[  **@command_type =** ] 'command_type'  
Typ av kommando som körs av den här jobbsteg. command_type är nvarchar(50) med ett standardvärde på TSql, vilket innebär att värdet för den @command_type parametern är en T-SQL-skript.

Om anges måste värdet vara TSql.

[  **@command_source =** ] 'command_source'  
Typ av plats där kommandot lagras. command_source är nvarchar(50) med ett standardvärde för infogade, vilket innebär att värdet för den @command_source parameter är literal texten för kommandot.

Om anges måste värdet vara infogad.

[  **@command =** ], kommando:  
I kommandot måste vara giltig T-SQL-skript och sedan körs av det här steget för jobbet. kommandot är nvarchar(max) med standardvärdet NULL.

[  **@credential_name =** ] 'credential_name'  
Namnet på databasen-omfattande autentisering i det här jobbet kontrollen databas som används för att ansluta till varje måldatabaserna inom målgruppen när det här steget körs. credential_name är nvarchar(128).

[  **@target_group_name =** ] 'target gruppnamn'  
Namnet på målgruppen som innehåller måldatabaserna steget jobbet ska köras på. target_group_name är nvarchar(128).

[  **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Fördröjning innan det första förnyade försöker om steget jobb misslyckas på det första körningen försöket. initial_retry_interval_seconds är int, med standardvärdet 1.

[  **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maximal fördröjning mellan försök. Om fördröjningen mellan försök skulle bli större än det här värdet, är det begränsat till det här värdet i stället. maximum_retry_interval_seconds är int, med standardvärdet på 120.

[  **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Multiplikatorn ska gälla för fördröjning av försök igen om flera jobb steg körningen försöker misslyckas. Till exempel om det första förnyade hade en fördröjning på 5 andra och backoff multiplikatorn är 2.0, sedan andra försök har en fördröjning på 10 sekunder och tredje försök har en fördröjning på 20 sekunder. retry_interval_backoff_multiplier är verkliga med standardvärdet 2.0.

[  **@retry_attempts =** ] retry_attempts  
Antal gånger att försöka köra om det första försöket misslyckas. Till exempel om retry_attempts-värdet är 10, tas 1 inledande omförsök försöket och 10, vilket ger totalt 11 försök. Om det slutliga försöket misslyckas avslutas jobbkörningen med en livscykel misslyckades. retry_attempts är int, med standardvärdet 10.

[  **@step_timeout_seconds =** ] step_timeout_seconds  
Längsta tid det tar att utföra steget. Om den här tiden har överskridits avslutas jobbkörningen med en livscykel av för lång tid. step_timeout_seconds är int, med standardvärdet 43 200 sekunder (12 timmar).

[  **@output_type =** ] 'output_type'  
Om inte null, vilken typ av destination kommandots första resultatuppsättningen skrivs till. Om du vill återställa värdet för output_type till NULL, anger du värdet för den här parametern till '' (tom sträng). output_type är nvarchar(50) med standardvärdet NULL.

Om anges måste värdet vara SqlDatabase.

[  **@output_credential_name =** ] 'output_credential_name'  
Om inte null begränsad namnet på databasen autentiseringsuppgifter som används för att ansluta till måldatabasen utdata. Måste anges om output_type är lika med SqlDatabase. Om du vill återställa värdet för output_credential_name till NULL, anger du värdet för den här parametern till '' (tom sträng). output_credential_name är nvarchar(128) med standardvärdet NULL.

[  **@output_server_name =** ] 'output_server_name'  
Om inte null, det fullständigt kvalificerade DNS-namnet på den server som innehåller måldatabasen utdata. Måste anges om output_type är lika med SqlDatabase. Om du vill återställa värdet för output_server_name till NULL, anger du värdet för den här parametern till '' (tom sträng). output_server_name är nvarchar(256) med standardvärdet NULL.

[  **@output_database_name =** ] 'output_database_name'  
Om inte är null, namnet på databasen innehåller som måltabellen utdata. Måste anges om output_type är lika med SqlDatabase. Om du vill återställa värdet för output_database_name till NULL, anger du värdet för den här parametern till '' (tom sträng). output_database_name är nvarchar(128) med standardvärdet NULL.

[  **@output_schema_name =** ] 'output_schema_name'  
Om inte är null, namnet på SQL-schemat innehåller som måltabellen utdata. Om output_type är lika med SqlDatabase, är standardvärdet dbo. Om du vill återställa värdet för output_schema_name till NULL, anger du värdet för den här parametern till '' (tom sträng). output_schema_name är nvarchar(128).

[  **@output_table_name =** ] 'output_table_name'  
Om inte null, namnet på den tabell som det kommandot första resultatuppsättningen ska skrivas till. Om tabellen inte redan finns, kommer den att skapas baserat på schemat för att returnera resultatuppsättningen. Måste anges om output_type är lika med SqlDatabase. Om du vill återställa värdet för output_server_name till NULL, anger du värdet för den här parametern till '' (tom sträng). output_table_name är nvarchar(128) med standardvärdet NULL.

[  **@job_version =** ] job_version utdata  
Output-parameter som kommer att tilldelas nya jobb versionsnumret. job_version är heltal.

[  **@max_parallelism =** ] max_parallelism utdata  
Den maximala nivån för parallellitet per elastisk pool. Om uppsättningen och sedan steget jobbet kommer att vara begränsad till endast köras på högst som många databaser per elastisk pool. Detta gäller för varje elastisk pool som ingår direkt i målgruppen eller inuti en server som ingår i målgruppen. Om du vill återställa värdet för max_parallelism till null-värde för denna parameter 1. max_parallelism är heltal.


#### <a name="return-code-values"></a>Returkod för värden
0 (klart) eller 1 (fel)

#### <a name="remarks"></a>Kommentarer
Alla pågående körningar av jobbet påverkas inte. När sp_update_jobstep lyckas stegvis jobbets versionsnumret. Jobbet körs nästa gång används den nya versionen.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den lagrade proceduren. Inställningen begränsa en användare att de bara att kunna övervaka jobb, kan du bevilja användaren som en del av följande databasrollen i jobbet agent databasen anges när du skapar jobbet agenten:

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
Namnet på jobbet som steget kommer att tas bort. job_name är nvarchar(128) med inget standardvärde.

[  **@step_id =** ] step_id  
ID-numret för steget som ska tas bort. Step_id eller step_name måste anges. step_id är ett heltal.

[  **@step_name =** ] 'step_name'  
Namnet på steget som ska tas bort. Step_id eller step_name måste anges. step_name är nvarchar(128).

[  **@job_version =** ] job_version utdata  
Output-parameter som kommer att tilldelas nya jobb versionsnumret. job_version är heltal.

#### <a name="return-code-values"></a>Returkod för värden
0 (klart) eller 1 (fel)

#### <a name="remarks"></a>Kommentarer
Alla pågående körningar av jobbet påverkas inte. När sp_update_jobstep lyckas stegvis jobbets versionsnumret. Jobbet körs nästa gång används den nya versionen.

Andra jobb steg numreras automatiskt om du vill fylla luckan efter steget Borttaget jobb.
 
#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den lagrade proceduren. Inställningen begränsa en användare att de bara att kunna övervaka jobb, kan du bevilja användaren som en del av följande databasrollen i jobbet agent databasen anges när du skapar jobbet agenten:
- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.






### <a name="spstartjob"></a>sp_start_job

Startar ett jobb körs.

#### <a name="syntax"></a>Syntax


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argument
[  **@job_name =** ] 'job_name'  
Namnet på jobbet som steget kommer att tas bort. job_name är nvarchar(128) med inget standardvärde.

[  **@job_execution_id =** ] job_execution_id utdata  
Utdataparameter som ska tilldelas den jobbkörningen id. job_version är uniqueidentifier.

#### <a name="return-code-values"></a>Returkod för värden
0 (klart) eller 1 (fel)

#### <a name="remarks"></a>Kommentarer
Ingen.
 
#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den lagrade proceduren. Inställningen begränsa en användare att de bara att kunna övervaka jobb, kan du bevilja användaren som en del av följande databasrollen i jobbet agent databasen anges när du skapar jobbet agenten:
- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.

### <a name="spstopjob"></a>sp_stop_job

Stoppar ett jobbkörningen.

#### <a name="syntax"></a>Syntax


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argument
[  **@job_execution_id =** ] job_execution_id  
ID-nummer för jobbkörningen att stoppa. job_execution_id är uniqueidentifier med standardvärdet NULL.

#### <a name="return-code-values"></a>Returkod för värden
0 (klart) eller 1 (fel)

#### <a name="remarks"></a>Kommentarer
Ingen.
 
#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den lagrade proceduren. Inställningen begränsa en användare att de bara att kunna övervaka jobb, kan du bevilja användaren som en del av följande databasrollen i jobbet agent databasen anges när du skapar jobbet agenten:
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
Namnet på målgruppen för att skapa. target_group_name är nvarchar(128) med inget standardvärde.

[  **@target_group_id =** ] target_group_id utdata målet grupp-ID-nummer som tilldelas jobbet i har skapats. target_group_id är en utgående variabel av typen uniqueidentifier med standardvärdet NULL.

#### <a name="return-code-values"></a>Returkod för värden
0 (klart) eller 1 (fel)

#### <a name="remarks"></a>Kommentarer
Målgrupper ger ett enkelt sätt att fokusera på ett jobb i en samling av databaser.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den lagrade proceduren. Inställningen begränsa en användare att de bara att kunna övervaka jobb, kan du bevilja användaren som en del av följande databasrollen i jobbet agent databasen anges när du skapar jobbet agenten:
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
Namnet på målgruppen för att ta bort. target_group_name är nvarchar(128) med inget standardvärde.

#### <a name="return-code-values"></a>Returkod för värden
0 (klart) eller 1 (fel)

#### <a name="remarks"></a>Kommentarer
Ingen.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den lagrade proceduren. Inställningen begränsa en användare att de bara att kunna övervaka jobb, kan du bevilja användaren som en del av följande databasrollen i jobbet agent databasen anges när du skapar jobbet agenten:
- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.

### <a name="spaddtargetgroupmember"></a>sp_add_target_group_member

Lägger till en databas eller en grupp databaser i en målgrupp.

#### <a name="syntax"></a>Syntax

```sql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] ‘membership_type’ ]   
        [ , [ @target_type = ] ‘target_type’ ]   
        [ , [ @refresh_credential_name = ] ‘refresh_credential_name’ ]   
        [ , [ @server_name = ] ‘server_name’ ]   
        [ , [ @database_name = ] ‘database_name’ ]   
        [ , [ @elastic_pool_name = ] ‘elastic_pool_name’ ]   
        [ , [ @shard_map_name = ] ‘shard_map_name’ ]   
        [ , [ @target_id = ] ‘target_id’ OUTPUT ]
```

#### <a name="arguments"></a>Argument
[  **@target_group_name =** ] 'target_group_name'  
Namnet på den målgrupp som medlemmen ska läggas till. target_group_name är nvarchar(128) med inget standardvärde.

[  **@membership_type =** ] 'membership_type'  
Anger om gruppmedlemmen mål ska inkluderas eller uteslutas. target_group_name är nvarchar(128) med standardvärdet 'Vara'. Giltiga värden för target_group_name är 'Vara' eller 'Exkludera'.

[  **@target_type =** ] 'target_type'  
Typ av måldatabasen eller mängd av databaser, inklusive alla databaser i en server, alla databaser i en elastisk pool, alla databaser i en Fragmentera karta eller en individuell databas. target_type är nvarchar(128) med inget standardvärde. Giltiga värden för target_type är ”SqlServer', 'SqlElasticPool', 'SqlDatabase' eller 'SqlShardMap'. 

[  **@refresh_credential_name =** ] 'refresh_credential_name'  
Namnet på den logiska servern. refresh_credential_name är nvarchar(128) med inget standardvärde.

[  **@server_name =** ] 'servernamn'  
Namnet på den logiska servern som ska läggas till den angivna målgruppen. servernamn måste anges när target_type är ”SqlServer'. server_name är nvarchar(128) med inget standardvärde.

[  **@database_name =** ] 'database_name'  
Namnet på databasen som ska läggas till den angivna målgruppen. database_name bör anges när target_type är ”SqlDatabase”. database_name är nvarchar(128) med inget standardvärde.

[  **@elastic_pool_name =** ] 'elastic_pool_name'  
Namnet på den elastiska poolen som ska läggas till den angivna målgruppen. elastic_pool_name måste anges när target_type är 'SqlElasticPool'. elastic_pool_name är nvarchar(128) med inget standardvärde.

[  **@shard_map_name =** ] 'shard_map_name'  
Namnet på poolen Fragmentera karta som ska läggas till den angivna målgruppen. elastic_pool_name måste anges när target_type är 'SqlSqlShardMap'. shard_map_name är nvarchar(128) med inget standardvärde.

[  **@target_id =** ] target_group_id utdata  
Mål-ID-nummer till målet gruppmedlemmens om skapat lägga till målgruppen. target_id är en utgående variabel av typen uniqueidentifier med standardvärdet NULL.
Returvärden kod 0 (klart) eller 1 (fel)

#### <a name="remarks"></a>Kommentarer
Ett jobb som körs på alla databaser på en server eller elastisk pool vid körningstid, när en logisk server eller en elastisk pool som ingår i målgruppen.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den lagrade proceduren. Inställningen begränsa en användare att de bara att kunna övervaka jobb, kan du bevilja användaren som en del av följande databasrollen i jobbet agent databasen anges när du skapar jobbet agenten:
- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.

#### <a name="examples"></a>Exempel
I följande exempel läggs alla databaser i London och NewYork servrar till gruppen servrar underhålla kundinformation. Du måste ansluta till den angivna när du skapar jobbet-agenten i detta fall ElasticJobs jobb databasen.

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
        [ , [ @target_id = ] ‘target_id’]
```



Argument [ @target_group_name =] 'target_group_name'  
Namnet på den målgrupp som du vill ta bort gruppmedlemmen mål. target_group_name är nvarchar(128) med inget standardvärde.

[ @target_id =] target_id  
 Mål-ID-nummer tilldelats gruppmedlemmen mål som ska tas bort. target_id är en unik identifierare med en standard-NULL.

#### <a name="return-code-values"></a>Returkod för värden
0 (klart) eller 1 (fel)

#### <a name="remarks"></a>Kommentarer
Målgrupper ger ett enkelt sätt att fokusera på ett jobb i en samling av databaser.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den lagrade proceduren. Inställningen begränsa en användare att de bara att kunna övervaka jobb, kan du bevilja användaren som en del av följande databasrollen i jobbet agent databasen anges när du skapar jobbet agenten:
- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.

#### <a name="examples"></a>Exempel
I följande exempel tar bort London-server från gruppen servrar underhålla kundinformation. Du måste ansluta till den angivna när du skapar jobbet-agenten i detta fall ElasticJobs jobb databasen.

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

Tar bort tidigare poster för ett jobb.

#### <a name="syntax"></a>Syntax


```sql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argument
[  **@job_name =** ] 'job_name'  
Namnet på jobbet som du vill ta bort tidigare poster. job_name är nvarchar(128) med standardvärdet NULL. Du måste ange job_id eller job_name, men båda kan inte anges.

[  **@job_id =** ] job_id  
 Jobbet identifieringsnumret för jobbet för att poster ska tas bort. job_id är uniqueidentifier med standardvärdet NULL. Du måste ange job_id eller job_name, men båda kan inte anges.

[  **@oldest_date =** ] oldest_date  
 Den äldsta posten ska kvarhållas i historiken. oldest_date är DATETIME2, med ett standardvärde på NULL. Om du har angett oldest_date sp_purge_jobhistory bara tar bort poster som är äldre än det angivna värdet.

#### <a name="return-code-values"></a>Returkod för värden
0 (klart) eller 1 (fel) anmärkningar-målgrupper är det enkelt att fokusera på ett jobb i en samling av databaser.

#### <a name="permissions"></a>Behörigheter
Som standard kan medlemmar i den fasta serverrollen sysadmin köra den lagrade proceduren. Inställningen begränsa en användare att de bara att kunna övervaka jobb, kan du bevilja användaren som en del av följande databasrollen i jobbet agent databasen anges när du skapar jobbet agenten:
- jobs_reader

Mer information om behörigheter för dessa roller finns i avsnittet behörighet i det här dokumentet. Endast medlemmar i sysadmin kan använda den här lagrade proceduren för att redigera attribut för jobb som ägs av andra användare.

#### <a name="examples"></a>Exempel
I följande exempel läggs alla databaser i London och NewYork servrar till gruppen servrar underhålla kundinformation. Du måste ansluta till den angivna när du skapar jobbet-agenten i detta fall ElasticJobs jobb databasen.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Jobbet vyer

Följande vyer som är tillgängliga i den [jobb databasen](elastic-jobs-overview.md#job-database).


|Visa  |Beskrivning  |
|---------|---------|
|[jobs_executions](#jobsexecutions-view)     |  Visar jobbhistorik körning.      |
|[Jobb](#jobs-view)     |   Visar alla jobb.      |
|[job_versions](#jobversions-view)     |   Visar alla jobbversioner.      |
|[jobbsteg](#jobsteps-view)     |     Visar alla steg i den aktuella versionen av varje jobb.    |
|[jobstep_versions](#jobstepversions-view)     |     Visar alla steg i alla versioner av varje jobb.    |
|[target_groups](#targetgroups-view)     |      Visar alla målgrupper.   |
|[target_group_members](#targetgroups-view)     |   Visar alla medlemmar i alla målgrupper som.      |


### <a name="jobsexecutions-view"></a>Visa jobs_executions

[jobb]. [jobs_executions]

Visar jobbhistorik körning.


|Kolumnnamn|   Datatyp   |Beskrivning|
|---------|---------|---------|
|**job_execution_id**   |Unik identifierare|  Unikt ID för en instans av en jobbkörningen.
|**job_name**   |nvarchar(128)  |Namnet på jobbet.
|**job_id** |Unik identifierare|  Unikt ID för jobbet.
|**job_version**    |int    |Version av jobbet (uppdateras automatiskt varje gång som jobbet har ändrats).
|**step_id**    |int|   (För det här jobbet) identifierare för steget. NULL anger att detta är den överordnade jobbkörningen.
|**is_active**| bitar |Anger om informationen är aktiv eller inaktiv. 1 anger aktiva jobb och 0 anger inaktiv.
|**Livscykel**| nvarchar(50)|Värde som anger status för jobbet: 'Skapade', 'Pågår ”,” misslyckad ”,” lyckades ”,” överhoppade', 'SucceededWithSkipped'|
|**create_time**|   datetime2(7)|   Datum och tid då jobbet skapades.
|**Starttid** |datetime2(7)|  Datum och tid som jobbet startade körningen. NULL om jobbet inte har körts ännu.
|**end_time**|  datetime2(7)    |Datum och tid som jobbet avslutades körning. NULL om jobbet inte har ännu körts eller har inte slutförts ännu körning.
|**current_attempts**   |int    |Antal gånger som steget gjordes. Överordnade jobb kommer att vara 0, underordnade jobbet körningar ska vara 1 eller större baserat på körningsprincipen.
|**current_attempt_start_time** |datetime2(7)|  Datum och tid som jobbet startade körningen. NULL anger att detta är den överordnade jobbkörningen.
|**last_message**   |nvarchar(max)| Jobbet eller steg historik-meddelande. 
|**target_type**|   nvarchar(128)   |Typ av måldatabasen eller mängd av databaser, inklusive alla databaser i en server, alla databaser i en elastisk pool eller en databas. Giltiga värden för target_type är ”SqlServer', 'SqlElasticPool' eller 'SqlDatabase'. NULL anger att detta är den överordnade jobbkörningen.
|**target_id**  |Unik identifierare|  Unikt ID för gruppmedlemmen mål.  NULL anger att detta är den överordnade jobbkörningen.
|**target_group_name**  |nvarchar(128)  |Namnet på målgruppen. NULL anger att detta är den överordnade jobbkörningen.
|**target_server_name**|    nvarchar(256)|  Namnet på den logiska servern i målgruppen. Bara anges om target_type är ”SqlServer'. NULL anger att detta är den överordnade jobbkörningen.
|**target_database_name**   |nvarchar(128)| Namnet på databasen som ingår i målgruppen. Ange bara när target_type är ”SqlDatabase”. NULL anger att detta är den överordnade jobbkörningen.


### <a name="jobs-view"></a>Visa jobb

[jobb]. [jobb]

Visar alla jobb.

|Kolumnnamn|   Datatyp|  Beskrivning|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Namnet på jobbet.|
|**job_id**|    Unik identifierare    |Unikt ID för jobbet.|
|**job_version**    |int    |Version av jobbet (uppdateras automatiskt varje gång som jobbet har ändrats).|
|**Beskrivning**    |nvarchar(512)| Beskrivning för jobbet. aktiverade bit anger om jobbet är aktiverat eller inaktiverat. 1 anger aktiverade jobb och 0 anger inaktiverad jobb.|
|**schedule_interval_type** |nvarchar(50)   |Värde som anger när jobbet ska köras: ”en gång”, ”minuter”, ”timmar”, ”dagar”, ”veckor”, ”månader”
|**schedule_interval_count**|   int|    Antal schedule_interval_type perioder som sker mellan varje körning av jobbet.|
|**schedule_start_time**    |datetime2(7)|  Datum och tid som jobbet har startats senaste körningen.|
|**schedule_end_time**| datetime2(7)|   Datum och tid som jobbet har senaste har slutförts.|


### <a name="jobversions-view"></a>Visa job_versions

[jobb]. [job_verions]

Visar alla jobbversioner.

|Kolumnnamn|   Datatyp|  Beskrivning|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Namnet på jobbet.|
|**job_id**|    Unik identifierare    |Unikt ID för jobbet.|
|**job_version**    |int    |Version av jobbet (uppdateras automatiskt varje gång som jobbet har ändrats).|


### <a name="jobsteps-view"></a>Visa jobbsteg

[jobb]. [jobbsteg]

Visar alla steg i den aktuella versionen av varje jobb.

|Kolumnnamn    |Datatyp| Beskrivning|
|------|------|-------|
|**job_name**   |nvarchar(128)| Namnet på jobbet.|
|**job_id** |Unik identifierare   |Unikt ID för jobbet.|
|**job_version**|   int|    Version av jobbet (uppdateras automatiskt varje gång som jobbet har ändrats).|
|**step_id**    |int    |(För det här jobbet) identifierare för steget.|
|**step_name**  |nvarchar(128)  |Steget unikt (för det här jobbet) namn.|
|**command_type**   |nvarchar(50)   |Typ av kommando som ska köras i steget jobb. För v1, värdet måste vara lika med till och standardvärdet är 'TSql'.|
|**command_source** |nvarchar(50)|  Platsen för kommandot. För v1, 'Infogade' är standard och accepteras endast värdet.|
|**kommandot**|   nvarchar(max)|  Kommandon som ska köras med elastiska jobb via command_type.|
|**credential_name**|   nvarchar(128)   |Databasbegränsade referenserna för körning av jobbet.|
|**target_group_name**| nvarchar(128)   |Namnet på målgruppen.|
|**target_group_id**|   Unik identifierare|   Unikt ID för målgruppen.|
|**initial_retry_interval_seconds**|    int |Fördröjning innan det första försöket försök igen. Standardvärdet är 1.|
|**maximum_retry_interval_seconds** |int|   Maximal fördröjning mellan försök. Om fördröjningen mellan försök skulle bli större än det här värdet, är det begränsat till det här värdet i stället. Standardvärdet är 120.|
|**retry_interval_backoff_multiplier**  |Verklig|  Multiplikatorn ska gälla för fördröjning av försök igen om flera jobb steg körningen försöker misslyckas. Standardvärdet är 2.0.|
|**retry_attempts** |int|   Antalet försök försöker använda om det här steget misslyckas. Standardvärdet 10, vilket betyder att inga nya försök.|
|**step_timeout_seconds**   |int|   Hur lång tid i minuter mellan nya försök. Standardvärdet är 0, vilket anger 0-minutersintervall.|
|**output_type**    |nvarchar(11)|  Platsen för kommandot. I den aktuella förhandsvisningen 'Infogade' är standard och accepteras endast värdet.|
|**output_credential_name**|    nvarchar(128)   |Ange namnet på autentiseringsuppgifterna som ska användas för att ansluta till målservern för att lagra resultaten.|
|**output_subscription_id**|    Unik identifierare|   Unikt ID för prenumerationen för mål-server\database för resultatet från frågan.|
|**output_resource_group_name** |nvarchar(128)| Resursgruppens namn där målservern finns.|
|**output_server_name**|    nvarchar(256)   |Ange namnet på målservern för resultatet.|
|**output_database_name**   |nvarchar(128)| Ange namnet på måldatabasen som för resultat.|
|**output_schema_name** |nvarchar(max)| Namnet på målschema. Som standard dbo, om inget annat anges.|
|**output_table_name**| nvarchar(max)|  Namnet på tabellen för lagring av resultatet från resultatet av frågan. Tabellen kommer att skapas automatiskt baserat på schemat för resultatet om den inte redan finns. Schemat måste matcha schemat för resultatuppsättningen.|
|**max_parallelism**|   int|    Maximalt antal databaser per elastisk pool steget jobbet ska köras på i taget. Standardvärdet är NULL, vilket innebär att ingen gräns. |


### <a name="jobstepversions-view"></a>Visa jobstep_versions

[jobb]. [jobstep_versions]

Visar alla steg i alla versioner av varje jobb. Schemat är identisk med [jobbsteg](#jobsteps-view).

### <a name="targetgroups-view"></a>Visa target_groups

[jobb]. [target_groups]

Listar alla målgrupper.

|Kolumnnamn|Datatyp| Beskrivning|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |Namnet på målgruppen, en samling av databaser. 
|**target_group_id**    |Unik identifierare   |Unikt ID för målgruppen.

### <a name="targetgroupsmembers-view"></a>Visa target_groups_members

[jobb]. [target_groups_members]

Visar alla medlemmar i alla målgrupper som.

|Kolumnnamn|Datatyp| Beskrivning|
|-----|-----|-----|
|**target_group_name**  |nvarchar (128|Namnet på målgruppen, en samling av databaser. |
|**target_group_id**    |Unik identifierare   |Unikt ID för målgruppen.|
|**membership_type**    |int|   Anger om målet gruppmedlemmens inkluderas eller exkluderas i målgruppen. Giltiga värden för target_group_name är 'Vara' eller 'Exkludera'.|
|**target_type**    |nvarchar(128)| Typ av måldatabasen eller mängd av databaser, inklusive alla databaser i en server, alla databaser i en elastisk pool eller en databas. Giltiga värden för target_type är ”SqlServer', 'SqlElasticPool', 'SqlDatabase' eller 'SqlShardMap'.|
|**target_id**  |Unik identifierare|  Unikt ID för gruppmedlemmen mål.|
|**refresh_credential_name**    |nvarchar(128)  |Namnet på databasen som omfattas autentiseringsuppgifter som används för att ansluta till målet gruppmedlemmens.|
|**PRENUMERATIONSID**    |Unik identifierare|  Unikt ID för prenumerationen.|
|**resource_group_name**    |nvarchar(128)| Namnet på resursgruppen där målet gruppmedlemmens finns.|
|**servernamn**    |nvarchar(128)  |Namnet på den logiska servern i målgruppen. Bara anges om target_type är ”SqlServer'. |
|**databasnamn**  |nvarchar(128)  |Namnet på databasen som ingår i målgruppen. Ange bara när target_type är ”SqlDatabase”.|
|**elastic_pool_name**  |nvarchar(128)| Namnet på den elastiska poolen i målgruppen. Ange bara när target_type är 'SqlElasticPool'.|
|**shard_map_name** |nvarchar(128)| Namnet på Fragmentera kartan i målgruppen. Ange bara när target_type är 'SqlShardMap'.|


## <a name="resources"></a>Resurser

 - ![Ikon för avsnittet länk](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "avsnittet länkikonen") [konventioner för Transact-SQL-Syntax](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  


## <a name="next-steps"></a>Nästa steg

- [Skapa och hantera elastiska jobb med hjälp av PowerShell](elastic-jobs-powershell.md)
- [Auktorisering och behörigheter för SQLServer](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
  