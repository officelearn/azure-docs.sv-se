---
title: Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell
description: I den här artikeln beskrivs hur du använder Azure PowerShell för att hantera DataSjöanalyskonton, datakällor, användare & jobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.topic: conceptual
ms.date: 06/29/2018
ms.openlocfilehash: 4273828c9c2bdb75fcbc1de45da55c5a03dd615f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66156427"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

I den här artikeln beskrivs hur du hanterar Azure Data Lake Analytics-konton, datakällor, användare och jobb med hjälp av Azure PowerShell.

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill använda PowerShell med DataSjöanalys samlar du in följande information: 

* **Prenumerations-ID:** ID:t för Azure-prenumerationen som innehåller ditt DataSjöanalyskonto.
* **Resursgrupp**: Namnet på azure-resursgruppen som innehåller ditt DataSjöanalyskonto.
* **DataSjöanalyskontonamn**: Namnet på ditt Data Lake Analytics-konto.
* **Standardkontonamn för DataSjölagring:** Varje DataSjöanalyskonto har ett standardkonto för DataSjölagring.
* **Plats**: Platsen för ditt Data Lake Analytics-konto, till exempel "Östra USA 2" eller andra platser som stöds.

PowerShell-kodfragmenten i den här självstudien använder dessa variabler för att lagra informationen

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>Logga in på Azure

### <a name="log-in-using-interactive-user-authentication"></a>Logga in med interaktiv användarautentisering

Logga in med ett prenumerations-ID eller med prenumerationsnamn

```powershell
# Using subscription id
Connect-AzAccount -SubscriptionId $subId

# Using subscription name
Connect-AzAccount -SubscriptionName $subname 
```

## <a name="saving-authentication-context"></a>Spara autentiseringskontext

Cmdlet `Connect-AzAccount` frågar alltid efter autentiseringsuppgifter. Du kan undvika att bli tillfrågad genom att använda följande cmdlets:

```powershell
# Save login session information
Save-AzAccounts -Path D:\profile.json  

# Load login session information
Select-AzAccounts -Path D:\profile.json 
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>Logga in med hjälp av en huvudidentitet för tjänsten (SPI)

```powershell
$tenantid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  
$spi_appname = "appname" 
$spi_appid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" 
$spi_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" 

$pscredential = New-Object System.Management.Automation.PSCredential ($spi_appid, (ConvertTo-SecureString $spi_secret -AsPlainText -Force))
Login-AzAccount -ServicePrincipal -TenantId $tenantid -Credential $pscredential -Subscription $subid
```

## <a name="manage-accounts"></a>Hantera konton


### <a name="list-accounts"></a>Lista över konton

```powershell
# List Data Lake Analytics accounts within the current subscription.
Get-AdlAnalyticsAccount

# List Data Lake Analytics accounts within a specific resource group.
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

### <a name="create-an-account"></a>Skapa ett konto

Alla Data Lake Analytics-konton kräver ett Data Lake Store-standardkonto som används för att lagra loggar. Du kan återanvända ett befintligt konto eller skapa ett konto. 

```powershell
# Create a data lake store if needed, or you can re-use an existing one
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-account-information"></a>Hämta kontoinformation

Få information om ett konto.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>Kontrollera om det finns ett konto

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>Hantera datakällor
Azure Data Lake Analytics stöder för närvarande följande datakällor:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure-lagring](../storage/common/storage-introduction.md)

Varje DataSjöanalyskonto har ett standardkonto för DataSjölager. Standardkontot för DataSjölagring används för att lagra jobbmetadata och jobbgranskningsloggar. 

### <a name="find-the-default-data-lake-store-account"></a>Hitta standardkontot för DataSjölagring

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Du kan hitta standardkontot för Datasjölagring genom att `IsDefault` filtrera listan över datakällor efter egenskapen:

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault } 
```

### <a name="add-a-data-source"></a>Lägga till en datakälla

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>Lista datakällor

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Account $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>Skicka U-SQL-jobb

### <a name="submit-a-string-as-a-u-sql-job"></a>Skicka en sträng som ett U-SQL-jobb

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```

### <a name="submit-a-file-as-a-u-sql-job"></a>Skicka en fil som ett U-SQL-jobb

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

### <a name="list-jobs"></a>Lista jobb

Resultatet innehåller de jobb som körs för närvarande och de jobb som nyss blev klara.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>Lista de översta N-jobben

Som standard sorteras listan över jobb på inlämningstiden. Så de senast inskickade jobben visas först. Som standard kommer ADLA-kontot ihåg jobb i 180 dagar, men Cmdlet Get-AdlJob som standard returnerar bara de första 500. Använd -Top-parametern för att lista ett visst antal jobb.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>Lista jobb efter jobbtillstånd

Använda `-State` parametern. Du kan kombinera något av följande värden:

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

### <a name="list-jobs-by-job-result"></a>Lista jobb efter jobbresultat

Använd `-Result` parametern för att identifiera om avslutade jobb har slutförts. Den har dessa värden:

* Avbrutet
* Misslyckades
* Inget
* Lyckades

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>Lista jobb efter jobblämnare

Parametern `-Submitter` hjälper dig att identifiera vem som har skickat ett jobb.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>Lista jobb efter inlämningstid

Den `-SubmittedAfter` är användbar vid filtrering till ett tidsintervall.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="get-job-status"></a>Hämta jobbstatus

Hämta status för ett specifikt jobb.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```


