---
title: Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell
description: Den här artikeln beskriver hur du använder Azure PowerShell för att hantera Data Lake Analytics-konton, datakällor, användare och jobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: mahi
manager: kfile
editor: jasonwhowell
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.topic: conceptual
ms.date: 06/02/2018
ms.openlocfilehash: 560f36dc64480fd6aceaa50226b191ee40d2486f
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36959856"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Den här artikeln beskriver hur du hanterar Azure Data Lake Analytics-konton, datakällor, användare och jobb med hjälp av Azure PowerShell.

## <a name="prerequisites"></a>Förutsättningar

Samla in följande typer av information om du vill använda PowerShell med Data Lake Analytics: 

* **Prenumerations-ID**: ID för Azure-prenumeration som innehåller ditt Data Lake Analytics-konto.
* **Resursgruppen**: namnet på Azure-resursgrupp som innehåller ditt Data Lake Analytics-konto.
* **Data Lake Analytics-kontonamn**: namnet på Data Lake Analytics-kontot.
* **Standard Data Lake Store-kontonamnet**: varje Data Lake Analytics-konto har ett Data Lake Store-standardkontot.
* **Plats**: platsen för ditt Data Lake Analytics-konto, till exempel ”USA, östra 2” eller andra platser som stöds.

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
Connect-AzureRmAccount -SubscriptionId $subId

# Using subscription name
Connect-AzureRmAccount -SubscriptionName $subname 
```

## <a name="saving-authenticaiton-context"></a>Spara kontext för autentisering

Den `Connect-AzureRmAccount` cmdlet efterfrågar alltid autentiseringsuppgifter. Du kan undvika uppmanas med hjälp av följande cmdlets:

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>Logga in med ett Service Principal identitet SPI)

```powershell
$tenantid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  
$spi_appname = "appname" 
$spi_appid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" 
$spi_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" 

$pscredential = New-Object System.Management.Automation.PSCredential ($spi_appid, (ConvertTo-SecureString $spi_secret -AsPlainText -Force))
Login-AzureRmAccount -ServicePrincipal -TenantId $tenantid -Credential $pscredential -Subscription $subid
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
* [Azure Storage](../storage/common/storage-introduction.md)

Varje Data Lake Analytics-kontot har ett Data Lake Store-standardkontot. Data Lake Store-standardkontot används för att spara jobbet metadata och jobbet granskningsloggar. 

### <a name="find-the-default-data-lake-store-account"></a>Hitta Data Lake Store-standardkontot

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Du kan hitta Data Lake Store-standardkontot genom att filtrera listan över datakällor genom den `IsDefault` egenskapen:

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

### <a name="list-data-sources"></a>Lista över datakällor

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Name $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "Blob"
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

### <a name="list-jobs"></a>Lista över jobb

Resultatet innehåller de jobb som körs för närvarande och de jobb som nyss blev klara.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>Lista över de x främsta jobb

Som standard för jobben i listan sorteras i överföringstid. Så att de nyligen skickade jobb visas först. Som standard i ADLA konto kommer ihåg jobb i 180 dagar men cmdleten Get-AdlJob som standard returnerar endast de första 500. Använd - översta parametern för att visa en lista med ett visst antal jobb.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>Lista över jobb efter jobbets status

Med den `-State` parameter. Du kan kombinera dessa värden:

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

### <a name="list-jobs-by-job-result"></a>Lista över jobb efter jobb resultat

Använd den `-Result` parametern för att identifiera om avslutades jobben har slutförts. Det har dessa värden:

* Annullerad
* Misslyckad
* Ingen
* Lyckades

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>Lista över jobb efter jobb avsändaren

Den `-Submitter` parametern hjälper dig att identifiera vem som har skickat ett jobb.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>Lista över jobb efter sändningstid

Den `-SubmittedAfter` är användbart i filtrering för ett tidsintervall.


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

### <a name="wait-for-a-job-to-finish"></a>Vänta tills ett jobb ska slutföras

I stället för att upprepa `Get-AdlAnalyticsJob` tills ett jobb är klar kan du använda den `Wait-AdlJob` för att vänta på att jobbet ska sluta.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="analyzing-job-history"></a>Analysera jobbhistorik

