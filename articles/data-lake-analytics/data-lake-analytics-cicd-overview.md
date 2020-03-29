---
title: Konfigurera en CI/CD-pipeline för Azure Data Lake Analytics
description: Lär dig hur du konfigurerar kontinuerlig integrering och kontinuerlig distribution för Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 09/14/2018
ms.openlocfilehash: b035be727df2dfecb613da79681affd740c69bec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60333884"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Konfigurera en CI/CD-pipeline för Azure Data Lake Analytics  

I den här artikeln får du lära dig hur du konfigurerar en kontinuerlig integrations- och distributionspipelline (CI/CD) för U-SQL-jobb och U-SQL-databaser.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>Använda CI/CD för U-SQL-jobb

Azure Data Lake Tools för Visual Studio innehåller u-SQL-projekttypen som hjälper dig att organisera U-SQL-skript. Om du använder U-SQL-projektet för att hantera din U-SQL-kod blir det enklare att använda U-SQL-projektet för att hantera din U-SQL-kod.

## <a name="build-a-u-sql-project"></a>Skapa ett U-SQL-projekt

Ett U-SQL-projekt kan byggas med Microsoft Build Engine (MSBuild) genom att passera motsvarande parametrar. Följ stegen i den här artikeln för att ställa in en byggprocess för ett U-SQL-projekt.

### <a name="project-migration"></a>Projektmigrering

Innan du konfigurerar en bygguppgift för ett U-SQL-projekt kontrollerar du att du har den senaste versionen av U-SQL-projektet. Öppna U-SQL-projektfilen i redigeraren och kontrollera att du har följande importobjekt:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Om inte, har du två alternativ för att migrera projektet:

- Alternativ 1: Ändra det gamla importobjektet till föregående.
- Alternativ 2: Öppna det gamla projektet i Azure Data Lake Tools för Visual Studio. Använd en ny version än 2.3.3000.0. Den gamla projektmallen uppgraderas automatiskt till den senaste versionen. Nya projekt som skapats med nya versioner än 2.3.3000.0 använder den nya mallen.

### <a name="get-nuget"></a>Skaffa NuGet

