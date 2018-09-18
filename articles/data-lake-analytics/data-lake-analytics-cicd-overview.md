---
title: Hur du ställer in en CI/CD-pipeline för Azure Data Lake Analytics
description: Lär dig hur du ställer in kontinuerlig integrering och kontinuerlig distribution för Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 09/14/2018
ms.openlocfilehash: a0fd7ff86fe7502ddc54e2533c5f79950b5f8082
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45731146"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Hur du ställer in en CI/CD-pipeline för Azure Data Lake Analytics  

I den här artikeln får du lära dig hur du ställer in en kontinuerlig integrering och distribution (CI/CD) pipeline för U-SQL-jobb och U-SQL-databaser.  

## <a name="use-cicd-for-u-sql-jobs"></a>Använd CI/CD för U-SQL-jobb

Azure Data Lake Tools för Visual Studio innehåller U-SQL-projekttyp som hjälper dig att organisera U-SQL-skript. Hantera dina U-SQL-kod med hjälp av U-SQL-projekt är ytterligare scenarier för CI/CD enkelt.

## <a name="build-a-u-sql-project"></a>Skapa ett U-SQL-projekt

Ett U-SQL-projekt kan byggas med Microsoft Build Engine (MSBuild) genom att ange motsvarande parametrar. Följ stegen i den här artikeln för att ställa in en build-process för ett U-SQL-projekt.

### <a name="project-migration"></a>Projekt-migrering

Innan du konfigurerar en build-aktivitet för ett U-SQL-projekt, kontrollera att du har den senaste versionen av U-SQL-projekt. Öppna projektfilen U-SQL i redigeringsprogrammet och kontrollera att du har dessa importera objekt:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Om inte, du har två alternativ för att migrera projektet:

- Alternativ 1: Ändra gammalt importera objekt till den föregående.
- Alternativ 2: Öppna det gamla projektet i Azure Data Lake Tools för Visual Studio. Använda en version som är nyare än 2.3.3000.0. Den gamla projektmallen uppgraderas automatiskt till den senaste versionen. Nya projekt som skapats med versioner som är nyare än 2.3.3000.0 använda den nya mallen.

### <a name="get-nuget"></a>Hämta NuGet