Använda Azure PowerShell för att analysera historiken för jobbet som har körts i Data Lake analytics är en kraftfull teknik. Du kan använda den och få insikter om användning och kostnader. Du kan lära dig mer genom att titta på den [jobbet historik analys exempel lagringsplatsen](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="list-job-pipelines-and-recurrences"></a>lista över jobb pipelines och repetitioner

Använd den `Get-AdlJobPipeline` för att se pipeline-information skickats tidigare jobb.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Använd den `Get-AdlJobRecurrence` för att se informationen upprepning för tidigare skickade jobb.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```


## <a name="manage-compute-policies"></a>Hantera principer för beräkning

### <a name="list-existing-compute-policies"></a>Visa en lista med befintliga beräknings-principer

Den `Get-AdlAnalyticsComputePolicy` cmdlet hämtar information om beräkning principer för ett Data Lake Analytics-konto.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Skapa en princip för beräkning

Den `New-AdlAnalyticsComputePolicy` cmdlet skapar en ny princip för beräkning för ett Data Lake Analytics-konto. Det här exemplet anger maximala Australien som är tillgängliga för den angivna användaren till 50 och minsta jobbets prioritet 250.

```powershell
$userObjectId = (Get-AzureRmAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```
## <a name="manage-files"></a>Hantera filer

### <a name="check-for-the-existence-of-a-file"></a>Kontrollera om finns en fil.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>Överföra och ladda ned

Överför en fil.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

Ladda upp en hel mapp rekursivt.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Hämta en fil.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Hämta en hel mapp rekursivt.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Om överföring eller hämtning processen avbryts, kan du försöka återuppta processen genom att köra cmdleten igen med den ``-Resume`` flaggan.

## <a name="manage-the-u-sql-catalog"></a>Hantera U-SQL-katalogen

U-SQL-katalogen används för att strukturera data och kod så att de kan delas av U-SQL-skript. Katalogen kan möjliga med data i Azure Data Lake högsta prestanda. Mer information finns i [Använd U-SQL-katalogen](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Listobjekt i U-SQL-katalogen

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>Visa en lista med alla sammansättningar U-SQL-katalogen

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

### <a name="get-details-about-a-catalog-item"></a>Få information om ett katalogobjekt

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="store-credentials-in-the-catalog"></a>Lagra autentiseringsuppgifter i katalogen

I en U-SQL-databas, skapar du ett autentiseringsuppgiftobjekt för en databas som finns på Azure. U-SQL-autentiseringsuppgifter för närvarande den enda typen av katalogobjekt som du kan skapa med hjälp av PowerShell.

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

### <a name="add-a-firewall-rule"></a>Lägg till en brandväggsregel

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

### <a name="get-details-of-azurerm-errors"></a>Hämta information om AzureRm fel

```powershell
Resolve-AzureRmError -Last
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

### <a name="find-a-tenantid"></a>Hitta en TenantID

Från ett prenumerationsnamn:

```powershell
function Get-TenantIdFromSubcriptionName( [string] $subname )
{
    $sub = (Get-AzureRmSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubcriptionName "ADLTrainingMS"
```

Från ett prenumerations-ID:

```powershell
function Get-TenantIdFromSubcriptionId( [string] $subid )
{
    $sub = (Get-AzureRmSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubcriptionId $subid
```

Från en domänadress, till exempel ”contoso.com”

```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>En lista över alla prenumerationer och klient-ID: N

```powershell
$subs = Get-AzureRmSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Skapa ett Data Lake Analytics-konto med hjälp av en mall

Du kan också använda en Azure-resursgrupp-mall med hjälp av följande exempel: [skapa ett Data Lake Analytics-konto med hjälp av en mall](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)

## <a name="next-steps"></a>Nästa steg
* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* Kom igång med Data Lake Analytics med hjälp av [Azure-portalen](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI 2.0](data-lake-analytics-get-started-cli2.md)
* Hantera Azure Data Lake Analytics med hjälp av [Azure-portalen](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) 