MSBuild ger inte inbyggt stöd för U-SQL-projekt. För att få den här supporten måste du lägga till en referens för din lösning till [Paketet Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet som lägger till den språktjänst som krävs.

Om du vill lägga till NuGet-paketreferensen högerklickar du på lösningen i Visual Studio Solution Explorer och väljer **Hantera NuGet-paket**. Du kan också lägga `packages.config` till en fil som anropas i lösningsmappen och lägga följande innehåll i den:

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>Hantera U-SQL-databasreferenser

U-SQL-skript i ett U-SQL-projekt kan ha frågesatser för U-SQL-databasobjekt. I så fall måste du referera till motsvarande U-SQL-databasprojekt som innehåller objektens definition innan du skapar U-SQL-projektet. Ett exempel är när du frågar en U-SQL-tabell eller refererar till en sammansättning. 

Läs mer om [U-SQL-databasprojekt](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
>DROP-satsen kan orsaka problem med borttagning av olyckor. Om du vill aktivera DROP-satsen måste du uttryckligen ange MSBuild-argumenten. **AllowDropStatement** aktiverar icke-datarelaterad DROP-åtgärd, till exempel funktionen drop assembly och drop table. **AllowDataDropStatement** aktiverar datarelaterad DROP-åtgärd, till exempel drop table och drop-schema. Du måste aktivera AllowDropStatement innan du använder AllowDataDropStatement.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Skapa ett U-SQL-projekt med MSBuild-kommandoraden

Migrera först projektet och hämta NuGet-paketet. Anropa sedan standard-MSBuild-kommandoraden med följande ytterligare argument för att skapa ditt U-SQL-projekt: 

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

Argumentdefinitionen och värdena är följande:

* **USQLSDKPath=\<U-SQL Nuget-paket>\build\runtime**. Den här parametern refererar till installationssökvägen för NuGet-paketet för U-SQL-språktjänsten.
* **USQLTargetType=Merge eller SyntaxCheck:**
    * **Slå samman**. Kopplingsläget sammanställer bakomkodfiler. Exempel är **.cs-,** **.py-** och **.r-filer.** Det infogar det resulterande användardefinierade kodbiblioteket i U-SQL-skriptet. Exempel är en dll-binär, Python eller R-kod.
    * **SyntaxKontrollera**. SyntaxCheck-läget sammanfogar först kod bakom filer till U-SQL-skriptet. Sedan sammanställer U-SQL-skriptet för att validera din kod.
* **DataRoot=\<DataRoot-sökvägen>**. DataRoot behövs bara för SyntaxCheck-läge. När skriptet skapas med SyntaxCheck-läge kontrollerar MSBuild referenserna till databasobjekt i skriptet. Innan du skapar ställer du in en matchande lokal miljö som innehåller de refererade objekten från U-SQL-databasen i byggdatorns DataRoot-mapp. Du kan också hantera dessa databasberoenden genom [att referera till ett U-SQL-databasprojekt](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). MSBuild kontrollerar bara referenser till databasobjekt, inte filer.
* **EnableDeployment=true** eller **false**. EnableDeployment anger om det är tillåtet att distribuera refererade U-SQL-databaser under byggprocessen. Om du refererar till ett U-SQL-databasprojekt och använder databasobjekten i U-SQL-skriptet anger du den här parametern till **true**.

### <a name="continuous-integration-through-azure-pipelines"></a>Kontinuerlig integrering via Azure Pipelines

Förutom kommandoraden kan du också använda Visual Studio Build eller en MSBuild-uppgift för att skapa U-SQL-projekt i Azure Pipelines. Om du vill konfigurera en byggpipeline kontrollerar du att du lägger till två aktiviteter i byggpipelinen: en NuGet-återställningsuppgift och en MSBuild-uppgift.

![MSBuild-aktivitet för ett U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

1.  Lägg till en NuGet-återställningsuppgift för att hämta `Azure.DataLake.USQL.SDK`det lösningsreferat NuGet-paketet som innehåller , så att MSBuild kan hitta U-SQL-språkmålen. Ange**katalogen** `$(Build.SourcesDirectory)/packages` **Avancerat** > mål till om du vill använda exemplet med MSBuild-argument direkt i steg 2.

    ![NuGet-återställningsuppgift för ett U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Ange MSBuild-argument i Visual Studio-byggverktyg eller i en MSBuild-uppgift som visas i följande exempel. Du kan också definiera variabler för dessa argument i Azure Pipelines build pipeline.

    ![Definiera CI/CD MSBuild-variabler för ett U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

    ```
    /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

### <a name="u-sql-project-build-output"></a>U-SQL-projekt bygga utdata

När du har kört en version skapas alla skript i U-SQL-projektet och matas ut till en zip-fil som heter `USQLProjectName.usqlpack`. Mappstrukturen i projektet hålls i den zippade byggutgången.

> [!NOTE]
>
> Kod-bakom filer för varje U-SQL-skript kommer att slås samman som en infogad sats till skriptet bygga utdata.
>

## <a name="test-u-sql-scripts"></a>Testa U-SQL-skript

Azure Data Lake tillhandahåller testprojekt för U-SQL-skript och C# UDO/UDAG/UDF:
* Lär dig hur du [lägger till testfall för U-SQL-skript och utökad C#-kod](data-lake-analytics-cicd-test.md#test-u-sql-scripts).
* Lär dig hur du [kör testfall i Azure Pipelines](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops).

## <a name="deploy-a-u-sql-job"></a>Distribuera ett U-SQL-jobb

När du har verifierat koden via bygg- och testprocessen kan du skicka U-SQL-jobb direkt från Azure Pipelines via en Azure PowerShell-uppgift. Du kan också distribuera skriptet till Azure Data Lake Store eller Azure Blob-lagring och [köra schemalagda jobb via Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>Skicka U-SQL-jobb via Azure Pipelines

Byggutdata för U-SQL-projektet är en zip-fil som heter **USQLProjectName.usqlpack**. Zip-filen innehåller alla U-SQL-skript i projektet. Du kan använda [Azure PowerShell-uppgiften](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) i Pipelines med följande exempel på PowerShell-skript för att skicka U-SQL-jobb direkt från Azure Pipelines.

```powershell
<#
    This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and submit all scripts one-by-one.

    Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.
          
    Example :
        USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

    return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
    $usqlFiles = GetUsqlFiles

    Write-Output "$($usqlFiles.Count) jobs to be submitted..."

    # Submit each usql script and wait for completion before moving ahead.
    foreach ($usqlFile in $usqlFiles)
    {
        $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

        Write-Output "Submitting job for '{$usqlFile}'"

        $jobToSubmit = Submit-AzDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        
        LogJobInformation $jobToSubmit
        
        Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
        LogJobInformation $jobResult
    }
}

Function LogJobInformation($jobInfo)
{
    Write-Output "************************************************************************"
    Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
    Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
    Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
    Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
    Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
    Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
    Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
    if ($item -ne $null)
    {
        return $item
    }
    return ""
}

Function Main()
{
    Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
    Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
    Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

    Write-Output "Starting USQL script deployment..."
    
    SubmitAnalyticsJob

    Write-Output "Finished deployment..."
}

Main
```

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Distribuera U-SQL-jobb via Azure Data Factory

Du kan skicka U-SQL-jobb direkt från Azure Pipelines. Du kan också ladda upp de inbyggda skripten till Azure Data Lake Store eller Azure Blob-lagring och [köra schemalagda jobb via Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

Använd [Azure PowerShell-uppgiften](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) i Azure Pipelines med följande exempel på PowerShell-skript för att överföra U-SQL-skripten till ett Azure Data Lake Store-konto:

```powershell
<#
    This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.
          
    Example :
        FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DestinationFolder = "USQLScriptSource" # Destination folder in ADLS
)

Function UploadResources()
{
    Write-Host "************************************************************************"
    Write-Host "Uploading files to $ADLSName"
    Write-Host "***********************************************************************"

    $usqlScripts = GetUsqlFiles

    $files = @(get-childitem $usqlScripts -recurse)
    foreach($file in $files)
    {
        Write-Host "Uploading file: $($file.Name)"
        Import-AzDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DestinationFolder $file)" -Force
    }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-a-u-sql-database"></a>CI/CD för en U-SQL-databas

Azure Data Lake Tools för Visual Studio tillhandahåller U-SQL-databasprojektmallar som hjälper dig att utveckla, hantera och distribuera U-SQL-databaser. Läs mer om ett [U-SQL-databasprojekt](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Skapa U-SQL-databasprojekt

### <a name="get-the-nuget-package"></a>Skaffa NuGet-paketet

MSBuild ger inte inbyggt stöd för U-SQL-databasprojekt. För att få den här möjligheten måste du lägga till en referens för din lösning till [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet-paketet som lägger till den språktjänst som krävs.

Om du vill lägga till NuGet-paketreferensen högerklickar du på lösningen i Visual Studio Solution Explorer. Välj **Hantera NuGet-paket.** Sök sedan efter och installera NuGet-paketet. Du kan också lägga till en fil som heter **packages.config** i lösningsmappen och lägga följande innehåll i den:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>Skapa U-SQL ett databasprojekt med MSBuild-kommandoraden

Om du vill skapa ditt U-SQL-databasprojekt anropar du standardkommandoraden MSBuild och skickar U-SQL SDK NuGet-paketreferensen som ytterligare ett argument. Se följande exempel: 

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Argumentet `USQLSDKPath=<U-SQL Nuget package>\build\runtime` refererar till installationssökvägen för NuGet-paketet för U-SQL-språktjänsten.

### <a name="continuous-integration-with-azure-pipelines"></a>Kontinuerlig integrering med Azure Pipelines

Förutom kommandoraden kan du använda Visual Studio Build eller en MSBuild-uppgift för att skapa U-SQL-databasprojekt i Azure Pipelines. Om du vill konfigurera en bygguppgift kontrollerar du att du lägger till två aktiviteter i byggpipelinen: en NuGet-återställningsuppgift och en MSBuild-uppgift.

   ![CI/CD MSBuild-uppgift för ett U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 


1. Lägg till en NuGet-återställningsuppgift för att hämta `Azure.DataLake.USQL.SDK`det lösningsreferat NuGet-paketet, som innehåller , så att MSBuild kan hitta U-SQL-språkmålen. Ange**katalogen** `$(Build.SourcesDirectory)/packages` **Avancerat** > mål till om du vill använda exemplet med MSBuild-argument direkt i steg 2.

   ![CI/CD NuGet-uppgift för ett U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Ange MSBuild-argument i Visual Studio-byggverktyg eller i en MSBuild-uppgift som visas i följande exempel. Du kan också definiera variabler för dessa argument i Azure Pipelines build pipeline.

   ![Definiera CI/CD MSBuild-variabler för ett U-SQL-databasprojekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

   ```
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
   ```
 
### <a name="u-sql-database-project-build-output"></a>U-SQL-databas projekt bygga utdata

Byggutdata för ett U-SQL-databasprojekt är ett U-SQL-databasdistributionspaket med namnet suffixet `.usqldbpack`. Paketet `.usqldbpack` är en zip-fil som innehåller alla DDL-satser i ett enda U-SQL-skript i en DDL-mapp. Den innehåller alla **DLL-filer** och ytterligare filer för sammansättning i en temp-mapp.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Testa tabellvärdade funktioner och lagrade procedurer

Det går inte att lägga till testfall för tabellvärdade funktioner och lagrade procedurer direkt. Som en lösning kan du skapa ett U-SQL-projekt som har U-SQL-skript som anropar dessa funktioner och skriver testfall för dem. Gör så här för att ställa in testfall för tabellvärdade funktioner och lagrade procedurer som definierats i U-SQL-databasprojektet:

1.  Skapa ett U-SQL-projekt för teständamål och skriv U-SQL-skript som anropar tabellvärderade funktioner och lagrade procedurer.
2.  Lägg till en databasreferens till U-SQL-projektet. Om du vill hämta den tabellvärda funktionen och den lagrade procedurdefinitionen måste du referera till databasprojektet som innehåller DDL-satsen. Läs mer om [databasreferenser](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Lägg till testfall för U-SQL-skript som anropar tabellvärderade funktioner och lagrade procedurer. Lär dig hur du [lägger till testfall för U-SQL-skript](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>Distribuera U-SQL-databas via Azure Pipelines

`PackageDeploymentTool.exe`tillhandahåller programmerings- och kommandoradsgränssnitt som hjälper till att distribuera U-SQL-databasdistributionspaket, **.usqldbpack**. SDK ingår i [paketet U-SQL SDK NuGet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), som finns vid **build/runtime/PackageDeploymentTool.exe**. Med `PackageDeploymentTool.exe`hjälp av kan du distribuera U-SQL-databaser till både Azure Data Lake Analytics och lokala konton.

> [!NOTE]
>
> PowerShell-kommandoradsstöd och Azure Pipelines-versionsuppgiftsstöd för U-SQL-databasdistribution väntar för närvarande.
>

Gör så här för att konfigurera en databasdistributionsuppgift i Azure Pipelines:

1. Lägg till en PowerShell Script-uppgift i en pipeline för att skapa eller släppa och kör följande PowerShell-skript. Den här uppgiften hjälper till att `PackageDeploymentTool.exe` få `PackageDeploymentTool.exe`Azure SDK-samband för och . Du kan ange parametrarna **-AzureSDK** och **-DBDeploymentTool** för att läsa in beroenden och distributionsverktyget till specifika mappar. Skicka **sökvägen -AzureSDK** till `PackageDeploymentTool.exe` som **parametern -AzureSDKPath** i steg 2. 

    ```powershell
    <#
        This script is used for getting dependencies and SDKs for U-SQL database deployment.
        PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.
        
        Example :
            GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
    #>

    param (
        [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
        [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL database deployment tool
        [string]$workingfolder = "" # Folder to execute these command lines
    )

    if ([string]::IsNullOrEmpty($workingfolder))
    {
        $scriptpath = $MyInvocation.MyCommand.Path
        $workingfolder = Split-Path $scriptpath
    }
    cd $workingfolder

    echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
    echo "Downloading required packages..."

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.5.1-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.4.1-preview -outf Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
    iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.4.1-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
    Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
    Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

    echo "Copy required DLLs to output folder..."

    mkdir $AzureSDK -Force
    mkdir $DBDeploymentTool -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.Azure.Management.DataLake.Store.2.4.1-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
    copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
    copy USQLSDK\build\runtime\*.* $DBDeploymentTool
    ```

2. Lägg till en **kommandoradsuppgift** i en pipeline för `PackageDeploymentTool.exe`att skapa eller släppa och fyll i skriptet genom att anropa . `PackageDeploymentTool.exe`finns under den **$DBDeploymentTool** definierade $DBDeploymentToolmappen. Exempelskriptet är följande: 

    * Distribuera en U-SQL-databas lokalt:

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Använd interaktivt autentiseringsläge för att distribuera en U-SQL-databas till ett Azure Data Lake Analytics-konto:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Använd **utsöndrande** autentisering för att distribuera en U-SQL-databas till ett Azure Data Lake Analytics-konto:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Använd **certFile-autentisering** för att distribuera en U-SQL-databas till ett Azure Data Lake Analytics-konto:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>Parameterbeskrivningar för PackageDeploymentTool.exe

#### <a name="common-parameters"></a>Vanliga parametrar

| Parameter | Beskrivning | Standardvärde | Krävs |
|---------|-----------|-------------|--------|
|Paket|Sökvägen till driftsättningspaketet för U-SQL-databasen som ska distribueras.|null|true|
|Databas|Databasnamnet som ska distribueras till eller skapas.|master|false|
|Loggfil|Sökvägen till filen för loggning. Standard till standard ut (konsol).|null|false|
|Loggnivå|Loggnivå: Utförlig, Normal, Varning eller Fel.|LogLevel.Normal|false|

#### <a name="parameter-for-local-deployment"></a>Parameter för lokal distribution

|Parameter|Beskrivning|Standardvärde|Krävs|
|---------|-----------|-------------|--------|
|DataRoot (DataRoot)|Sökvägen till den lokala datarotmappen.|null|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Parametrar för Azure Data Lake Analytics-distribution

|Parameter|Beskrivning|Standardvärde|Krävs|
|---------|-----------|-------------|--------|
|Konto|Anger vilket Azure Data Lake Analytics-konto som ska distribueras till efter kontonamn.|null|true|
|ResourceGroup|Azure-resursgruppsnamnet för Azure Data Lake Analytics-kontot.|null|true|
|SubscriptionId|Azure-prenumerations-ID för Azure Data Lake Analytics-kontot.|null|true|
|Klientorganisation|Klientnamnet är Azure Active Directory (Azure AD) domännamn. Hitta den på sidan prenumerationshantering i Azure-portalen.|null|true|
|AzureSDKPath|Sökvägen till sökberoende sammansättningar i Azure SDK.|null|true|
|Interaktiv|Om interaktivt läge ska användas eller inte.|false|false|
|ClientId|Azure AD-program-ID krävs för icke-interaktiv autentisering.|null|Krävs för icke-interaktiv autentisering.|
|Utsöndrar|Utöndra eller lösenord för icke-interaktiv autentisering. Den bör endast användas i en betrodd och säker miljö.|null|Krävs för icke-interaktiv autentisering, annars använd SecreteFile.|
|SecreteFile|Filen sparar utsöndrar eller lösenord för icke-interaktiv autentisering. Se till att den endast kan läsas av den aktuella användaren.|null|Krävs för icke-interaktiv autentisering, annars använd Secrete.|
|CertFile (CertFile)|Filen sparar X.509-certifiering för icke-interaktiv autentisering. Standard är att använda klientutönstning autentisering.|null|false|
| Projektprefix | Prefixet för databasdistribution av ett U-SQL DDL-jobb. | Deploy_ + DateTime.Now | false |

## <a name="next-steps"></a>Nästa steg

- [Så här testar du din Azure Data Lake Analytics-kod](data-lake-analytics-cicd-test.md).
- [Kör U-SQL-skript på den lokala datorn](data-lake-analytics-data-lake-tools-local-run.md).
- [Använd U-SQL-databasprojekt för att utveckla U-SQL-databas](data-lake-analytics-data-lake-tools-develop-usql-database.md).