MSBuild ger inte inbyggt stöd för U-SQL-projekt. För att få det här stödet kan du behöva lägga till en referens för din lösning till den [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet-paketet som lägger till tjänsten nödvändiga språkpaket.

Lägg till referens för NuGet-paketet genom att högerklicka på lösningen i Visual Studio Solution Explorer och välj **hantera NuGet-paket**. Du kan också lägga till en fil med namnet `packages.config` i lösningsmappen och placera följande innehåll till den:

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>Hantera referenser för U-SQL-databas

U-SQL-skript i ett U-SQL-projekt kan ha fråga-uttryck för U-SQL database-objekt. I så fall måste du referera motsvarande U-SQL database-projekt som innehåller definitionen för objekten innan du skapar U-SQL-projekt. Ett exempel är när du frågar en U-SQL-tabell eller referera till en sammansättning. 

Läs mer om [U-SQL database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
>U-SQL database-projekt är för närvarande i offentlig förhandsversion. Om du har DROP-uttryck i projektet, misslyckas versionen. Släpp-instruktionen får snart.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Skapa ett U-SQL-projekt med MSBuild-kommandorad

Först migrera projektet och hämta NuGet-paketet. Anropa sedan standard MSBuild-kommandoraden med följande ytterligare argument att bygga projektet U-SQL: 

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

Definition av argument och värden är följande:

* **USQLSDKPath = < U-SQL-Nuget-paketet > \build\runtime**. Den här parametern refererar till installationssökvägen för NuGet-paketet för tjänsten U-SQL-språk.
* **USQLTargetType = Merge eller SyntaxCheck**:
    * **Sammanfoga**. Sammanfoga kompilerar bakomliggande kod filer. Exempel är **.cs**, **.py**, och **.r** filer. Den inlines resulterande biblioteket användardefinierad kod i U-SQL-skript. Exempel är en dll-binär, Python eller R kod.
    * **SyntaxCheck**. SyntaxCheck läge sammanfogar först filer med bakomliggande kod i U-SQL-skriptet. Den kompilerar sedan U-SQL-skript för att verifiera din kod.
* **DataRoot =<DataRoot path>**. DataRoot krävs endast för SyntaxCheck läge. När den bygger skriptet med SyntaxCheck läge kontrollerar MSBuild referenser till databasobjekt i skriptet. Innan du bygga, ställa in en matchande lokal miljö som innehåller de refererade objekt i U-SQL-databas i build-datorns DataRoot mapp. Du kan också hantera dessa databasen beroenden av [refererar till ett U-SQL-databasprojekt](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). MSBuild kontrollerar endast databasen objektreferenser, inte filer.
* **EnableDeployment = true** eller **FALSKT**. EnableDeployment anger om det har rätt för att distribuera refererade U-SQL-databaser under skapandeprocessen. Om du refererar till ett projekt för U-SQL-databas och använder databasobjekt i U-SQL-skript, ange den här parametern **SANT**.

### <a name="continuous-integration-with-azure-devops-pipelines"></a>Kontinuerlig integrering med Azure DevOps-Pipelines

Förutom command line använda du också Visual Studio versionen eller en MSBuild-uppgift för att skapa U-SQL-projekt i tjänsten Azure DevOps Pipelines (Pipelines). Om du vill konfigurera en build-pipeline, bör du lägga till två aktiviteter i pipelinen build: en restore NuGet-uppgift och en MSBuild-uppgift.

![MSBuild-aktiviteten för ett U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

1.  Lägg till en aktivitet för återställning av NuGet för att hämta lösningen refererar till NuGet-paketet som innehåller `Azure.DataLake.USQL.SDK`, så att MSBuild hittar mål för U-SQL-språk. Ange **Avancerat** > **målkatalogen** till `$(Build.SourcesDirectory)/packages` om du vill använda exemplet för MSBuild-argument direkt i steg 2.

    ![NuGet Återställ aktivitet för ett U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Ange MSBuild-argument i Visual Studio-genereringsverktyg eller i en MSBuild-uppgift som du ser i följande exempel. Eller du kan definiera variabler för de här argumenten i Pipelines build-pipeline.

    ![Definiera variabler för CI/CD MSBuild för ett U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

### <a name="u-sql-project-build-output"></a>Resultatet för U-SQL-projekt

När du kör en version, alla skript i U-SQL-projektet har skapats och spara till en zip-fil som heter `USQLProjectName.usqlpack`. Mappstrukturen i projektet sparas i resultatet från komprimerade.

> [!NOTE]
>
> Bakomliggande kod filer för varje U-SQL-skript tillsammans som en infogad instruktion till build-skriptet returnerade.
>

## <a name="test-u-sql-scripts"></a>Testa U-SQL-skript

Azure Data Lake innehåller testprojekt för U-SQL-skript och C# UDO/UDAG/UDF:
* Lär dig hur du [lägga till testfall för U-SQL-skript och utökade C#-kod](data-lake-analytics-cicd-test.md#test-u-sql-scripts).
* Lär dig hur du [kör testfall i Azure DevOps](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops).

## <a name="deploy-a-u-sql-job"></a>Distribuera ett U-SQL-jobb

Du kan skicka U-SQL-jobb direkt från Azure DevOps via en Azure PowerShell-uppgift när du har kontrollerat kod genom skapa och testa processen. Du kan också distribuera skriptet till Azure Data Lake Store eller Azure Blob storage och [köra schemalagda jobb via Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

### <a name="submit-u-sql-jobs-through-azure-devops-pipelines"></a>Skicka U-SQL-jobb via Azure DevOps-Pipelines

Versionen utdata av U-SQL-projektet en zip-fil kallas **USQLProjectName.usqlpack**. Zip-filen innehåller alla U-SQL-skript i projektet. Du kan använda den [Azure PowerShell-uppgift](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) i Pipelines med följande PowerShell-exempelskript för att skicka U-SQL-jobb direkt från tjänsten Azure DevOps-Pipelines.

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

        $jobToSubmit = Submit-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        
        LogJobInformation $jobToSubmit
        
        Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
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

Du kan skicka U-SQL-jobb direkt från tjänsten Azure DevOps-Pipelines. Eller du kan överföra de inbyggda skript till Azure Data Lake Store eller Azure Blob storage och [köra schemalagda jobb via Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

Använd den [Azure PowerShell-uppgift](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) i Azure DevOps med följande PowerShell-exempelskript för att ladda upp U-SQL-skript till ett Azure Data Lake Store-konto:

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
    [Parameter(Mandatory=$false)][string]$DesitinationFolder = "USQLScriptSource" # Desitination folder in ADLS
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
        Import-AzureRmDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DesitinationFolder $file)" -Force
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

Azure Data Lake Tools för Visual Studio innehåller projektmallar för U-SQL-databas som hjälper dig att utveckla, hantera och distribuera U-SQL-databaser. Läs mer om en [U-SQL database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Skapa U-SQL database-projekt

### <a name="get-the-nuget-package"></a>Hämta NuGet-paketet

MSBuild ger inte inbyggt stöd för U-SQL-databasprojekt. För att få den här möjligheten kan du behöva lägga till en referens för din lösning till den [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet-paketet som lägger till tjänsten nödvändiga språkpaket.

Lägg till referens för NuGet-paketet genom att högerklicka på lösningen i Visual Studio Solution Explorer. Välj **hantera NuGet-paket**. Sök sedan efter och installera NuGet-paketet. Du kan också lägga till en fil med namnet **packages.config** i lösningsmappen och placera följande innehåll till den:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>Skapa U-SQL database-projekt med MSBuild-kommandorad

Anropa standard MSBuild-kommandoraden för att bygga projektet U-SQL-databas och skicka referens för U-SQL SDK NuGet-paketet som ett ytterligare argument. Se följande exempel: 

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Argumentet `USQLSDKPath=<U-SQL Nuget package>\build\runtime` refererar till installationssökvägen för NuGet-paketet för tjänsten U-SQL-språk.

### <a name="continuous-integration-with-the-azure-devops-pipelines-service"></a>Kontinuerlig integrering med tjänsten Azure DevOps-Pipelines

Förutom kommandoraden, kan du använda Visual Studio-versionen eller en MSBuild-uppgift för att skapa projekt för U-SQL-databas i Azure DevOps-Pipelines. Om du vill konfigurera en build-aktivitet, se till att lägga till två aktiviteter i pipelinen build: en restore NuGet-uppgift och en MSBuild-uppgift.

   ![CI/CD MSBuild-aktiviteten för ett U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 


1.  Lägg till en aktivitet för återställning av NuGet för att hämta lösningen refererar till NuGet-paketet, vilket innefattar `Azure.DataLake.USQL.SDK`, så att MSBuild hittar mål för U-SQL-språk. Ange **Avancerat** > **målkatalogen** till `$(Build.SourcesDirectory)/packages` om du vill använda exemplet för MSBuild-argument direkt i steg 2.

    ![CI/CD-NuGet-uppgift för ett U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Ange MSBuild-argument i Visual Studio-genereringsverktyg eller i en MSBuild-uppgift som du ser i följande exempel. Eller du kan definiera variabler för de här argumenten i Azure DevOps build-pipeline.

   ![Definiera variabler för CI/CD MSBuild för ett projekt för U-SQL-databas](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
    ```
 
### <a name="u-sql-database-project-build-output"></a>Resultatet för U-SQL-databas-projekt

Skapa utdata för ett projekt för U-SQL-databas är ett U-SQL-databas distributionspaket, med namnet med suffixet `.usqldbpack`. Den `.usqldbpack` paketet är en zip-fil som innehåller alla DDL-instruktionerna i ett enda U-SQL-skript i en DDL-mapp. Det innehåller alla **DLL-filer** och ytterligare filer för sammansättningen i en tillfällig mapp.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Testa tabellvärdesfunktioner och lagrade procedurer

Att lägga till testfall för tabellvärdesfunktioner och lagrade procedurer direkt stöds inte för närvarande. Du kan skapa ett U-SQL-projekt som har U-SQL-skript som anropar dessa funktioner och skriva testfall för dem som en lösning. Vidta följande steg för att ställa in testfall för tabellvärdesfunktioner och lagrade procedurer som definierats i U-SQL database-projekt:

1.  Skapa ett U-SQL-projekt för testning och skriver U-SQL-skript som anropar tabellvärdesfunktioner och lagrade procedurer.
2.  Lägg till en databas som referens i U-SQL-projektet. För att få tabellvärdesfunktion och lagrade Procedurdefinition, måste du referera databasprojektet som innehåller DDL-instruktion. Läs mer om [databasen referenser](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Lägg till testfall för U-SQL-skript som anropar tabellvärdesfunktioner och lagrade procedurer. Lär dig hur du [lägga till testfall för U-SQL-skript](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-the-azure-devops-pipelines-service"></a>Distribuera U-SQL-databas via tjänsten Azure DevOps-Pipelines

`PackageDeploymentTool.exe` programmering och kommandoradsverktyget gränssnitt som hjälper att distribuera paket för distribution av U-SQL-databas, **.usqldbpack**. SDK: N som ingår i den [U-SQL SDK NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), som finns i **build/runtime/PackageDeploymentTool.exe**. Med hjälp av `PackageDeploymentTool.exe`, du kan distribuera U-SQL-databaser till både Azure Data Lake Analytics och lokala konton.

> [!NOTE]
>
> Stöd för PowerShell-kommandoraden och Pipelines publiceringsuppgift stöd för distribution av U-SQL-databaser är för närvarande väntar.
>

Vidta följande steg för att ställa in en uppgift för distribution av databasen i Pipelines:

1. Lägg till en PowerShell-skript-aktivitet i en version eller släpp pipeline och kör följande PowerShell-skript. Den här uppgiften hjälper till att hämta Azure SDK-beroendena för `PackageDeploymentTool.exe` och `PackageDeploymentTool.exe`. Du kan ange den **- AzureSDK** och **- DBDeploymentTool** parametrar för att läsa in beroenden och distributionsverktyg till specifika mappar. Skicka den **- AzureSDK** sökvägen till `PackageDeploymentTool.exe` som den **- AzureSDKPath** parameter i steg 2. 

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

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.2.3-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.3.3-preview -outf Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
    iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.3.3-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
    Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
    Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

    echo "Copy required DLLs to output folder..."

    mkdir $AzureSDK -Force
    mkdir $DBDeploymentTool -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.Azure.Management.DataLake.Store.2.3.3-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
    copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
    copy USQLSDK\build\runtime\*.* $DBDeploymentTool
    ```

2. Lägg till en **kommandoradsaktivitet** i en pipeline för bygge eller versionen och Fyll i skriptet genom att anropa `PackageDeploymentTool.exe`. `PackageDeploymentTool.exe` finns under det definierade **$DBDeploymentTool** mapp. Exempelskriptet är följande: 

    * Distribuera en lokal U-SQL-databas:

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Använd interaktiva autentiseringsläge för att distribuera en U-SQL-databas till ett Azure Data Lake Analytics-konto:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Använd **secrete** autentisering för att distribuera en U-SQL-databas till ett Azure Data Lake Analytics-konto:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Använd **certifikatfil** autentisering för att distribuera en U-SQL-databas till ett Azure Data Lake Analytics-konto:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>Beskrivningar av parametern PackageDeploymentTool.exe

#### <a name="common-parameters"></a>Gemensamma parametrar

| Parameter | Beskrivning | Standardvärde | Krävs |
|---------|-----------|-------------|--------|
|Paket|Sökvägen till distributionspaketet för U-SQL-databas som ska distribueras.|Null|true|
|Databas|Namnet på databasen som ska distribueras till eller skapats.|master|false|
|Loggfil|Sökvägen till filen för loggning. Som standard till standard ut (konsol).|Null|false|
|LogLevel|Certifikatutfärdarnivå: Verbose, Normal, varning eller fel.|LogLevel.Normal|false|

#### <a name="parameter-for-local-deployment"></a>Parametern för lokal distribution

|Parameter|Beskrivning|Standardvärde|Krävs|
|---------|-----------|-------------|--------|
|DataRoot|Sökvägen till rotmappen för lokala data.|Null|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Parametrar för Azure Data Lake Analytics-distribution

|Parameter|Beskrivning|Standardvärde|Krävs|
|---------|-----------|-------------|--------|
|Konto|Anger vilket Azure Data Lake Analytics-konto för distribution till efter kontonamn.|Null|true|
|ResourceGroup|Azure resursgruppens namn för Azure Data Lake Analytics-konto.|Null|true|
|SubscriptionId|Azure prenumerations-ID för Azure Data Lake Analytics-konto.|Null|true|
|Klientorganisation|Innehavarens namn är domännamnet i Azure Active Directory (AD Azure). Hitta den på sidan för hantering av prenumeration i Azure-portalen.|Null|true|
|AzureSDKPath|Sökvägen som ska genomsökas beroende sammansättningar i Azure SDK.|Null|true|
|Interaktiv|Om eller inte ska användas interaktivt läge för autentisering.|false|false|
|ClientId|Azure AD-program-ID krävs för icke-interaktiv autentisering.|Null|Krävs för icke-interaktiv autentisering.|
|Secrete|Secrete eller lösenord för icke-interaktiv autentisering. Den bör användas endast i en tillförlitlig och säker miljö.|Null|Krävs för icke-interaktiv autentisering, eller så Använd SecreteFile.|
|SecreteFile|Filen sparas secrete eller lösenord för icke-interaktiv autentisering. Se till att hålla den läsbara bara av den aktuella användaren.|Null|Krävs för icke-interaktiv autentisering, eller så Använd Secrete.|
|Certifikatfil|Filen sparas X.509-certifiering för icke-interaktiv autentisering. Standardinställningen är att använda klienten secrete autentisering.|Null|false|
| JobPrefix | Prefix för databasdistribution av ett U-SQL DDL-jobb. | Deploy_ + DateTime.Now | false |

## <a name="next-steps"></a>Nästa steg

- [Så här testar du din Azure Data Lake Analytics-kod](data-lake-analytics-cicd-test.md).
- [Kör U-SQL-skript på den lokala datorn](data-lake-analytics-data-lake-tools-local-run.md).
- [Använd U-SQL database-projekt att utveckla U-SQL-databas](data-lake-analytics-data-lake-tools-develop-usql-database.md).
