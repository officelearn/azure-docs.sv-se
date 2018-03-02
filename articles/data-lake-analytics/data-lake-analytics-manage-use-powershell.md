---
title: "Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell | Microsoft Docs"
description: "Lär dig hur du hanterar Data Lake Analytics-konton, datakällor, jobb och katalogobjekt. "
services: data-lake-analytics
documentationcenter: 
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/23/2017
ms.author: mahi
ms.openlocfilehash: dd81e9d6c91387b3873593b84e952ca4f2546c57
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Lär dig mer om att hantera Azure Data Lake Analytics-konton, datakällor, jobb och katalogobjekt med hjälp av Azure PowerShell. 

## <a name="prerequisites"></a>Förutsättningar

När du skapar ett Data Lake Analytics-konto som du behöver veta:

* **Prenumerations-ID**: Azure prenumerations-ID som Data Lake Analytics-kontot finns.
* **Resursgruppen**: namnet på Azure-resursgrupp som innehåller ditt Data Lake Analytics-konto.
* **Data Lake Analytics-kontonamn**: kontonamnet får bara innehålla gemena bokstäver och siffror.
* **Data Lake Store-standardkonto**: Varje Data Lake Analytics-konto har ett Data Lake Store-standardkonto. Dessa konton måste vara på samma plats.
* **Plats**: platsen för ditt Data Lake Analytics-konto, till exempel ”USA, östra 2” eller andra platser som stöds. Platser som stöds kan visas på vår [sida med priser](https://azure.microsoft.com/pricing/details/data-lake-analytics/).

PowerShell-kodfragmenten i den här självstudien använder dessa variabler för att lagra informationen

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in"></a>Logga in

Logga in med ett prenumerations-id.

```powershell
Login-AzureRmAccount -SubscriptionId $subId
```

Logga in med ett prenumerationsnamn.

```
Login-AzureRmAccount -SubscriptionName $subname 
```

Den `Login-AzureRmAccount` cmdlet efterfrågar alltid autentiseringsuppgifter. Du kan undvika uppmanas med hjälp av följande cmdlets:

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

## <a name="managing-accounts"></a>Hantera konton

### <a name="create-a-data-lake-analytics-account"></a>Skapa ett Data Lake Analytics-konto

Om du inte redan har en [resursgruppen](../azure-resource-manager/resource-group-overview.md#resource-groups) om du vill använda, skapa en. 

```powershell
New-AzureRmResourceGroup -Name  $rg -Location $location
```

Alla Data Lake Analytics-konton kräver ett Data Lake Store-standardkonto som används för att lagra loggar. Du kan återanvända ett befintligt konto eller skapa ett konto. 

```powershell
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

När en resursgrupp och ett Data Lake Store-konto är tillgängligt skapar du ett Data Lake Analytics-konto.

```powershell
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-information-about-an-account"></a>Hämta information om ett konto

Få information om ett konto.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

Kontrollera om finns ett visst Data Lake Analytics-konto. Cmdleten returnerar antingen `$true` eller `$false`.

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

Kontrollera om finns ett visst Data Lake Store-konto. Cmdleten returnerar antingen `$true` eller `$false`.

```powershell
Test-AdlStoreAccount -Name $adls
```

### <a name="listing-accounts"></a>Visar en lista över konton

Lista över Data Lake Analytics-konton i den aktuella prenumerationen.

```powershell
Get-AdlAnalyticsAccount
```

Lista över Data Lake Analytics-konton inom en viss resursgrupp.

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

## <a name="managing-firewall-rules"></a>Hantera brandväggsregler

Visa en lista med brandväggsregler.

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

Lägg till en brandväggsregel.

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

Ändra en brandväggsregel.

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

Ta bort en brandväggsregel.

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

Tillåt Azure IP-adresser.

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="managing-data-sources"></a>Hantera datakällor
Azure Data Lake Analytics stöder för närvarande följande datakällor:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

När du skapar ett Analytics-konto, måste du ange ett Data Lake Store-konto för att vara standarddatakälla. Data Lake Store-standardkontot används för att spara jobbet metadata och jobbet granskningsloggar. När du har skapat ett Data Lake Analytics-konto kan du lägga till ytterligare Data Lake Store-konton och/eller Storage-konton. 

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

### <a name="submit-a-string-as-a-u-sql-script"></a>Skicka en sträng som ett U-SQL-skript

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

### <a name="submit-a-file-as-a-u-sql-script"></a>Skicka en fil som ett U-SQL-skript

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

## <a name="list-jobs-in-an-account"></a>Lista över jobb i ett konto

### <a name="list-all-the-jobs-in-the-account"></a>Räkna upp alla jobb för kontot. 

Resultatet innehåller de jobb som körs för närvarande och de jobb som nyss blev klara.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>Lista över de x främsta jobb

Som standard för jobben i listan sorteras i överföringstid. Så att de nyligen skickade jobb visas först. Som standard i ADLA konto kommer ihåg jobb i 180 dagar men cmdleten Get-AdlJob som standard returnerar endast de första 500. Använd - översta parametern för att visa en lista med ett visst antal jobb.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-based-on-the-value-of-job-property"></a>Lista över jobb baserat på värdet för egenskapen jobb

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

Den `-Submitter` parametern hjälper dig att identifiera vem som har skickat ett jobb.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

Den `-SubmittedAfter` är användbart i filtrering för ett tidsintervall.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="analyzing-job-history"></a>Analysera jobbhistorik

Använda Azure PowerShell för att analysera historiken för jobbet som har körts i Data Lake analytics är en kraftfull teknik. Du kan använda den och få insikter om användning och kostnader. Du kan lära dig mer genom att titta på den [jobbet historik analys exempel lagringsplatsen](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="get-information-about-pipelines-and-recurrences"></a>Hämta information om pipelines och repetitioner

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

## <a name="get-information-about-a-job"></a>Hämta information om ett jobb

### <a name="get-job-status"></a>Hämta jobbstatus

Hämta status för ett specifikt jobb.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="examine-the-job-outputs"></a>Granska utdata för jobbet

När jobbet är slut kan du kontrollera att filen finns genom att lista filer i en mapp.

```powershell
Get-AdlStoreChildItem -Account $adls -Path "/"
```

## <a name="manage-running-jobs"></a>Hantera jobb som körs

### <a name="cancel-a-job"></a>Avbryta ett jobb

```powershell
Stop-AdlJob -Account $adls -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Vänta tills ett jobb ska slutföras

I stället för att upprepa `Get-AdlAnalyticsJob` tills ett jobb är klar kan du använda den `Wait-AdlJob` för att vänta på att jobbet ska sluta.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
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

## <a name="check-for-the-existence-of-a-file"></a>Kontrollera om finns en fil.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading"></a>Överföra och ladda ned

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

## <a name="manage-catalog-items"></a>Hantera katalogobjekt

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

Lista över alla sammansättningar i alla databaser i en ADLA.

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

### <a name="create-credentials-in-a-catalog"></a>Skapa autentiseringsuppgifter i en katalog

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

### <a name="get-basic-information-about-an-adla-account"></a>Hämta grundläggande information om ett ADLA konto

Ges ett kontonamn hämtar följande kod grundläggande information om kontot

```
$adla_acct = Get-AdlAnalyticsAccount -Name "saveenrdemoadla"
$adla_name = $adla_acct.Name
$adla_subid = $adla_acct.Id.Split("/")[2]
$adla_sub = Get-AzureRmSubscription -SubscriptionId $adla_subid
$adla_subname = $adla_sub.Name
$adla_defadls_datasource = Get-AdlAnalyticsDataSource -Account $adla_name  | ? { $_.IsDefault } 
$adla_defadlsname = $adla_defadls_datasource.Name

Write-Host "ADLA Account Name" $adla_name
Write-Host "Subscription Id" $adla_subid
Write-Host "Subscription Name" $adla_subname
Write-Host "Defautl ADLS Store" $adla_defadlsname
Write-Host 

Write-Host '$subname' " = ""$adla_subname"" "
Write-Host '$subid' " = ""$adla_subid"" "
Write-Host '$adla' " = ""$adla_name"" "
Write-Host '$adls' " = ""$adla_defadlsname"" "
```

## <a name="working-with-azure"></a>Arbeta med Azure

### <a name="get-details-of-azurerm-errors"></a>Hämta information om AzureRm fel

```powershell
Resolve-AzureRmError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator"></a>Kontrollera om du kör som administratör

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

Från ett prenumerations-id:

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

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>En lista över alla prenumerationer och klient-ID: n

```powershell
$subs = Get-AzureRmSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Skapa ett Data Lake Analytics-konto med hjälp av en mall

Du kan också använda en Azure-resursgrupp-mallen med följande PowerShell-skript:

```powershell
$subId = "<Your Azure Subscription ID>"

$rg = "<New Azure Resource Group Name>"
$location = "<Location (such as East US 2)>"
$adls = "<New Data Lake Store Account Name>"
$adla = "<New Data Lake Analytics Account Name>"

$deploymentName = "MyDataLakeAnalyticsDeployment"
$armTemplateFile = "<LocalFolderPath>\azuredeploy.json"  # update the JSON template path 

# Log in to Azure
Login-AzureRmAccount -SubscriptionId $subId

# Create the resource group
New-AzureRmResourceGroup -Name $rg -Location $location

# Create the Data Lake Analytics account with the default Data Lake Store account.
$parameters = @{"adlAnalyticsName"=$adla; "adlStoreName"=$adls}
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $rg -TemplateFile $armTemplateFile -TemplateParameterObject $parameters 
```

Mer information finns i [distribuera ett program med Azure Resource Manager-mall](../azure-resource-manager/resource-group-template-deploy.md) och [redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md).

**Exempelmall**

Spara följande text som en `.json` filen och sedan använda föregående PowerShell-skriptet för att använda mallen. 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adlAnalyticsName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Analytics account to create."
      }
    },
    "adlStoreName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Store account to create."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('adlStoreName')]",
      "type": "Microsoft.DataLakeStore/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ ],
      "tags": { }
    },
    {
      "name": "[parameters('adlAnalyticsName')]",
      "type": "Microsoft.DataLakeAnalytics/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
      "tags": { },
      "properties": {
        "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
        "dataLakeStoreAccounts": [
          { "name": "[parameters('adlStoreName')]" }
        ]
      }
    }
  ],
  "outputs": {
    "adlAnalyticsAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
    },
    "adlStoreAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg
* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* Kom igång med Data Lake Analytics med hjälp av [Azure-portalen](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI 2.0](data-lake-analytics-get-started-cli2.md)
* Hantera Azure Data Lake Analytics med hjälp av [Azure-portalen](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) 
