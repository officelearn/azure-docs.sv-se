---
title: Skapa en elastisk jobbagent med PowerShell
description: Lär dig hur du skapar en elastisk jobbagent med PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
ms.date: 03/13/2019
ms.openlocfilehash: 74a72df9d8c0bc8a578fea57ab81fb496f8e6add
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420370"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>Skapa en elastisk jobbagent med PowerShell

[Elastiska jobb](sql-database-job-automation-overview.md#elastic-database-jobs-preview) aktiverar körning av ett eller flera Transact-SQL-skript (T-SQL) parallellt över flera databaser.

In this tutorial, you learn the steps required to run a query across multiple databases:

> [!div class="checklist"]
> * Skapa en elastiskt jobbagent
> * Skapa autentiseringsuppgifter för jobbet så att det kan köra skript på sina mål
> * Definiera mål (servrar, elastiska pooler, databaser, shard-kartor) som du vill köra jobbet mot
> * Skapa autentiseringsuppgifter för databasen i måldatabaserna så att agenten kan ansluta och köra jobb
> * Skapa ett jobb
> * Lägg till jobbsteg i ett jobb
> * Starta körningen av ett jobb
> * Övervaka ett jobb

## <a name="prerequisites"></a>Krav

The upgraded version of Elastic Database jobs has a new set of PowerShell cmdlets for use during migration. These new cmdlets transfer all of your existing job credentials, targets (including databases, servers, custom collections), job triggers, job schedules, job contents, and jobs over to a new Elastic Job agent.

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>Install the latest Elastic Jobs cmdlets

Om du inte redan har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Install the **Az.Sql** module to get the latest Elastic Job cmdlets. Kör följande kommandon i PowerShell med administratörsbehörighet.

```powershell
# installs the latest PackageManagement and PowerShellGet packages
Find-Package PackageManagement | Install-Package -Force
Find-Package PowerShellGet | Install-Package -Force

# Restart your powershell session with administrative access

# Install and import the Az.Sql module, then confirm
Install-Module -Name Az.Sql
Import-Module Az.Sql

Get-Module Az.Sql
```

In addition to the **Az.Sql** module, this tutorial also requires the *SqlServer* PowerShell module. Mer information finns i avsnittet om att [installera SQL Server PowerShell-modulen](/sql/powershell/download-sql-server-ps-module).

## <a name="create-required-resources"></a>Skapa nödvändiga resurser

För att skapa elastiska jobbagenter krävs en databas (S0 eller högre) som kan användas som [jobbdatabas](sql-database-job-automation-overview.md#job-database).

The script below creates a new resource group, server, and database for use as the Job database. The second script creates a second server with two blank databases to execute jobs against.

Elastiska jobb har inga särskilda krav för namngivningskonventioner, så du kan använda vilken namnkonvention du vill, förutsatt att de uppfyller något av [kraven för Azure](/azure/architecture/best-practices/resource-naming).

```powershell
# sign in to Azure account
Connect-AzAccount

# create a resource group
Write-Output "Creating a resource group..."
$resourceGroupName = Read-Host "Please enter a resource group name"
$location = Read-Host "Please enter an Azure Region"
$rg = New-AzResourceGroup -Name $resourceGroupName -Location $location
$rg

# create a server
Write-Output "Creating a server..."
$agentServerName = Read-Host "Please enter an agent server name"
$agentServerName = $agentServerName + "-" + [guid]::NewGuid()
$adminLogin = Read-Host "Please enter the server admin name"
$adminPassword = Read-Host "Please enter the server admin password"
$adminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$adminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $adminLogin, $adminPasswordSecure
$agentServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $agentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$agentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$agentServer

# create the job database
Write-Output "Creating a blank SQL database to be used as the Job Database..."
$jobDatabaseName = "JobDatabase"
$jobDatabase = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $agentServerName -DatabaseName $jobDatabaseName -RequestedServiceObjectiveName "S0"
$jobDatabase
```

```powershell
# create a target server and sample databases - uses the same credentials
Write-Output "Creating target server..."
$targetServerName = Read-Host "Please enter a target server name"
$targetServerName = $targetServerName + "-" + [guid]::NewGuid()
$targetServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $targetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set target server firewall rules to allow all Azure IPs
$targetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$targetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$targetServer

# create sample databases to execute jobs against
$db1 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database1"
$db1
$db2 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database2"
$db2
```

## <a name="use-elastic-jobs"></a>Use Elastic Jobs

To use Elastic Jobs, register the feature in your Azure subscription by running the following command. Run this command once for the subscription in which you intend to provision the Elastic Job agent. Subscriptions that only contain databases that are job targets don't need to be registered.

```powershell
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

### <a name="create-the-elastic-job-agent"></a>Skapa en elastiskt jobbagent

En agent för elastiska jobb är en Azure-resurs för att skapa, köra och hantera jobb. Agenten kör jobb baserat på ett schema eller som ett engångsjobb.

The **New-AzSqlElasticJobAgent** cmdlet requires an Azure SQL database to already exist, so the *resourceGroupName*, *serverName*, and *databaseName* parameters must all point to existing resources.

```powershell
Write-Output "Creating job agent..."
$agentName = Read-Host "Please enter a name for your new Elastic Job agent"
$jobAgent = $jobDatabase | New-AzSqlElasticJobAgent -Name $agentName
$jobAgent
```

### <a name="create-the-job-credentials"></a>Create the job credentials

Jobs use database scoped credentials to connect to the target databases specified by the target group upon execution and execute scripts. Dessa databasbegränsade autentiseringsuppgifter används också för att ansluta till huvuddatabasen för att räkna upp alla databaser i en server eller en elastisk pool när någon av dessa används som medlemstyp för målgruppen.

Databasbegränsade autentiseringsuppgifter måste skapas i databasen för jobbet. Alla måldatabaser måste ha en inloggning med tillräcklig behörighet för att slutföra jobbet.

![Autentiseringsuppgifter för elastiska jobb](media/elastic-jobs-overview/job-credentials.png)

Förutom autentiseringsuppgifterna i avbildningen, observera även tillägget av **beviljandekommando** i följande skript. Dessa behörigheter krävs för skriptet vi valde för det här exempeljobbet. Because the example creates a new table in the targeted databases, each target db needs the proper permissions to successfully run.

Kör följande skript för att skapa autentiseringsuppgifter för det nödvändiga jobbet (i jobbdatabasen):

```powershell
# in the master database (target server)
# create the master user login, master user, and job user login
$params = @{
  'database' = 'master'
  'serverInstance' =  $targetServer.ServerName + '.database.windows.net'
  'username' = $adminLogin
  'password' = $adminPassword
  'outputSqlErrors' = $true
  'query' = "CREATE LOGIN masteruser WITH PASSWORD='password!123'"
}
Invoke-SqlCmd @params
$params.query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @params
$params.query = "CREATE LOGIN jobuser WITH PASSWORD='password!123'"
Invoke-SqlCmd @params

# for each target database
# create the jobuser from jobuser login and check permission for script execution
$targetDatabases = @( $db1.DatabaseName, $Db2.DatabaseName )
$createJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$grantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$grantCreateScript = "GRANT CREATE TABLE TO jobuser"

$targetDatabases | % {
  $params.database = $_
  $params.query = $createJobUserScript
  Invoke-SqlCmd @params
  $params.query = $grantAlterSchemaScript
  Invoke-SqlCmd @params
  $params.query = $grantCreateScript
  Invoke-SqlCmd @params
}

# create job credential in Job database for master user
Write-Output "Creating job credentials..."
$loginPasswordSecure = (ConvertTo-SecureString -String "password!123" -AsPlainText -Force)

$masterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $loginPasswordSecure
$masterCred = $jobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $masterCred

$jobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $loginPasswordSecure
$jobCred = $jobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $jobCred
```

### <a name="define-the-target-databases-to-run-the-job-against"></a>Define the target databases to run the job against

En [målgrupp](sql-database-job-automation-overview.md#target-group) utgörs av en eller flera databaser som ett jobbsteg ska köras mot.

The following snippet creates two target groups: *serverGroup*, and *serverGroupExcludingDb2*. *serverGroup* targets all databases that exist on the server at the time of execution, and *serverGroupExcludingDb2* targets all databases on the server, except *targetDb2*:

```powershell
Write-Output "Creating test target groups..."
# create ServerGroup target group
$serverGroup = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$serverGroup | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName

# create ServerGroup with an exclusion of db2
$serverGroupExcludingDb2 = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -Database $db2.DatabaseName -Exclude
```

### <a name="create-a-job-and-steps"></a>Create a job and steps

This example defines a job and two job steps for the job to run. Det första steget i jobbet (*step1*) skapar en ny tabell (*Step1Table*) i varje databas i målgruppen *ServerGroup*. The second job step (*step2*) creates a new table (*Step2Table*) in every database except for *TargetDb2*, because the target group defined previously specified to exclude it.

```powershell
Write-Output "Creating a new job..."
$jobName = "Job1"
$job = $jobAgent | New-AzSqlElasticJob -Name $jobName -RunOnce
$job

Write-Output "Creating job steps..."
$sqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$sqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $serverGroup.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText1
$job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $serverGroupExcludingDb2.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText2
```

### <a name="run-the-job"></a>Kör jobbet

Kör följande kommando direkt för att starta jobbet:

```powershell
Write-Output "Start a new execution of the job..."
$jobExecution = $job | Start-AzSqlElasticJob
$jobExecution
```

Efter varje steg som avslutats korrekt bör du se två nya tabeller i TargetDb1 och bara en ny tabell i TargetDb2:

   ![verifiering av nya tabeller i SSMS](media/elastic-jobs-overview/job-execution-verification.png)

You can also schedule the job to run later. Kör följande kommando för att schemalägga ett jobb så att det körs vid en viss tid:

```powershell
# run every hour starting from now
$job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

### <a name="monitor-status-of-job-executions"></a>Övervaka status för jobbkörningar

Följande kodavsnitt hämtar information om jobbkörning:

```powershell
# get the latest 10 executions run
$jobAgent | Get-AzSqlElasticJobExecution -Count 10

# get the job step execution details
$jobExecution | Get-AzSqlElasticJobStepExecution

# get the job target execution details
$jobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

The following table lists the possible job execution states:

|Status|Beskrivning|
|:---|:---|
|**Created** | The job execution was just created and is not yet in progress.|
|**InProgress** | The job execution is currently in progress.|
|**WaitingForRetry** | The job execution wasn’t able to complete its action and is waiting to retry.|
|**Lyckades** | The job execution has completed successfully.|
|**SucceededWithSkipped** | The job execution has completed successfully, but some of its children were skipped.|
|**Misslyckades** | The job execution has failed and exhausted its retries.|
|**TimedOut** | The job execution has timed out.|
|**Avbrutet** | The job execution was canceled.|
|**Överhoppad** | The job execution was skipped because another execution of the same job step was already running on the same target.|
|**WaitingForChildJobExecutions** | The job execution is waiting for its child executions to complete.|

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort alla resurser som du har skapat i den här självstudien genom att ta bort resursgruppen.

> [!TIP]
> If you plan to continue to work with these jobs, you do not clean up the resources created in this article.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien körde du ett Transact-SQL-skript mot en uppsättning databaser. Du har lärt dig att göra följande:

> [!div class="checklist"]
> * Skapa en elastiskt jobbagent
> * Skapa autentiseringsuppgifter för jobbet så att det kan köra skript på sina mål
> * Definiera mål (servrar, elastiska pooler, databaser, shard-kartor) som du vill köra jobbet mot
> * Skapa autentiseringsuppgifter för databasen i måldatabaserna så att agenten kan ansluta och köra jobb
> * Skapa ett jobb
> * Lägga till ett steg i jobbet
> * Starta en körning av jobbet
> * Övervaka jobbet

> [!div class="nextstepaction"]
> [Hantera elastiska jobb med Transact-SQL](elastic-jobs-tsql.md)