### <a name="cancel-a-job"></a>Avbryta ett jobb

```powershell
Stop-AdlJob -Account $adla -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Vänta tills ett jobb är klart

I stället `Get-AdlAnalyticsJob` för att upprepa tills ett `Wait-AdlJob` jobb är klart kan du använda cmdleten för att vänta tills jobbet ska avslutas.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="analyzing-job-history"></a>Analysera jobbhistorik

Att använda Azure PowerShell för att analysera historiken för jobb som har körts i DataSjöanalys är en kraftfull teknik. Du kan använda den för att få insikter om användning och kostnad. Du kan läsa mer genom att titta på [exempelrepoyrfördelningen för jobbhistorikanalys](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="list-job-pipelines-and-recurrences"></a>Lista jobbpipelor och upprepningar

Använd `Get-AdlJobPipeline` cmdleten för att se pipeline-informationen som tidigare skickats jobb.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Använd `Get-AdlJobRecurrence` cmdleten för att se upprepningsinformationen för tidigare inskickade jobb.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```


## <a name="manage-compute-policies"></a>Hantera beräkningsprinciper

### <a name="list-existing-compute-policies"></a>Lista befintliga beräkningsprinciper

Cmdlet `Get-AdlAnalyticsComputePolicy` hämtar information om beräkningsprinciper för ett Data Lake Analytics-konto.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Skapa en beräkningsprincip

Cmdlet `New-AdlAnalyticsComputePolicy` skapar en ny beräkningsprincip för ett Data Lake Analytics-konto. I det här exemplet anges den maximala AUs som är tillgänglig för den angivna användaren till 50 och den lägsta jobbprioriteten till 250.

```powershell
$userObjectId = (Get-AzAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```
## <a name="manage-files"></a>Hantera filer

### <a name="check-for-the-existence-of-a-file"></a>Kontrollera om finns en fil.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>Ladda upp och ladda ner

Ladda upp en fil.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

Ladda upp en hel mapp rekursivt.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Ladda ner en fil.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Ladda ner en hel mapp rekursivt.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Om uppladdnings- eller hämtningsprocessen avbryts kan du försöka återuppta processen ``-Resume`` genom att köra cmdleten igen med flaggan.

## <a name="manage-the-u-sql-catalog"></a>Hantera U-SQL-katalogen

U-SQL-katalogen används för att strukturera data och kod så att de kan delas av U-SQL-skript. Katalogen möjliggör högsta möjliga prestanda med data i Azure Data Lake. Mer information finns i [Använd U-SQL-katalogen](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Lista objekt i U-SQL-katalogen

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>Lista alla sammansättningar i U-SQL-katalogen

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>Få information om en katalogartikel

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="store-credentials-in-the-catalog"></a>Lagra autentiseringsuppgifter i katalogen

Skapa ett autentiseringsuppgifter för en databas som finns i Azure i en U-SQL-databas. För närvarande är U-SQL-autentiseringsuppgifter den enda typen av katalogobjekt som du kan skapa via PowerShell.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

## <a name="manage-firewall-rules"></a>Hantera brandväggsregler

### <a name="list-firewall-rules"></a>Lista brandväggsregler

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>Lägga till en brandväggsregel

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="modify-a-firewall-rule"></a>Ändra en brandväggsregel

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>Ta bort en brandväggsregel

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Tillåt Azure IP-adresser

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```


## <a name="working-with-azure"></a>Arbeta med Azure

### <a name="get-error-details"></a>Få felinformation

```powershell
Resolve-AzError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>Kontrollera om du kör som administratör på din Windows-dator

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Hitta ett TenantID

Från ett prenumerationsnamn:

```powershell
function Get-TenantIdFromSubscriptionName( [string] $subname )
{
    $sub = (Get-AzSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubscriptionName "ADLTrainingMS"
```

Från ett prenumerations-ID:

```powershell
function Get-TenantIdFromSubscriptionId( [string] $subid )
{
    $sub = (Get-AzSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubscriptionId $subid
```

Från en domänadress som "contoso.com"

```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Lista alla dina prenumerationer och klient-ID:er

```powershell
$subs = Get-AzSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Skapa ett DataSjöanalyskonto med hjälp av en mall

Du kan också använda en Azure Resource Group-mall med följande exempel: [Skapa ett DataSjöanalyskonto med hjälp av en mall](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)

## <a name="next-steps"></a>Nästa steg
* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* Komma igång med Data Lake Analytics med [Azure portalen](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [Azure CLI](data-lake-analytics-get-started-cli.md)
* Hantera Azure Data Lake Analytics med [Azure portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) 
