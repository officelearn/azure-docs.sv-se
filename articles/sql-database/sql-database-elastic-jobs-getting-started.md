---
title: Komma igång med elastic database-jobb | Microsoft Docs
description: Använd elastic database-jobb för att köra T-SQL-skript som sträcker sig över flera databaser.
services: sql-database
ms.service: sql-database
subservice: sacoperations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 07/16/2018
ms.openlocfilehash: e760aee960cf9d84cd5076c993b1a9583b045860
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159286"
---
# <a name="getting-started-with-elastic-database-jobs"></a>Komma igång med Elastic Database-jobb


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


Elastic Database-jobb (förhandsversion) för Azure SQL Database kan du tillförlitligt köra T-SQL-skript som sträcker sig över flera databaser samtidigt som du försöker igen och ge garantier för slutlig slutförande automatiskt. Mer information om funktionen för Elastic Database-jobb finns i [elastiska jobb](sql-database-elastic-jobs-overview.md).

Den här artikeln utökar exemplet finns i [komma igång med elastiska Databasverktyg](sql-database-elastic-scale-get-started.md). När du är klar kan du se hur du skapar och hanterar jobb som hanterar en grupp med relaterade databaser. Du behöver inte använda verktyg för elastisk skalning för att kunna dra nytta av fördelarna med elastiska jobb.

