---
title: "Skapa och hantera elastiska jobb med hjälp av PowerShell | Microsoft Docs"
description: "PowerShell som används för att hantera Azure SQL Database-pooler"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
ms.assetid: 737d8d13-5632-4e18-9cb0-4d3b8a19e495
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 12ace2ff3bcb967ec5e0ae88d3ce79a53836dd5e
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="create-and-manage-sql-database-elastic-jobs-using-powershell-preview"></a>Skapa och hantera SQL Database: elastiska jobb med hjälp av PowerShell (förhandsgranskning)

PowerShell-APIs för **elastisk databas jobb** (under förhandsgranskning) gör att du kan definiera en grupp databaser mot vilken skript körs. Den här artikeln visar hur du skapar och hanterar **elastisk databas jobb** med PowerShell-cmdlets. Se [elastiska jobb översikt](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Krav
* En Azure-prenumeration. För en kostnadsfri utvärderingsversion finns [kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).
* En uppsättning databaser som skapats med elastiska Databasverktyg. Se [Kom igång med elastiska Databasverktyg](sql-database-elastic-scale-get-started.md).
* Azure PowerShell. Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* **Den elastiska databasen jobb** PowerShell paket: finns [jobb installerar elastisk databas](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Välj din Azure-prenumeration
Välj prenumerationen du behöver ditt prenumerations-Id (**- SubscriptionId**) eller prenumerationsnamn (**- SubscriptionName**). Om du har flera prenumerationer kan du köra den **Get-AzureRmSubscription** cmdlet och kopiera önskade prenumerationsinformation från resultatet anges. När du har din prenumerationsinformation kör du följande cmdlet för att ange den här prenumerationen som standard, nämligen mål för att skapa och hantera jobb:

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

Den [PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) rekommenderas att utveckla och köra PowerShell-skript mot jobb för elastisk databas.

## <a name="elastic-database-jobs-objects"></a>Elastiska jobb databasobjekt
I följande tabell visas ut alla objekttyper för **elastisk databas jobb** tillsammans med dess beskrivning och relevanta PowerShell APIs.

<table style="width:100%">
  <tr>
    <th>Objekttyp</th>
    <th>Beskrivning</th>
    <th>Relaterade PowerShell API: er</th>
  </tr>
  <tr>
    <td>Autentiseringsuppgift</td>
    <td>Användarnamn och lösenord för att ansluta till databaser för körning av skript eller ett program av DACPACs. <p>Lösenordet krypteras innan du skickar till och lagra i databasen för den elastiska databasen jobb.  Lösenordet dekrypteras av tjänsten elastiska databasen jobb via autentiseringsuppgifter skapas och som överförts från ett installationsskript.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>Ny AzureSqlJobCredential</p><p>Ange AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Skript</td>
    <td>Transact-SQL-skript som ska användas för körning över databaser.  Skriptet ska skapas för att vara idempotent eftersom tjänsten kommer att försöka körningen av skriptet vid fel.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>Ny AzureSqlJobContent</p>
    <p>Ange AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Programmet på datanivå </a> paket som ska tillämpas över databaser.

    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Ny AzureSqlJobContent</p>
    <p>Ange AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Databasen mål</td>
    <td>Namn för databasen och pekar på en Azure SQL Database.

    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Ny AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Fragmentera kartan mål</td>
    <td>Kombinationen av target databas och autentiseringsuppgifter som används för att avgöra information som lagras i en elastisk databas Fragmentera mappning.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Ny AzureSqlJobTarget</p>
    <p>Ange AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Mål för anpassad insamling</td>
    <td>Angiven grupp databaser som ska användas tillsammans för att köras.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Ny AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Anpassad samling underordnade mål</td>
    <td>Mål för databasen som refereras från en anpassad samling.</td>
    <td>
    <p>Lägg till AzureSqlJobChildTarget</p>
    <p>Ta bort AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Jobb</td>
    <td>
    <p>Definition av parametrar för ett jobb som kan användas för att starta körningen eller för att uppfylla ett schema.</p>
    </td>
    <td>
    <p>Get-AzureSqlJob</p>
    <p>Ny AzureSqlJob</p>
    <p>Ange AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>Jobbkörningen</td>
    <td>
    <p>Behållare för aktiviteter som krävs för att uppfylla antingen köra ett skript eller använda en DACPAC till ett mål med autentiseringsuppgifter för databasanslutningar med fel hanteras i enlighet med en körningsprincip.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stoppa AzureSqlJobExecution</p>
    <p>Vänta AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Jobb för körning av aktiviteten</td>
    <td>
    <p>Arbetsenhet att slutföra ett jobb.</p>
    <p>Om en projektaktivitet inte kan har köra, resulterande Undantagsmeddelande loggas och en ny matchande projektaktivitet skapas och körs i enlighet med angiven körningsprincipen.</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stoppa AzureSqlJobExecution</p>
    <p>Vänta AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Jobbet körningsprincipen</td>
    <td>
    <p>Kontroller jobbet körning timeout, försök gränser och intervall mellan försök.</p>
    <p>Den elastiska databasen jobb innehåller en körningsprincip för standard-jobb som orsakar i princip obegränsat återförsök uppgiften jobbfel med exponentiell backoff intervall mellan varje försök.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecutionPolicy</p>
    <p>Ny AzureSqlJobExecutionPolicy</p>
    <p>Ange AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>Schema</td>
    <td>
    <p>Tid baserat specifikationen för körning ska ske på ett reoccurring intervall eller på en gång.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>Ny AzureSqlJobSchedule</p>
    <p>Ange AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>Jobbet utlöser</td>
    <td>
    <p>En mappning mellan ett jobb och ett schema för att utlösaren jobb enligt schemat.</p>
    </td>
    <td>
    <p>Ny AzureSqlJobTrigger</p>
    <p>Ta bort AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Stöds för elastisk databas jobb gruppera typer
Jobbet körs Transact-SQL (T-SQL)-skript eller ett program av DACPACs över flera databaser. När ett jobb skickas för att köra över en grupp databaser jobbet ”utökar” den till underordnade jobb där varje utför begärda körning mot en enskild databas i gruppen. 

Det finns två typer av grupper som du kan skapa: 

* [Fragmentera kartan](sql-database-elastic-scale-shard-map-management.md) grupp: när ett jobb skickas för att rikta en Fragmentera karta jobbet frågar Fragmentera kartan för att fastställa den aktuella mängden shards och skapar sedan underordnade jobb för varje Fragmentera i kartan Fragmentera.
* Samlingsgruppen för anpassade: en anpassad definierad uppsättning databaser. När ett jobb riktar sig till en anpassad samling, skapar den underordnade jobb för varje databas för närvarande i anpassade samlingar.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Om du vill ange den elastiska databasen jobb anslutning
En anslutning måste anges till jobben *databasen* innan du använder jobb API: er. Kör denna cmdlet utlöser ett fönster för autentiseringsuppgifter att popup-begär användarnamn och lösenord som skapas när du installerar elastisk databas jobb. Alla exemplen i det här avsnittet förutsätter att det här första steget redan har utförts.

Öppna en anslutning till elastisk databas jobb:

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Krypterade autentiseringsuppgifter i jobb för elastisk databas
Databasautentiseringsuppgifter kan infogas i jobben *databasen* med dess lösenord krypteras. Det är nödvändigt att lagra autentiseringsuppgifter för att aktivera jobb som ska utföras vid ett senare tillfälle (med jobbscheman).

Kryptering fungerar via ett certifikat som skapas som en del av installationen. Installationsskriptet skapar och laddar upp certifikatet i Azure Cloud Service för dekryptering av lagrade krypterade lösenord. Azure Cloud Service senare lagrar den offentliga nyckeln i jobben *databasen* som gör det möjligt för gränssnittet PowerShell API eller klassiska Azure-portalen att kryptera en lösenordet utan att certifikatet ska vara lokalt installerad.

Autentiseringsuppgifter lösenord är krypterad och säkra från användare med läsåtkomst till elastiska jobb databasobjekt. Men det är möjligt för en obehörig användare med skrivskyddad åtkomst till den elastiska databasen jobb objekt att extrahera ett lösenord. Autentiseringsuppgifterna är avsedda att återanvändas i jobbet körningar. Autentiseringsuppgifter skickas till måldatabaserna när anslutningar upprättas. Det finns för närvarande inga begränsningar för måldatabaserna används för varje autentiseringsuppgifter, obehörig användare kan lägga till databasen mål för en databas med skadliga användarkontrollen. Användaren kan sedan starta ett jobb som den här databasen för att få lösenord för de autentiseringsuppgifter som mål.

Rekommenderade säkerhetsmetoder för elastisk databas jobb är:

* Begränsa användning av API: er till betrodda personer.
* Autentiseringsuppgifter bör ha minst behörighet att utföra åtgärden för jobbet.  Mer information kan ses i detta [auktoriserings- och behörigheter](https://msdn.microsoft.com/library/bb669084.aspx) SQL Server MSDN-artikel.

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Så här skapar du en krypterade autentiseringsuppgifter för jobbkörningen över databaser
Så här skapar du en ny krypterade autentiseringsuppgifter i [ **cmdlet Get-Credential** ](https://technet.microsoft.com/library/hh849815.aspx) uppmanas att ange ett användarnamn och lösenord som kan skickas till den [ **cmdlet New-AzureSqlJobCredential** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcredential).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Uppdatera autentiseringsuppgifterna
När du ändrar lösenord, använda den [ **cmdlet Set-AzureSqlJobCredential** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcredential) och ange den **CredentialName** parameter.

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Definiera ett mål för elastisk databas Fragmentera karta
Att köra ett jobb för alla databaser i en Fragmentera (skapat med hjälp av [klientbibliotek för elastisk databas](sql-database-elastic-database-client-library.md)), Använd en Fragmentera som mål för databasen. Det här exemplet kräver ett delat program som skapats med hjälp av klientbiblioteket för elastisk databas. Se [komma igång med elastisk databas verktyg exempel](sql-database-elastic-scale-get-started.md).

Fragmentera kartan manager-databasen måste anges som ett mål för databasen och sedan kartan specifika Fragmentera måste anges som mål.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Skapa ett T-SQL-skript för körning på databaser
När du skapar T-SQL-skript för körning, rekommenderas att skapa dem för att vara [idempotent](https://en.wikipedia.org/wiki/Idempotence) och motståndskraftiga mot fel. Den elastiska databasen jobb kommer att försöka körningen av ett skript när körningen påträffar ett fel, oavsett klassificeringen av felet.

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
Om T-SQL-skript har definierats i en fil, använder du detta för att importera skriptet:

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Uppdatera ett T-SQL-skript för körning över databaser
Det här PowerShell-skriptet uppdaterar T-SQL-kommandotexten för ett befintligt skript.

Ange följande variabler återspeglar önskade skript definition anges:

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

### <a name="to-update-the-definition-to-an-existing-script"></a>Uppdatera definitionen till ett befintligt skript
    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Att skapa ett jobb för att köra ett skript på en Fragmentera karta
Detta PowerShell-skript startar ett jobb för körning av ett skript på varje Fragmentera i en elastisk skalbarhet Fragmentera mappning.

Ange följande variabler återspeglar önskade skript och mål:

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

Uppdatera följande variabel för att återspegla önskade Jobbnamnet som har köras:

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution

## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Att hämta tillståndet för en enskild jobbkörningen
Använd den [ **cmdlet Get-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/get-azuresqljobexecution) och ange den **JobExecutionId** parametern för att visa status för jobbkörningen.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Använda samma **Get-AzureSqlJobExecution** med den **IncludeChildren** parametern för att visa status för underordnade jobbet körningar, nämligen ett visst tillstånd för varje jobbkörningen mot varje databas som mål för jobbet.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Visa status över flera jobb körningar
Den [ **cmdlet Get-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) flera valfria parametrar som kan användas för att visa flera jobb körningar, filtreras via de angivna parametrarna. Följande visar några av de möjliga sätt att använda Get-AzureSqlJobExecution:

Hämta alla aktiva översta nivå jobbet körningar:

    Get-AzureSqlJobExecution

Hämta alla jobb för övre nivå körningar, inklusive inaktiva jobb körningar:

    Get-AzureSqlJobExecution -IncludeInactive

Hämta alla underordnade jobbet körningar av en tillhandahållna jobbet körnings-ID, inklusive inaktiva jobb körningar:

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren

Hämta alla jobb körningar som skapats med hjälp av ett schema / jobb tillsammans med inaktiva jobb:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Hämta alla jobb som mål för en angiven Fragmentera karta, inklusive inaktiva jobb:

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Hämta alla jobb som mål för en angiven anpassad samling, inklusive inaktiva jobb:

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Hämta listan över jobb uppgiften körningar inom en specifik jobbkörningen:

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Hämta jobb aktivitetsinformation körning:

Följande PowerShell-skript kan användas för att visa information om ett jobb för körning av aktiviteten, vilket är särskilt användbart när körningen felsökningsändamål.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="to-retrieve-failures-within-job-task-executions"></a>Att hämta fel i jobb uppgiften körningar
Den **JobTaskExecution objekt** innehåller en egenskap för livscykeln för uppgiften tillsammans med en meddelandeegenskap. Om ett jobb för körning av aktiviteten misslyckades livscykel egenskap anges *misslyckades* och meddelandeegenskapen kommer att anges till det resulterande Undantagsmeddelandet och dess stacken. Om ett jobb misslyckades, är det viktigt att visa information om jobbuppgifter som misslyckades för ett visst jobb.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>Vänta på ett jobbkörning ska slutföras
Följande PowerShell-skript kan användas för att vänta på en projektaktivitet att slutföra:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Skapa en princip för anpassad körning
Den elastiska databasen jobb kan du skapa anpassade körningsprinciper som kan användas när du startar jobb.

Körningsprinciper tillåter för närvarande för att definiera:

* Namn: Identifierare för körningsprincipen.
* Tidsgräns för jobb: Total tid innan ett jobb kommer att avbrytas av elastiska databasen jobb.
* Inledande återförsöksintervall: Intervall ska vänta innan nytt försök görs.
* Maximal återförsöksintervall: Locket återförsöksintervall ska användas.
* Försök intervall Backoff värde: Värde används för att beräkna nästa intervall mellan försök.  Följande formel används: (första försök intervall) * Math.pow ((intervall Backoff värde) (antal nya försök) - 2). 
* Maximalt antal försök: Maximalt antal försök försöker utföra inom ett jobb.

Standard-körningsprincipen används följande värden:

* Namn: Standardprincipen för körning
* Tidsgräns för jobb: 1 vecka
* Inledande återförsöksintervall: 100 millisekunder
* Maximal återförsöksintervall: 30 minuter
* Försök intervall värde: 2
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

### <a name="update-a-custom-execution-policy"></a>Uppdatera en anpassad körningsprincip
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
Den elastiska databasen jobb stöder förfrågningar om annullering av jobb.  Om den elastiska databasen jobb upptäcker en begäran om att avbryta ett jobb som körs, försöker den att stoppa jobbet.

Det finns två olika sätt att elastiska databasen jobb kan utföra en annullering:

1. Avbryt aktiviteter som körs: om en annullering identifieras när en aktivitet körs för närvarande en annullering görs inom körs aspekt av aktiviteten.  Exempel: om det finns en tidskrävande fråga som för närvarande utförs när en annullering görs, är ett försök att avbryta frågan.
2. Om du avbryter återförsök för aktiviteten: om en annullering identifieras av kontrollen tråd innan en uppgift startas för körning tråden kontrollen ska undvika starta uppgiften och deklarera begäran som avbruten.

Om det krävs en annullering av jobbet för överordnade jobb respekteras begäran om att avbryta för överordnade jobb och alla dess underordnade jobb.

För att skicka en begäran om att avbryta, Använd den [ **cmdlet Stop AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) och ange den **JobExecutionId** parameter.

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Ta bort ett jobb och Jobbhistorik asynkront
Den elastiska databasen jobb stöder asynkrona borttagning av jobb. Ett jobb kan vara markerad för borttagning och tas bort jobbet och alla dess jobbhistorik när alla jobb körningar har slutförts för jobbet. Systemet Avbryt inte automatiskt aktiva jobb körningar.  

Anropa [ **stoppa AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) att avbryta aktiva jobb körningar.

Utlös borttagning av jobbet genom att använda den [ **cmdlet Remove-AzureSqlJob** ](/powershell/module/elasticdatabasejobs/remove-azuresqljob) och ange den **jobbnamn** parameter.

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName

## <a name="to-create-a-custom-database-target"></a>Så här skapar du en anpassad databas mål
Du kan definiera anpassade databasen mål för att köra en direkt eller som ska ingå i en anpassad databas-grupp. Till exempel eftersom **elastiska pooler** är stöds inte än direkt med hjälp av PowerShell APIs, kan du skapa en anpassad databas mål och anpassad databas samling mål som omfattar alla databaser i poolen.

Ange följande variabler återspeglar den önskade databasinformationen:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Så här skapar du en anpassad databas samling mål
Använd den [ **ny AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) för att definiera en anpassad databas samling mål för att aktivera körningen över flera definierade databasen mål. När du har skapat en databasgrupp vara databaser kopplat till målet för anpassad insamling.

Ange följande variabler önskade anpassade samlingar mål konfiguration:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Att lägga till databaser till en anpassad databas samling mål
Att lägga till en databas till en specifik egen samling använder den [ **Lägg till AzureSqlJobChildTarget** ](/powershell/module/elasticdatabasejobs/add-azuresqljobchildtarget) cmdlet.

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Granska databaser i en anpassad databas samling målet
Använd den [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) för att hämta underordnade databaser i en anpassad databas samling målet. 

    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Skapa ett jobb för att köra ett skript i en anpassad databas samling mål
Använd den [ **ny AzureSqlJob** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) för att skapa ett jobb mot en grupp databaser som definieras av en anpassad databas samling mål. Elastiska databasen jobb kommer Expandera jobbet till flera underordnade jobb varje motsvarar en databas som är associerade med samlingen målet anpassad databas och se till att skriptet körs mot varje databas. Igen, är det viktigt att skripten har idempotent för att hantera att återförsök.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Insamling av data över databaser
Du kan använda ett jobb för att köra en fråga i en grupp med databaser och skicka resultaten till en viss tabell. Tabellen kan efterfrågas efter faktumet att se resultatet av frågan från varje databas. Detta ger en asynkron metod för att köra en fråga över flera databaser. Misslyckade försök hanteras automatiskt via återförsök.

Den angivna tabellen skapas automatiskt om det inte finns ännu. Den nya tabellen matchar schemat för den returnerade resultatuppsättningen. Om ett skript som returnerar flera resultatmängder elastisk databas jobb bara att skicka först till måltabellen.

Följande PowerShell-skript körs ett skript och samlar in resultaten i en angiven tabell. Det här skriptet förutsätter att ett T-SQL-skript har skapats som matar ut en enda resultatmängd och ett mål för insamling av anpassad databas har skapats.

Det här skriptet använder den [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) cmdlet. Ange parametrar för skriptet, autentiseringsuppgifter och körning av mål:

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

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Skapa och starta ett jobb för scenarion för insamling av data
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

## <a name="to-schedule-a-job-execution-trigger"></a>Så här schemalägger du en utlösare för körning av jobbet
Följande PowerShell-skript kan användas för att skapa ett återkommande schema. Det här skriptet använder ett minuters intervall, men [ **ny AzureSqlJobSchedule** ](/powershell/module/elasticdatabasejobs/new-azuresqljobschedule) stöder också - DayInterval, - HourInterval, - MonthInterval, och WeekInterval - parametrar. Scheman som kör en gång kan skapas med skicka - görs.

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
En utlösare för jobbet kan definieras om du vill att ett jobb som körs enligt ett schema med tiden. Följande PowerShell-skript kan användas för att skapa en utlösare för jobbet.

Använd [ny AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/new-azuresqljobtrigger) och ange följande variabler som motsvarar den önskade jobbet och schema:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    -JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Ta bort en schemalagd association att avbryta jobbet körs enligt schema
Jobbet utlösaren kan tas bort för att avbryta igen jobbkörningen via en utlösare för jobbet. Ta bort utlösaren jobbet om du vill stoppa ett jobb från utförs enligt ett schema som använder den [ **cmdlet Remove-AzureSqlJobTrigger**](/powershell/module/elasticdatabasejobs/remove-azuresqljobtrigger).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Hämta jobb utlösare som är bunden till en tidsplan
Följande PowerShell-skript kan användas för att hämta och visa jobb utlösare som har registrerats för en viss tidsplan.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Om du vill hämta jobb utlösare som är bunden till ett jobb
Använd [Get-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/get-azuresqljobtrigger) att hämta och visa scheman som innehåller ett registrerade jobb.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Skapa ett program för datanivå (DACPAC) för körning över databaser
Om du vill skapa en DACPAC finns [-datanivåprogram](https://msdn.microsoft.com/library/ee210546.aspx). Om du vill distribuera en DACPAC använder den [cmdlet New-AzureSqlJobContent](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent). DACPAC måste vara tillgänglig för tjänsten. Det rekommenderas att överföra en skapade DACPAC till Azure Storage och skapa en [signatur för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md) för DACPAC.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Uppdatera data-tier application (DACPAC) för körning över databaser
Befintliga DACPACs som registrerats i den elastiska databasen jobb kan uppdateras för att peka mot nya URI: er. Använd den [ **cmdlet Set-AzureSqlJobContentDefinition** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcontentdefinition) uppdatera DACPAC URI på en befintlig registrerade DACPAC:

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Att skapa ett jobb för att tillämpa en dataskiktsprogrammet (DACPAC) över databaser
När en DACPAC har skapats i den elastiska databasen jobb, kan du skapa ett jobb för att tillämpa DACPAC över flera databaser. Följande PowerShell-skript kan användas för att skapa ett DACPAC-jobb över en anpassad samling med databaser:

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
