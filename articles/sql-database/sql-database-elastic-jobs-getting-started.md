---
title: "Komma igång med elastisk databas jobb | Microsoft Docs"
description: "hur du använder jobb för elastisk databas"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
ms.assetid: 2540de0e-2235-4cdd-9b6a-b841adba00e5
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: ddove
ms.openlocfilehash: 05c20e880d4eb1eacdecc0c4c7e7491dfe1e6a89
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="getting-started-with-elastic-database-jobs"></a>Komma igång med jobb för elastisk databas
Elastiska databasen jobb (förhandsversion) för Azure SQL Database kan du tillförlitlighet köra T-SQL-skript som sträcker sig över flera databaser när du försöker och ge garantier för eventuell slutförande automatiskt. Mer information om funktionen för elastisk databas jobb finns i [översikt funktionssidan](sql-database-elastic-jobs-overview.md).

Det här avsnittet utökar exemplet hittades i [komma igång med elastiska Databasverktyg](sql-database-elastic-scale-get-started.md). När du är klar, kommer: Lär dig att skapa och hantera jobb som hanterar en grupp av relaterade databaser. Du behöver inte använda verktygen elastisk skalbarhet för att kunna dra nytta av fördelarna med elastiska jobb.

## <a name="prerequisites"></a>Krav
Hämta och kör den [komma igång med elastisk databas verktyg exempel](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Skapa en Fragmentera kartan manager med sample-appen
Här skapar du en karta Fragmentera manager tillsammans med flera delar, följt av infogning av data till shards. Om du redan har shards med shardade data i dem kan du hoppa över följande steg och flytta till nästa avsnitt.

1. Skapa och köra den **komma igång med elastiska Databasverktyg** exempelprogrammet. Följ stegen tills steg 7 i avsnittet [ladda ned och kör exempelappen](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). I slutet av steg 7 visas följande kommandotolk:

   ![kommandotolk](./media/sql-database-elastic-query-getting-started/cmd-prompt.png)

2. I Kommandotolken, Skriv ”1” och tryck på **RETUR**. Detta skapar Fragmentera kartan manager och lägger till två delar i server. Sedan skriver ”3” och tryck på **RETUR**; Upprepa åtgärden fyra gånger. Detta infogar exempel datarader i din shards.
3. Den [Azure Portal](https://portal.azure.com) tre nya databaser som ska visas:

   ![Visual Studio-bekräftelse](./media/sql-database-elastic-query-getting-started/portal.png)

   Nu skapar vi en anpassad databas-samling som visar alla databaser i kartan Fragmentera. Detta gör att vi kan skapa och köra ett jobb som lägger till en ny tabell över shards.

Här kan vi skulle vanligtvis skapa en karta Fragmentera mål med hjälp av den **ny AzureSqlJobTarget** cmdlet. Fragmentera kartan manager-databasen måste anges som ett mål för databasen och sedan kartan specifika Fragmentera har angetts som mål. I stället det dags att räkna upp alla databaser på servern och lägga till databaserna till den nya anpassa samlingen med undantag för master-databasen.

## <a name="creates-a-custom-collection-and-add-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Skapar en anpassad samling och lägger till alla databaser på servern till målet med undantag för master anpassad samling.
   ```
    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName
    $dbsinserver | %{
    $currentdb = $_.DatabaseName
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target."
        }

        else
        {
            throw $_
        }

    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
   }
   ```
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Skapa ett T-SQL-skript för körning på databaser
   ```
    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script
   ```

## <a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Skapa jobb för att köra ett skript på den anpassade gruppen av databaser

   ```
    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="execute-the-job"></a>Kör jobbet
Följande PowerShell-skript kan användas för att köra ett befintligt jobb:

Uppdatera följande variabel för att återspegla önskade Jobbnamnet som har köras:

   ```
    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="retrieve-the-state-of-a-single-job-execution"></a>Hämta tillståndet för en enskild jobbkörningen
Använda samma **Get-AzureSqlJobExecution** med den **IncludeChildren** parametern för att visa status för underordnade jobbet körningar, nämligen ett visst tillstånd för varje jobbkörningen mot varje databas som mål för jobbet.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions
   ```

## <a name="view-the-state-across-multiple-job-executions"></a>Visa status över flera jobb körningar
Den **Get-AzureSqlJobExecution** cmdlet har flera valfria parametrar som kan användas för att visa flera jobb körningar, filtreras via de angivna parametrarna. Följande visar några av de möjliga sätt att använda Get-AzureSqlJobExecution:

Hämta alla aktiva översta nivå jobbet körningar:

   ```
    Get-AzureSqlJobExecution
   ```

Hämta alla jobb för övre nivå körningar, inklusive inaktiva jobb körningar:

   ```
    Get-AzureSqlJobExecution -IncludeInactive
   ```

Hämta alla underordnade jobbet körningar av en tillhandahållna jobbet körnings-ID, inklusive inaktiva jobb körningar:

   ```
    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren
   ```

Hämta alla jobb körningar som skapats med hjälp av ett schema / jobb tillsammans med inaktiva jobb:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive
   ```

Hämta alla jobb som mål för en angiven Fragmentera karta, inklusive inaktiva jobb:

   ```
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Hämta alla jobb som mål för en angiven anpassad samling, inklusive inaktiva jobb:

   ```
    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Hämta listan över jobb uppgiften körningar inom en specifik jobbkörningen:

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions
   ```

Hämta jobb aktivitetsinformation körning:

Följande PowerShell-skript kan användas för att visa information om ett jobb för körning av aktiviteten, vilket är särskilt användbart när körningen felsökningsändamål.
   ```
    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution
   ```

## <a name="retrieve-failures-within-job-task-executions"></a>Hämta fel i jobb uppgiften körningar
Objektet JobTaskExecution innehåller en egenskap för livscykeln för uppgiften tillsammans med en meddelandeegenskap. Om ett jobb för körning av aktiviteten misslyckades livscykel egenskap anges *misslyckades* och meddelandeegenskapen kommer att anges till det resulterande Undantagsmeddelandet och dess stacken. Om ett jobb misslyckades, är det viktigt att visa information om jobbuppgifter som misslyckades för ett visst jobb.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions)
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }
   ```

## <a name="waiting-for-a-job-execution-to-complete"></a>Väntar på att ett jobbkörning ska slutföras
Följande PowerShell-skript kan användas för att vänta på en projektaktivitet att slutföra:

   ```
    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

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

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy
   ```

### <a name="update-a-custom-execution-policy"></a>Uppdatera en anpassad körningsprincip
Uppdatera önskade körningsprincipen att uppdatera:

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
   ```

## <a name="cancel-a-job"></a>Avbryta ett jobb
Den elastiska databasen jobb stöder jobb annullering begäranden.  Om den elastiska databasen jobb upptäcker en begäran om att avbryta ett jobb som körs, försöker den att stoppa jobbet.

Det finns två olika sätt att elastiska databasen jobb kan utföra en annullering:

1. Avbryta pågående aktiviteter: om en annullering identifieras när en aktivitet körs för närvarande en annullering görs inom körs aspekt av aktiviteten.  Exempel: om det finns en tidskrävande fråga som för närvarande utförs när en annullering görs, är ett försök att avbryta frågan.
2. Annullering återförsök för aktiviteten: Om en annullering identifieras av kontrollen tråd innan en uppgift startas för körning kontrollen tråden ska undvika att starta uppgiften och deklarera begäran som avbruten.

Om det krävs en annullering av jobbet för överordnade jobb respekteras begäran om att avbryta för överordnade jobb och alla dess underordnade jobb.

För att skicka en begäran om att avbryta, Använd den **stoppa AzureSqlJobExecution** cmdlet och ange den **JobExecutionId** parameter.

   ```
    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Ta bort ett jobb efter namn och den jobbhistorik
Den elastiska databasen jobb stöder asynkrona borttagning av jobb. Ett jobb kan vara markerad för borttagning och tas bort jobbet och alla dess jobbhistorik när alla jobb körningar har slutförts för jobbet. Systemet Avbryt inte automatiskt aktiva jobb körningar.  

Stoppa AzureSqlJobExecution måste i stället anropas om du vill avbryta aktiva jobb körningar.

Utlös borttagning av jobbet genom att använda den **ta bort AzureSqlJob** cmdlet och ange den **jobbnamn** parameter.

   ```
    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
   ```

## <a name="create-a-custom-database-target"></a>Skapa en anpassad databas mål
Anpassad databas mål kan definieras i jobb för elastisk databas som kan användas för att köras direkt eller som ska ingå i en anpassad databas-grupp. Eftersom **elastiska pooler** ännu direkt stöds inte via PowerShell-APIs du helt enkelt skapa en anpassad databas mål och anpassad databas samling mål som omfattar alla databaser i poolen.

Ange följande variabler återspeglar den önskade databasinformationen:

   ```
    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName
   ```

## <a name="create-a-custom-database-collection-target"></a>Skapa en anpassad databas samling mål
En anpassad databas samling mål kan definieras för att möjliggöra körning över flera definierade databasen mål. När du har skapat en databasgrupp kan databaser vara kopplad till anpassade samlingar målet.

Ange följande variabler önskade anpassade samlingar mål konfiguration:

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="add-databases-to-a-custom-database-collection-target"></a>Lägga till databaser till en anpassad databas samling mål
Databasen mål kan vara associerat med anpassad databas samling mål för att skapa en grupp databaser. När ett jobb skapas som en anpassad databas samling mål, kommer att expanderas om du vill anpassa databaserna som är associerade med gruppen vid tidpunkten för körning.

Lägg till rätt databas i en specifik egen samling:

   ```
    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName
   ```

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Granska databaser i en anpassad databas samling målet
Använd den **Get-AzureSqlJobTarget** för att hämta underordnade databaser i en anpassad databas samling målet.

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets
   ```

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Skapa ett jobb för att köra ett skript i en anpassad databas samling mål
Använd den **ny AzureSqlJob** för att skapa ett jobb mot en grupp databaser som definieras av en anpassad databas samling mål. Elastiska databasen jobb kommer Expandera jobbet till flera underordnade jobb varje motsvarar en databas som är associerade med samlingen målet anpassad databas och se till att skriptet körs mot varje databas. Igen, är det viktigt att skripten har idempotent för att hantera att återförsök.

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="data-collection-across-databases"></a>Insamling av data över databaser
**Den elastiska databasen jobb** stöder köra en fråga i en grupp med databaser och skickar resultatet till en angiven databastabell. Tabellen kan efterfrågas efter faktumet att se resultatet av frågan från varje databas. Detta ger en asynkron metod för att köra en fråga över flera databaser. Fel fall, till exempel en av databaserna som för tillfället otillgänglig hanteras automatiskt via återförsök.

Den angivna tabellen skapas automatiskt om den inte ännu finns, matchar schemat för den returnerade resultatuppsättningen. Om en skriptkörningen returnerar flera resultatmängder skickas bara det första till den angivna måltabellen jobb för elastisk databas.

Följande PowerShell-skript kan användas för att köra ett skript som samlar in resultaten till en angiven tabell. Det här skriptet förutsätter att ett T-SQL-skript har skapats som matar ut en enda resultatmängd och ett mål för insamling av anpassad databas har skapats.

Ange följande för att återspegla önskade skript, autentiseringsuppgifter och mål för körning:

   ```
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
   ```

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Skapa och starta ett jobb för scenarion för insamling av data
   ```
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Skapa ett schema för jobbkörningen med en utlösare för jobb
Följande PowerShell-skript kan användas för att skapa ett reoccurring schema. Det här skriptet använder en minuts intervall, men ny AzureSqlJobSchedule stöder också - DayInterval, - HourInterval, - MonthInterval, och WeekInterval - parametrar. Scheman som kör en gång kan skapas med skicka - görs.

Skapa ett nytt schema:
   ```
    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime
    Write-Output $schedule
   ```

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Skapa en utlösare för jobbet om du vill att ett jobb som körs på en tidsplan
En utlösare för jobbet kan definieras om du vill att ett jobb som körs enligt ett schema med tiden. Följande PowerShell-skript kan användas för att skapa en utlösare för jobbet.

Ange följande variabler som motsvarar den önskade jobbet och schema:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
    Write-Output $jobTrigger
   ```

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Ta bort en schemalagd association att avbryta jobbet körs enligt schema
Jobbet utlösaren kan tas bort för att avbryta igen jobbkörningen via en utlösare för jobbet.
Ta bort utlösaren jobbet om du vill stoppa ett jobb från utförs enligt ett schema som använder den **ta bort AzureSqlJobTrigger** cmdlet.

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
   ```

## <a name="import-elastic-database-query-results-to-excel"></a>Importera elastisk databas frågeresultaten till Excel
 Du kan importera resultatet av en fråga till en Excel-fil.

1. Starta Excel 2013.
2. Navigera till den **Data** menyfliksområdet.
3. Klicka på **från andra källor** och på **från SQL Server**.

   ![Excel-import från andra källor](./media/sql-database-elastic-query-getting-started/exel-sources.png)

4. I den **Dataanslutningsguiden** skriver server servernamn och inloggningsuppgifter. Klicka sedan på **Nästa**.
5. I dialogrutan **Markera databasen som innehåller de data som du vill**, Välj den **ElasticDBQuery** databas.
6. Välj den **kunder** tabell i listan och klickar på **nästa**. Klicka på **Slutför**.
7. I den **importera Data** formuläret under **Välj hur du vill visa data i arbetsboken**väljer **tabell** och på **OK**.

Alla rader från **kunder** tabell som sparas i olika delar fylla i Excel-blad.

## <a name="next-steps"></a>Nästa steg
Du kan nu använda Excel-data. Använda anslutningssträngen med servernamnet, databasnamnet och autentiseringsuppgifter för att ansluta din verktyg för BI och integrering till elastisk fråga databas. Kontrollera att SQL Server stöds som en datakälla för verktyget du behöver. Referera till elastisk fråga databas och externa tabeller precis som andra SQL Server-databas och SQL Server-tabeller som du vill ansluta till med din-verktyget.

### <a name="cost"></a>Kostnad
Det finns utan extra kostnad för att använda funktionen för elastisk databas frågan. Dock just nu den här funktionen är bara tillgängliga på premiumdatabaser som en slutpunkt, men delar som kan vara av en tjänstnivå.

Mer information om priser finns [prisinformation för SQL-databasen](https://azure.microsoft.com/pricing/details/sql-database/).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