## <a name="prerequisites"></a>Förutsättningar
Ladda ned och kör den [komma igång med Elastic Database-verktyg exempel](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Skapa en shard kartan manager med hjälp av exempelappen
Här skapa du en skärvkarta manager tillsammans med flera shards, följt av inmatningen av data i shards. Om du redan har shards som konfigurerats med shardade data i dem kan du hoppa över följande steg och flytta till nästa avsnitt.

1. Skapa och köra den **komma igång med elastiska Databasverktyg** exempelprogrammet. Följ stegen tills steg 7 i avsnittet [ladda ned och kör exempelappen](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). I slutet av steg 7 Se följande kommandotolk:

   ![kommandotolk](./media/sql-database-elastic-query-getting-started/cmd-prompt.png)

2. Skriv ”1” i kommandofönstret och tryck på **RETUR**. Detta skapar fragmentet kartan manager och lägger till två fragment till servern. Skriv ”3” och tryck på sedan **RETUR**; och upprepa åtgärden fyra gånger. Detta infogar rader med exempel data i din fragment.
3. Den [Azure-portalen](https://portal.azure.com) ska visa tre nya databaser:

   ![Visual Studio-bekräftelse](./media/sql-database-elastic-query-getting-started/portal.png)

   Nu kan skapa vi en anpassad databas-samling som visar alla databaser i fragmentkartan. På så sätt kan vi skapa och köra ett jobb som lägger till en ny tabell över shards.

Här vi vanligtvis skulle skapa en skärvkarta mål med hjälp av den **New AzureSqlJobTarget** cmdlet. Databasen måste anges som ett mål för databasen och sedan specifika fragmentkartan har angetts som mål. I stället det dags att räkna upp alla databaser på servern och lägga till databaserna till den nya anpassa samlingen med undantag för master-databasen.

## <a name="creates-a-custom-collection-and-add-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Skapar en anpassad samling och lägger till alla databaser på servern till målet för anpassad insamling med undantag för master.
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

## <a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Skapa jobb för att köra ett skript för anpassad grupp med databaser

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

Uppdatera följande variabel för att återspegla det önskade Jobbnamnet för att har genomfört:

   ```
    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="retrieve-the-state-of-a-single-job-execution"></a>Hämta tillståndet för en enskild jobbkörning
Använd samma **Get-AzureSqlJobExecution** cmdlet med den **IncludeChildren** parameter för att visa status för underordnade jobbkörningar, nämligen ett visst tillstånd för varje jobbkörning mot varje databas mål för jobbet.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions
   ```

## <a name="view-the-state-across-multiple-job-executions"></a>Visa status över flera jobbkörningar
Den **Get-AzureSqlJobExecution** cmdlet har flera valfria parametrar som kan användas för att visa flera jobbkörningar filtreras via de angivna parametrarna. Nedan visas några av de möjliga sätten att använda Get-AzureSqlJobExecution:

Hämta alla aktiva översta jobbkörningar:

   ```
    Get-AzureSqlJobExecution
   ```

Hämta alla översta jobbkörningar, inklusive inaktiva jobbkörningar:

   ```
    Get-AzureSqlJobExecution -IncludeInactive
   ```

Hämta alla underordnade jobbkörningar av en tillhandahållna jobbet körnings-ID, inklusive inaktiva jobbkörningar:

   ```
    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren
   ```

Hämta alla jobbkörningar som skapats med hjälp av ett schema / jobb tillsammans med inaktiva jobb:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive
   ```

Hämta alla jobb som riktar in sig på en angiven fragmentkartan, inklusive inaktiva jobb:

   ```
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Hämta alla jobb som riktar in sig på en angiven anpassade samling, inklusive inaktiva jobb:

   ```
    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Hämta listan över uppgiften jobbkörningar i en specifik jobbkörningen:

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions
   ```

Hämta information om jobbkörningar uppgift:

Följande PowerShell-skript kan användas för att visa information om jobbaktiviteter, vilket är särskilt användbart när du felsöker fel vid körning.
   ```
    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution
   ```

## <a name="retrieve-failures-within-job-task-executions"></a>Hämta programfel i jobbkörningar för uppgift
Objektet JobTaskExecution innehåller en egenskap för livscykeln för uppgiften tillsammans med en meddelandeegenskap. Om ett jobb för körning av aktiviteten misslyckades livscykel-egenskapen är inställd *misslyckades* och meddelandeegenskapen är inställt på det resulterande Undantagsmeddelandet och dess stack. Om ett jobb inte lyckades, är det viktigt att visa information om jobbuppgifter som misslyckades för ett visst jobb.

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

## <a name="waiting-for-a-job-execution-to-complete"></a>Väntar på en jobbkörningen ska slutföras
Följande PowerShell-skript kan användas för att vänta tills åtgärden ett jobb har slutförts:

   ```
    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="create-a-custom-execution-policy"></a>Skapa en princip för anpassad körning
Elastic Database-jobb kan du skapa anpassade körningsprinciper som kan användas när du startar jobb.

För närvarande tillåter körningsprinciper för att definiera:

* Namn: Identifierare för körningsprincipen.
* Tidsgräns för jobb: Total tid innan ett jobb har avbrutits av Elastic Database-jobb.
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

### <a name="update-a-custom-execution-policy"></a>Uppdatera en anpassad körningsprincipen
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
Elastic Database-jobb har stöd för begäranden för annullering av jobb.  Om elastiska Databasjobb upptäcker en avbrottsbegäran för ett jobb som körs, försöker stoppa jobbet.

Det finns två olika sätt att Elastic Database-jobb kan utföra en uppsägning:

1. Avbryta pågående aktiviteter: om ett avbrott identifieras när en aktivitet körs för närvarande en uppsägning prövas inom körs för närvarande aspekt av aktiviteten.  Till exempel: om det finns en tidskrävande fråga som för närvarande utförs när en uppsägning görs, det är ett försök att avbryta frågan.
2. Annullerad omförsök: Om ett avbrott identifieras av kontroll tråden innan en aktivitet startas för körning, kontroll tråden undviker starta uppgiften och deklarera begäran som har avbrutits.

Om ett jobb avbrott har begärts för ett överordnat jobb, är på avbrottsbegäran användas för det överordnade jobbet och alla dess underordnade jobb.

För att skicka en begäran om annullering, använda den **Stop-AzureSqlJobExecution** cmdleten och ange den **JobExecutionId** parametern.

   ```
    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Ta bort ett jobb efter namn och jobbets historik
Elastic Database-jobb har stöd för asynkrona borttagning av jobb. Ett jobb kan markeras för borttagning och systemet tar bort jobbet och alla dess jobbhistorik när alla jobbkörningar har slutfört för projektet. Systemet avbryts inte active jobbkörningar automatiskt.  

Stoppa AzureSqlJobExecution måste i stället anropas om du vill avbryta active jobbkörningar.

För att utlösa jobbet tas bort, Använd den **Remove-AzureSqlJob** cmdleten och ange den **JobName** parametern.

   ```
    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
   ```

## <a name="create-a-custom-database-target"></a>Skapa en anpassad databas-mål
Anpassad databas mål kan definieras i Elastic Database-jobb som kan användas för körning direkt eller ska ingå i en anpassad databas-grupp. Eftersom **elastiska pooler** ännu direkt stöds inte via PowerShell-APIs du helt enkelt skapa en anpassad databas mål och anpassad databas samling mål som omfattar alla databaser i poolen.

Ange följande variabler för att återspegla den önskade databasinformationen:

   ```
    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName
   ```

## <a name="create-a-custom-database-collection-target"></a>Skapa en anpassad databas samling mål
En anpassad databas samling mål kan du definiera om du vill aktivera körning över flera definierade databasen mål. När du har skapat en grupp kan databaser associeras till målet för anpassad insamling.

Ange följande variabler på önskad anpassad samling target konfiguration:

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="add-databases-to-a-custom-database-collection-target"></a>Lägga till databaser i en anpassad databas samling mål
Mål för databasen kan associeras med anpassad databas samling mål att skapa en grupp med databaser. När ett jobb skapas som har ett mål för anpassad databas-samling, har detta utökats för att rikta databaser som är kopplad till gruppen vid tidpunkten för körning.

Lägg till rätt databas i en specifik anpassade samling:

   ```
    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName
   ```

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Granska databaserna i en anpassad databas samling mål
Använd den **Get-AzureSqlJobTarget** cmdlet för att hämta underordnade databaserna i en anpassad databas samling mål.

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets
   ```

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Skapa ett jobb för att köra ett skript i en anpassad databas samling mål
Använd den **New AzureSqlJob** cmdlet för att skapa ett jobb mot en grupp med databaser som definieras av en anpassad databas samling mål. Elastic Database-jobb kan utökas jobbet i flera underordnade jobb varje motsvarar en databas som är associerade med anpassad databas samling mål och se till att skriptet körs mot varje databas. Igen, är det viktigt att skripten är idempotenta för att hantera att återförsök.

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="data-collection-across-databases"></a>Insamling av data mellan databaser
**Elastic Database-jobb** stöder kör en fråga för en grupp med databaser och skickar resultatet till en angiven databastabell. Tabellen kan frågas efter faktumet att se resultatet av frågan från varje databas. Detta är en asynkron mekanism för att köra en fråga över flera databaser. Fel fall, till exempel en av databaserna är inte tillgänglig för tillfället hanteras automatiskt via återförsök.

Den angivna tabellen skapas automatiskt om det inte finns ännu, matchar schemat för den returnerade resultatuppsättningen. Om en körning av skript returnerar flera resultatmängder, skickar elastiska databasjobb endast den första som den angivna tabellen.

Följande PowerShell-skript kan användas för att köra ett skript som samlar in resultaten till en angiven tabell. Det här skriptet förutsätter att du har skapat ett T-SQL-skript som matar ut en enda resultatmängd och ett mål för insamling av anpassad databas har skapats.

Ange följande för att återspegla den önskade skript, autentiseringsuppgifter och körningsmål:

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

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Skapa och starta ett jobb för insamling av datascenarier
   ```
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Skapa ett schema för jobbkörning med en utlösare som jobb
Följande PowerShell-skript kan användas för att skapa ett återkommande schema. Det här skriptet använder en minuts intervall, men New-AzureSqlJobSchedule stöder också - DayInterval, - HourInterval, - MonthInterval, och -WeekInterval parametrar. Scheman som kör bara en gång kan skapas av skicka - genomfört.

Skapa ett nytt schema:
   ```
    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime
    Write-Output $schedule
   ```

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Skapa en utlösare som jobb ska ha ett jobb som körs på en tidsplan
En utlösare för jobbet kan definieras för ett jobb som körs enligt ett schema. Följande PowerShell-skript kan användas för att skapa en utlösare för jobbet.

Ange följande variabler på motsvarar önskad jobbet och schema:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
    Write-Output $jobTrigger
   ```

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Ta bort en schemalagd kopplingen att avbryta jobbet från att köras på schema
Jobbet utlösaren kan tas bort för att avbryta körningen av återkommande jobb via en utlösare för jobbet.
Ta bort jobbet utlösaren om du vill stoppa ett jobb från som körs enligt ett schema med hjälp av den **Remove-AzureSqlJobTrigger** cmdlet.

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
   ```

## <a name="import-elastic-database-query-results-to-excel"></a>Importera elastisk databas frågeresultaten till Excel
 Du kan importera resultatet av en fråga till en Excel-fil.

1. Starta Excel 2013.
2. Navigera till den **Data** menyfliksområdet.
3. Klicka på **från andra källor** och klicka på **från SQL Server**.

   ![Excel-import från andra källor](./media/sql-database-elastic-query-getting-started/exel-sources.png)

4. I den **Dataanslutningsguiden** skriver du servernamnet och inloggningsuppgifter för servern. Klicka sedan på **Nästa**.
5. I dialogrutan **Markera databasen som innehåller de data du vill**väljer den **ElasticDBQuery** databas.
6. Välj den **kunder** tabellen i listvyn och klicka på **nästa**. Klicka sedan på **Slutför**.
7. I den **importdata** formuläret under **Välj hur du vill visa data i din arbetsbok**väljer **tabell** och klicka på **OK**.

Alla rader från **kunder** tabell, lagras i olika shards fylla i Excel-blad.

## <a name="next-steps"></a>Nästa steg
Du kan nu använda Excel-data. Använd anslutningssträngen med servernamnet, databasnamnet och autentiseringsuppgifter för att ansluta din integreringsverktyg BI och data till elastisk fråga i databasen. Kontrollera att SQL Server stöds som en datakälla för ditt verktyg. Se elastisk fråga databas och externa tabeller precis som andra SQL Server-databas och SQL Server-tabeller som du vill ansluta till med verktyg.

### <a name="cost"></a>Kostnad
Det finns ingen extra kostnad för att använda funktionen för Elastic Database-fråga. Men just nu den här funktionen är endast tillgänglig på Premium- och affärskritiska databaser och elastiska pooler som en slutpunkt, men shards kan vara av valfri tjänstnivå.

Information om priser finns i [prisinformation för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
