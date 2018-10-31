---
title: Skapa och hantera elastiska jobb med hjälp av PowerShell | Microsoft Docs
description: PowerShell används för att hantera Azure SQL Database-pooler
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: pwershell
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 06/14/2018
ms.openlocfilehash: 9ed5026211bec11b510d095decac25f8d4b8a52a
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243205"
---
# <a name="create-and-manage-sql-database-elastic-jobs-using-powershell-preview"></a>Skapa och hantera SQL Database elastiska jobb med PowerShell (förhandsversion)


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


PowerShell-APIs för **elastiska databasjobb** (i förhandsversion), kan du definiera en grupp med databaser mot vilken skript ska köras. Den här artikeln visar hur du skapar och hanterar **elastiska databasjobb** med PowerShell-cmdletar. Se [elastiska jobb översikt](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Förutsättningar
* En Azure-prenumeration. En kostnadsfri utvärderingsversion, se [kostnadsfri utvärderingsmånad](https://azure.microsoft.com/pricing/free-trial/).
* En uppsättning databaser som har skapats med verktyg för elastiska databaser. Se [Kom igång med elastiska Databasverktyg](sql-database-elastic-scale-get-started.md).
* Azure PowerShell. Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* **Elastic Database-jobb** PowerShell-paketet: se [installera Elastic Database-jobb](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Välj din Azure-prenumeration
Välj prenumerationen som du behöver ditt prenumerations-Id (**- SubscriptionId**) eller prenumerationsnamn (**- SubscriptionName**). Om du har flera prenumerationer kan du köra den **Get-AzureRmSubscription** cmdlet och kopiera ange den önskade prenumerationsinformationen från resultatet. När du har din prenumerationsinformation, kör du följande cmdlet för att ange den här prenumerationen som standard, nämligen målet för att skapa och hantera jobb:

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

Den [PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) rekommenderas för användning att utveckla och köra PowerShell-skript mot Elastic Database-jobb.

## <a name="elastic-database-jobs-objects"></a>Elastiska jobb databasobjekt
I följande tabell visas ut alla objekttyper för **elastiska databasjobb** tillsammans med dess beskrivning och relevanta PowerShell APIs.

<table style="width:100%">
  <tr>
    <th>Objekttyp</th>
    <th>Beskrivning</th>
    <th>Relaterade PowerShell API: er</th>
  </tr>
  <tr>
    <td>Autentiseringsuppgift</td>
    <td>Användarnamn och lösenord ska användas vid anslutning till databaser för körning av skript eller ett program av DACPACs. <p>Lösenordet krypteras innan du skickar till och lagra i databasen för Elastic Database-jobb.  Lösenordet dekrypteras av tjänsten Elastic Database-jobb via autentiseringsuppgifter har skapat och överfört från installationsskriptet.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Skript</td>
    <td>Transact-SQL-skript som ska användas för körning på databaser.  Skriptet ska skapas för att vara idempotent eftersom tjänsten kommer att försöka igen körningen av skriptet vid fel.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Dataskiktsprogram </a> paket som ska användas på databaser.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Mål för databasen</td>
    <td>Namn på databas- och som pekar på en Azure SQL Database.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Shard kartan mål</td>
    <td>Kombinationen av ett mål för databas och en autentiseringsuppgift som ska användas för att avgöra information som lagras i en Elastic Database-fragmentkartan.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    <p>Set-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Mål för anpassad insamling</td>
    <td>Definierade grupp med databaser som ska användas tillsammans för körning.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Anpassad insamling underordnade mål</td>
    <td>Databas-mål som refereras till från en anpassad samling.</td>
    <td>
    <p>Add-AzureSqlJobChildTarget</p>
    <p>Remove-AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Jobb</td>
    <td>
    <p>Definition av parametrar för ett jobb som kan användas för att utlösa körningen eller uppfylla ett schema.</p>
    </td>
    <td>
    <p>Get-AzureSqlJob</p>
    <p>New-AzureSqlJob</p>
    <p>Set-AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>Jobbkörning</td>
    <td>
    <p>Behållare för nödvändiga åtgärder för att uppfylla antingen köra ett skript eller tillämpa en DACPAC till ett mål med hjälp av autentiseringsuppgifter för databasanslutningar med fel som hanteras i enlighet med en körningsprincip.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Jobbaktiviteter</td>
    <td>
    <p>Arbetsenhet att slutföra ett jobb.</p>
    <p>Om en projektaktivitet inte kan har kör, resulterande Undantagsmeddelandet loggas och en ny uppgift för matchande jobbet skapas och körs i enlighet med den angivna körningsprincipen.</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Körningsprincip för jobbet</td>
    <td>
    <p>Kontroller jobb körning tidsgränser, gränsen för återförsök och intervall mellan försök.</p>
    <p>Elastic Database-jobb innehåller ett jobb RemoteSigned vilket leder till att i princip oändlig försök för misslyckade aktiviteter för jobbet med exponentiell backoff intervall mellan varje nytt försök.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecutionPolicy</p>
    <p>New-AzureSqlJobExecutionPolicy</p>
    <p>Set-AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>Schema</td>
    <td>
    <p>Tid baserat specifikationen för körning ska kunna utföras på ett återkommande intervall eller på en gång.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>New-AzureSqlJobSchedule</p>
    <p>Set-AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>Jobbet utlöser</td>
    <td>
    <p>En mappning mellan ett jobb och ett schema för att utlösaren jobbkörningen enligt schemat.</p>
    </td>
    <td>
    <p>New-AzureSqlJobTrigger</p>
    <p>Remove-AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Stöds elastiska databasjobb gruppen typer
Jobbet körs Transact-SQL (T-SQL)-skript eller program för DACPACs för en grupp med databaser. När ett jobb skickas som ska köras i en grupp med databaser jobbet ”expanderar” den i underordnade jobb där vart och ett utför begärda körning mot en enskild databas i gruppen. 

Det finns två typer av grupper som du kan skapa: 

* [Fragmentkartan](sql-database-elastic-scale-shard-map-management.md) grupp: när ett jobb skickas för att rikta en skärvkarta, jobbet frågar fragmentkartan för att fastställa den aktuella uppsättningen shards och skapar sedan underordnat jobb för varje fragment i fragmentkartan.
* Samlingsgruppen för anpassade: en anpassad definierad uppsättning databaser. När ett jobb riktar sig mot en anpassad samling, skapar den underordnade jobb för varje databas för närvarande i anpassade samlingen.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Om du vill ange Elastic Database-jobb anslutning
En anslutning måste vara inställt för jobben *kontrolldatabas* innan du börjar använda jobb API: er. Kör denna cmdlet utlöser ett fönster för autentiseringsuppgifter att visas som begär användarnamn och lösenord som skapas när du installerar Elastic Database-jobb. Alla exempel som är tillgängliga i det här avsnittet förutsätter att det här första steget redan har utförts.

Öppna en anslutning till Elastic Database-jobb:

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Krypterade autentiseringsuppgifter i Elastic Database-jobb
Databasautentiseringsuppgifter kan infogas i jobben *kontrolldatabas* med dess lösenord krypteras. Det är nödvändigt att lagra autentiseringsuppgifter för att aktivera jobb som ska köras vid ett senare tillfälle (med jobbscheman).

Kryptering fungerar via ett certifikat som skapas som en del av installationen. Installationsskriptet skapar och laddar upp certifikatet i Azure-molntjänst för dekryptering av lagrade krypterade lösenord. Azure Cloud Service senare lagrar den offentliga nyckeln i jobben *kontrolldatabas* vilket gör att PowerShell API: et eller Azure portalgränssnitt att kryptera ett angivet lösenord utan att certifikatet som ska installeras lokalt .

Credential-lösenord är krypterad och säkra från användare med skrivskyddad åtkomst till objekt för Elastic Database-jobb. Men det är möjligt för en obehörig användare med läsåtkomst till elastiska Databasjobb objekt att extrahera ett lösenord. Autentiseringsuppgifterna är utformade för att återanvändas i jobbkörningar. Autentiseringsuppgifterna skickas till måldatabaserna när upprättar anslutningar. Det finns för närvarande inga begränsningar för måldatabaserna används för varje autentiseringsuppgifter, obehörig användare kan lägga till ett mål för databasen för en databas under den illasinnade användaren kontroll. Användaren kan sedan starta ett jobb som riktar in sig på den här databasen för att få lösenord för de autentiseringsuppgifter.

Rekommenderade säkerhetsmetoder för Elastic Database-jobb är:

* Begränsa användningen av API:er till betrodda personer.
* Autentiseringsuppgifterna ska ha så få behörigheter som behövs för att utföra projektaktiviteten.  Mer information kan visas på den här [auktorisering och behörigheter](https://msdn.microsoft.com/library/bb669084.aspx) SQL Server MSDN-artikeln.

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Så här skapar du en krypterade autentiseringsuppgifter för jobbkörning mellan databaser
Att skapa en ny krypterade autentiseringsuppgifter i [ **cmdlet Get-Credential** ](/powershell/module/microsoft.powershell.security/get-credential) frågar efter ett användarnamn och lösenord som kan skickas till den [ **cmdlet New-AzureSqlJobCredential** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcredential).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Uppdatera autentiseringsuppgifter
När du ändrar lösenord, använda den [ **cmdlet Set-AzureSqlJobCredential** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcredential) och ange den **CredentialName** parametern.

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Definiera ett mål för Elastic Database fragment karta
Att köra ett jobb mot alla databaser i en shard (skapats med hjälp av [Elastic Database-klientbiblioteket](sql-database-elastic-database-client-library.md)), använder en skärvkarta som mål för databasen. Det här exemplet kräver ett shardat program som skapats med klientbiblioteket för elastiska databaser. Se [komma igång med Elastic Database-verktyg exempel](sql-database-elastic-scale-get-started.md).

Databasen måste anges som ett mål för databasen och sedan de specifika fragmentkartan måste anges som mål.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Skapa ett T-SQL-skript för körning på databaser
När du skapar T-SQL-skript för körning kan vi rekommenderar starkt att skapa dem för att vara [idempotenta](https://en.wikipedia.org/wiki/Idempotence) och vara motståndskraftiga mot fel. Elastic Database-jobb försöker köra ett skript när körningen påträffar ett fel, oavsett klassificeringen av felet.

Använd den [ **cmdlet New-AzureSqlJobContent** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent) att skapa och spara ett skript för körning och ange den **- ContentName** och **- CommandText** parametrar.

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>Skapa ett nytt skript från en fil
Om T-SQL-skript har definierats i en fil kan du använda detta för att importera skriptet:

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Uppdatera ett T-SQL-skript för körning på databaser
Det här PowerShell-skriptet uppdaterar T-SQL-Kommandotext för ett befintligt skript.

Ange följande variabler för att återspegla den önskade skript definitionen anges:

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>Att uppdatera definitionen till ett befintligt skript
    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Att skapa ett jobb för att köra ett skript i en skärvkarta
Det här PowerShell-skriptet startar ett jobb för körning av ett skript i varje shard i en elastisk skalning fragmentkartan.

Ange följande variabler på önskad skript och mål:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>Att köra ett jobb
Det här PowerShell-skriptet körs ett befintligt jobb:

Uppdatera följande variabel för att återspegla det önskade Jobbnamnet för att har genomfört:

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution

## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Att hämta tillståndet för en enskild jobbkörning
Använd den [ **cmdlet Get-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/get-azuresqljobexecution) och ange den **JobExecutionId** parameter för att visa status för jobbkörningen.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Använd samma **Get-AzureSqlJobExecution** cmdlet med den **IncludeChildren** parameter för att visa status för underordnade jobbkörningar, nämligen ett visst tillstånd för varje jobbkörning mot varje databas mål för jobbet.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Visa tillstånd över flera jobbkörningar
Den [ **cmdlet Get-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) har flera valfria parametrar som kan användas för att visa flera jobbkörningar filtreras via de angivna parametrarna. Nedan visas några av de möjliga sätten att använda Get-AzureSqlJobExecution:

Hämta alla aktiva översta nivån jobbkörningar:

    Get-AzureSqlJobExecution

Hämta alla översta nivån jobbkörningar, inklusive inaktiva jobbkörningar:

    Get-AzureSqlJobExecution -IncludeInactive

Hämta alla underordnade jobbkörningar av en tillhandahållna jobbet körnings-ID, inklusive inaktiva jobbkörningar:

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren

Hämta alla jobbkörningar som skapats med hjälp av ett schema / jobb tillsammans med inaktiva jobb:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Hämta alla jobb som riktar in sig på en angiven fragmentkartan, inklusive inaktiva jobb:

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Hämta alla jobb som riktar in sig på en angiven anpassade samling, inklusive inaktiva jobb:

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Hämta listan över uppgiften jobbkörningar i en specifik jobbkörningen:

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Hämta information om jobbkörningar uppgift:

Följande PowerShell-skript kan användas för att visa information om jobbaktiviteter, vilket är särskilt användbart när du felsöker fel vid körning.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="to-retrieve-failures-within-job-task-executions"></a>Att hämta fel i aktiviteten jobbkörningar
Den **JobTaskExecution objekt** innehåller en egenskap för livscykeln för uppgiften tillsammans med en meddelandeegenskap. Om ett jobb för körning av aktiviteten misslyckades livscykel egenskapen sätts till *misslyckades* och meddelandeegenskapen anges till det resulterande Undantagsmeddelandet och dess stack. Om ett jobb inte lyckades, är det viktigt att visa information om jobbuppgifter som misslyckades för ett visst jobb.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>Vänta tills ett jobbkörning ska slutföras
Följande PowerShell-skript kan användas för att vänta tills åtgärden ett jobb har slutförts:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Skapa en princip för anpassad körning
Elastic Database-jobb kan du skapa anpassade körningsprinciper som kan användas när du startar jobb.

För närvarande tillåter körningsprinciper för att definiera:

* Namn: Identifierare för körningsprincipen.
* Tidsgräns för jobb: Total tid innan ett jobb avbryts av Elastic Database-jobb.
* Inledande återförsöksintervallet: Intervall ska gå innan nytt försök görs.
* Maximalt återförsöksintervall: Tak för återförsöksintervall att använda.
* Gör om intervallet Backoff koefficienten: Koefficienten som används för att beräkna nästa intervall mellan försök.  Följande formel används: (inledande återförsöksintervallet) * Math.pow ((intervall Backoff koefficienten) (antal nya försök) – 2). 
* Maximalt antal försök: Det maximala antalet återförsök försöker utföra i ett jobb.

RemoteSigned använder följande värden:

* Namn: RemoteSigned
* Tidsgräns för jobb: 1 vecka
* Inledande återförsöksintervallet: 100 millisekunder
* Maximalt återförsöksintervall: 30 minuter
* Försök intervall koefficienten: 2
* Maximalt antal försök: 2 147 483 647

Skapa önskade körningsprincipen:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>Uppdatera en anpassad körningsprincipen
Uppdatera önskade körningsprincipen att uppdatera:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy

## <a name="cancel-a-job"></a>Avbryta ett jobb
Elastic Database-jobb har stöd för begäranden om annullering av jobb.  Om elastiska Databasjobb upptäcker en avbrottsbegäran för ett jobb som körs, försöker den att stoppa jobbet.

Det finns två olika sätt att Elastic Database-jobb kan utföra en uppsägning:

1. Avbryt som för närvarande kör uppgifter: om ett avbrott identifieras när en aktivitet körs för närvarande en uppsägning görs inom den körs för närvarande aspekten av uppgiften.  Till exempel: om det finns en tidskrävande fråga som för närvarande utförs när en uppsägning görs, blir det ett försök att avbryta frågan.
2. Avbryter omförsök: om ett avbrott har identifierats av kontroll tråd innan en aktivitet startas för körning, kontroll tråden ska undvika att starta uppgiften och deklarera begäran som har avbrutits.

Om ett jobb avbrott har begärts för ett överordnat jobb, kan på avbrottsbegäran användas för det överordnade jobbet och alla dess underordnade jobb.

För att skicka en begäran om annullering, använda den [ **cmdlet Stop-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) och ange den **JobExecutionId** parametern.

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Ta bort ett jobb och Jobbhistorik asynkront
Elastic Database-jobb har stöd för asynkrona borttagning av jobb. Ett jobb kan markeras för borttagning och systemet tar bort jobbet och alla dess jobbhistorik när alla jobbkörningar har slutfört för projektet. Systemet avbryts inte automatiskt active jobbkörningar.  

Anropa [ **Stop-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) annullera active jobbkörningar.

För att utlösa jobbet tas bort, Använd den [ **cmdlet Remove-AzureSqlJob** ](/powershell/module/elasticdatabasejobs/remove-azuresqljob) och ange den **JobName** parametern.

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName

## <a name="to-create-a-custom-database-target"></a>Skapa en anpassad databas-mål
Du kan definiera anpassad databas mål för körning av direkt eller ska ingå i en anpassad databas-grupp. Till exempel eftersom **elastiska pooler** är stöds inte än direkt med hjälp av PowerShell APIs, kan du skapa en anpassad databas mål och anpassad databas samling mål som omfattar alla databaser i poolen.

Ange följande variabler för att återspegla den önskade databasinformationen:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Skapa en anpassad databas samling mål
Använd den [ **New-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) cmdlet för att definiera en anpassad databas samling mål för att aktivera körning över flera definierade databasen mål. När du har skapat en grupp kan databaser vara kopplat till målet för anpassad insamling.

Ange följande variabler på önskad anpassad samling target konfiguration:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Att lägga till databaser till ett mål för insamling av anpassad databas
Att lägga till en databas till en specifik anpassade samling använder den [ **Lägg till AzureSqlJobChildTarget** ](/powershell/module/elasticdatabasejobs/add-azuresqljobchildtarget) cmdlet.

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Granska databaserna i en anpassad databas samling mål
Använd den [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) cmdlet för att hämta underordnade databaserna i en anpassad databas samling mål. 

    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Skapa ett jobb för att köra ett skript i en anpassad databas samling mål
Använd den [ **New-AzureSqlJob** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) cmdlet för att skapa ett jobb mot en grupp med databaser som definieras av en anpassad databas samling mål. Elastic Database-jobb kommer Expandera jobbet till flera underordnade jobb varje motsvarar en databas som är associerade med anpassad databas samling mål och se till att skriptet körs mot varje databas. Igen, är det viktigt att skripten är idempotenta för att hantera att återförsök.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Insamling av data mellan databaser
Du kan använda ett jobb för att köra en fråga över en grupp med databaser och skicka resultaten till en viss tabell. Tabellen kan frågas efter faktumet att se resultatet av frågan från varje databas. Detta ger en asynkron metod för att köra en fråga över flera databaser. Misslyckade försök hanteras automatiskt via återförsök.

Den angivna tabellen skapas automatiskt om det inte finns ännu. Den nya tabellen matchar schemat för den returnerade resultatuppsättningen. Om ett skript returnerar flera resultatmängder, skickas bara först till måltabellen i Elastic Database-jobb.

Följande PowerShell-skript körs ett skript och samlar in resultaten till en angiven tabell. Det här skriptet förutsätter att en T-SQL-skript har skapats som matar ut en enda resultatmängd och att ett mål för insamling av anpassad databas har skapats.

Det här skriptet använder den [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) cmdlet. Ange parametrar för skriptet, autentiseringsuppgifter och körningsmål:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Skapa och starta ett jobb för scenarier för insamling av data
Det här skriptet använder den [ **Start AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/start-azuresqljobexecution) cmdlet.

    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>Så här schemalägger du en utlösare för körning av jobb
Följande PowerShell-skript kan användas för att skapa ett återkommande schema. Det här skriptet använder ett minuters intervall, men [ **New-AzureSqlJobSchedule** ](/powershell/module/elasticdatabasejobs/new-azuresqljobschedule) stöder också - DayInterval, - HourInterval, - MonthInterval, och -WeekInterval parametrar. Scheman som kör bara en gång kan skapas av skicka - genomfört.

Skapa ett nytt schema:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>Att utlösa ett jobb som körs på en tidsplan
En utlösare för jobbet kan definieras för ett jobb som körs enligt ett schema. Följande PowerShell-skript kan användas för att skapa en utlösare för jobbet.

Använd [New AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/new-azuresqljobtrigger) och Ställ in följande variabler på motsvarar önskad jobbet och schema:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    -JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Ta bort en schemalagd association att avbryta jobbet från att köras på schema
Jobbet utlösaren kan tas bort för att avbryta körningen av återkommande jobb via en utlösare för jobbet. Ta bort jobbet utlösaren om du vill stoppa ett jobb från som körs enligt ett schema med hjälp av den [ **cmdlet Remove-AzureSqlJobTrigger**](/powershell/module/elasticdatabasejobs/remove-azuresqljobtrigger).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Hämta jobb utlösare som är bunden till en tidsplan
Följande PowerShell-skript kan användas för att hämta och visa jobb-utlösare som registrerats för en viss tidsplan.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>För att hämta jobb utlösare som är bunden till ett jobb
Använd [Get-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/get-azuresqljobtrigger) att hämta och visa scheman som innehåller ett registrerade jobb.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Skapa ett program för datanivå (DACPAC) för körning på databaser
Om du vill skapa en DACPAC [datanivåprogram](https://msdn.microsoft.com/library/ee210546.aspx). Om du vill distribuera en DACPAC använder den [cmdlet New-AzureSqlJobContent](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent). DACPAC måste vara tillgänglig för tjänsten. Det rekommenderas att ladda upp en skapade DACPAC till Azure Storage och skapa en [signatur för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md) för DACPAC.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Uppdatera ett program för datanivå (DACPAC) för körning på databaser
Befintliga DACPACs som inträffar inom Elastic Database-jobb kan uppdateras för att peka mot nya URI: er. Använd den [ **cmdlet Set-AzureSqlJobContentDefinition** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcontentdefinition) uppdatera DACPAC URI på en befintlig registrerade DACPAC:

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Att skapa ett jobb för att tillämpa ett dataskiktsprogram (DACPAC) mellan databaser
När du har skapat en DACPAC i Elastic Database-jobb kan du skapa ett jobb för att tillämpa DACPAC för en grupp med databaser. Följande PowerShell-skript kan användas för att skapa ett DACPAC-jobb i en anpassad samling databaser:

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
