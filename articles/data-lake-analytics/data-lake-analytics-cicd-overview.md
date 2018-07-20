---
title: Hur du ställer in CI/CD-pipeline för Azure Data Lake Analytics | Microsoft Docs
description: Lär dig hur du ställer in kontinuerlig integrering och kontinuerlig distribution för Azure Data Lake Analytics.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: c069bc2a6147a021ea9bdf37e2926d5c8f33281c
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145017"
---
# <a name="how-to-set-up-cicd-pipeline-for-azure-data-lake-analytics"></a>Hur du ställer in CI/CD-pipeline för Azure Data Lake Analytics

I det här dokumentet har du lära dig hur du ställer in CI/CD-pipeline för U-SQL-jobb och U-SQL-databaser.

## <a name="cicd-for-u-sql-job"></a>CI/CD för U-SQL-jobb

Azure Data Lake Tools för Visual Studio innehåller U-SQL-projekttyp som hjälper dig att organisera U-SQL-skript. Hantera dina U-SQL-kod med hjälp av U-SQL-projekt gör ytterligare scenarier för CI/CD enkelt.

## <a name="build-u-sql-project"></a>Skapa U-SQL-projekt

U-SQL-projekt kan byggas med MSBuild genom att ange motsvarande parametrar. Följ stegen nedan för att ställa in skapandeprocessen för U-SQL-projekt.

### <a name="project-migration"></a>Projekt-migrering

Innan du konfigurerar build-uppgift för U-SQL-projekt, se till att använda den senaste versionen av U-SQL-projekt. Öppna projektfilen U-SQL i redigeraren och kontrollera om du har följande importera objekt:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Om inte, du har två alternativ för att migrera projektet:

- Alternativ 1: Ändra gammalt importera objekt till det som anges ovan.
- Alternativ 2: Öppna det gamla projektet i Azure Data Lake Tools för Visual Studio efter version 2.3.3000.0. Den gamla projektmallen uppgraderas automatiskt till den senaste versionen. Det nya projektet som du skapat när du har version 2.3.3000.0 använder den nya mallen direkt.

### <a name="get-nuget-package"></a>Hämta NuGet-paketet

MSBuild ger inte inbyggt stöd för U-SQL-projekttyp. Om du vill lägga till den här möjligheten, du måste lägga till en referens för din lösning till den [Microsoft.Azure.DataLake.USQL.SDK Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) som lägger till tjänsten nödvändiga språkpaket.

Lägg till referens för NuGet-paketet, kan du högerklicka på lösningen i Solution Explorer och välja **hantera NuGet-paket**. Du kan också lägga till en fil med namnet `packages.config` i lösningsmappen och Lägg till nedan innehållet i den.

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>Hantera referenser för U-SQL-databas

Om U-SQL-skript i U-SQL-projektet har fråga-uttryck för U-SQL database-objekt, till exempel fråga en U-SQL-tabell eller referera till en sammansättning måste du referera till motsvarande U-SQL database-projekt som innehåller definitionen av de här objekten innan att skapa den här U-SQL-projekt.

[Läs mer om U-SQL database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md)

>[!NOTE]
>U-SQL database-projekt är för närvarande i offentlig förhandsversion. Om du har DROP-uttryck i projektet, misslyckas versionen. Släpp-instruktionen får snart.
>

### <a name="build-u-sql-project-with-msbuild-command-line"></a>Skapa U-SQL-projekt med MSBuild-kommandorad

När du migrerar projektet och hämta NuGet-paketet kan anropa du standard MSBuild-kommandoraden med ytterligare argument nedan för att bygga projektet U-SQL:

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

Definition av argument och värden är:

* USQLSDKPath = < U-SQL-Nuget-paketet > \build\runtime: den här parametern refererar till installationssökvägen för NuGet-paketet för tjänsten U-SQL-språk.
* USQLTargetType = Merge eller SyntaxCheck:
    * Sammanslagning: Sammanfoga kompilerar bakomliggande kod filer, som .cs, .py och .r-filer och inlines resulterande användardefinierad kod-bibliotek (t.ex en dll-binär, Python och R-kod) i U-SQL-skript.
    * SyntaxCheck: SyntaxCheck läge först sammanfogar filer med bakomliggande kod i U-SQL-skript och kompilerar U-SQL-skript för att verifiera din kod.
* DataRoot =<DataRoot path>: DataRoot krävs endast för SyntaxCheck läge. När du skapar skriptet med SyntaxCheck läge kontrollerar MSBuild referenser i skriptet till databasobjekt. Se till att ställa in en matchande lokal miljö som innehåller de refererade objekt från U-SQL-databasen på build-datorns DataRoot mappen innan byggnad. Du kan också hantera dessa databasen beroenden av [refererar till ett U-SQL-databasprojekt](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). Observera att kontrollerar MSBuild endast database objekt referens, inte filer.
* EnableDeployment = SANT eller FALSKT: EnableDeployment anger om det går att distribuera refererade U-SQL-databaser under skapandeprocessen. Om du referera till projektet för U-SQL-databas och använder databasobjekt i U-SQL-skript, kan du ange den här parametern till true.

### <a name="continuous-integration-with-visual-studio-team-service"></a>Kontinuerlig integrering med Visual Studio Team Service

Förutom kommandoraden, kan kunder även använda Visual Studio-versionen eller MSBuild för att skapa U-SQL-projekt i Visual Studio Team Service. Om du vill konfigurera build uppgiften att se till att:

1.  Lägg till NuGet återställning aktiviteten för att hämta lösningen refererar till NuGet-paketet, inklusive `Azure.DataLake.USQL.SDK`, så att MSBuild hittar mål för U-SQL-språk. Ange **Avancerat > målkatalogen** som `$(Build.SourcesDirectory)/packages` om du vill använda exemplet för MSBuild-argument direkt i steg 2.

    ![Data Lake ange CI CD MSBuild-uppgift för U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

    ![Data Lake ange CI CD Nuget-uppgift för U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Ange MSBuild-argument, och du kan ange argumenten i Visual Studio-versionen eller MSBuild aktivitet, som nedan eller du kan definiera variabler för de här argumenten i VSTS byggesdefinition.

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

    ![Data Lake ange CI CD MSBuild variabler för U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

### <a name="u-sql-project-build-output"></a>Resultatet för U-SQL-projekt

När du har kört build, alla skript i U-SQL-projektet har skapats och för utdata till en zip-fil som heter `USQLProjectName.usqlpack`. Mappstrukturen i ditt projekt kommer att behållas i komprimerade resultatet.

>[!NOTE]
>
>Koden bakom filen för varje U-SQL-skript tillsammans som infogade-uttrycket för att skapa utdata från skriptet.
>

## <a name="test-u-sql-script"></a>Testa U-SQL-skript

Azure Data Lake innehåller testprojekt för U-SQL-skript och C# UDO/UDAG/UDF:
* [Lär dig hur du lägger till testfall för U-SQL-skript och utökade C#-kod](data-lake-analytics-cicd-test.md#test-u-sql-scripts)
* [Lär dig hur du kör dessa testfall i Visual Studio Team Service](data-lake-analytics-cicd-test.md#run-test-cases-in-visual-studio-team-service)

## <a name="u-sql-job-deployment"></a>Distribution av U-SQL-jobb

När du har verifierat kod genom skapa och testa processen, kan du skicka U-SQL-jobb direkt från Visual Studio Team Service via **Azure PowerShell-uppgift**. Du kan också distribuera skriptet till Azure Data Lake Store/Azure Blob Storage och [köra schemalagda jobb via Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

### <a name="submit-u-sql-jobs-through-visual-studio-team-service"></a>Skicka U-SQL-jobb via Visual Studio Team Service

Versionen utdata av U-SQL-projektet en zip-fil kallas **USQLProjectName.usqlpack** inkluderar alla U-SQL-skript i projektet. Du kan använda den [Azure PowserShell uppgift i Visual Studio Team Service](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) med nedan PowserShell exempelskriptet att skicka U-SQL jobb direkt från Visual Studio Team Service skapa eller frigöra pipeline.

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

Utöver för att skicka in U-SQL-jobb direkt från Visual Studio Team Service, du kan också ladda upp de inbyggda skript till Azure Data Lake Store/Azure Blob Storage och [köra schemalagda jobb via Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

Använd den [Azure PowerShell-aktivitet i Visual Studio Team Service](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) med nedan PowerShell-exempelskript att ladda upp U-SQL skript för att Azure Data Lake Store-konto.

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

## <a name="cicd-for-u-sql-database"></a>CI/CD för U-SQL-databas

Azure Data Lake Tools för Visual Studio innehåller projektmall för U-SQL-databas som hjälper utvecklare att utveckla, hantera och distribuera U-SQL-databaser snabbt och enkelt. [Läs mer om U-SQL database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Skapa U-SQL database-projekt

### <a name="get-nuget-package"></a>Hämta NuGet-paketet

MSBuild ger inte inbyggt stöd för projekttyp för U-SQL-databas. Om du vill lägga till den här möjligheten, du måste lägga till en referens för din lösning till den [Microsoft.Azure.DataLake.USQL.SDK Nuget-paketet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) som lägger till tjänsten nödvändiga språkpaket.

Lägg till referens för NuGet-paket, kan du högerklicka på lösningen i Solution Explorer och välja **hantera NuGet-paket** du kan sedan söka efter och installera NuGet-paketet. Eller du kan lägga till en fil med namnet ”packages.config” i lösningsmappen och Lägg till nedan innehållet i den.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-database-project-with-msbuild-command-line"></a>Skapa U-SQL database-projekt med MSBuild-kommandorad

Du kan anropa standard MSBuild-kommandorad och pass som referens för U-SQL SDK NuGet-paketet som ytterligare argument som den här att bygga projektet U-SQL-databas:

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Argumenten `USQLSDKPath=<U-SQL Nuget package>\build\runtime` refererar till installationssökvägen för NuGet-paketet för tjänsten U-SQL-språk.

### <a name="continuous-integration-with-visual-studio-team-service"></a>Kontinuerlig integrering med Visual Studio Team Service

Förutom command line kunder kan också använda **Visual Studio-versionen** eller **MSBuild-aktiviteten** att skapa U-SQL-databas-projekt i Visual Studio Team Service. Om du vill konfigurera build uppgiften att se till att:

1.  Lägg till NuGet återställning aktiviteten för att hämta lösningen refererar till NuGet-paketet, inklusive `Azure.DataLake.USQL.SDK`, så att MSBuild hittar mål för U-SQL-språk. Ange **Avancerat > målkatalogen** som `$(Build.SourcesDirectory)/packages` om du vill använda exemplet för MSBuild-argument direkt i steg 2.

    ![Data Lake ange CI CD MSBuild-uppgift för U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

    ![Data Lake ange CI CD Nuget-uppgift för U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Ange MSBuild-argument, och du kan ange argumenten i Visual Studio-versionen eller MSBuild aktivitet, som nedan eller du kan definiera variabler för de här argumenten i VSTS byggesdefinition.

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
    ```

    ![Data Lake ange CI CD MSBuild-variabler för U-SQL database-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

### <a name="u-sql-database-project-build-output"></a>Resultatet för U-SQL-databas-projekt

Skapa utdata för U-SQL database-projekt är ett U-SQL-databas distributionspaket, med namnet med suffixet `.usqldbpack`. Den `.usqldbpack` paketet är en zip-fil som innehåller alla DDL-instruktionerna i ett enda U-SQL-skript i DDL-mappen och alla DLL-filer och ytterligare filer för sammansättningar i Temp-mappen.

## <a name="test-table-valued-function-and-stored-procedure"></a>Testa tabellvärdesfunktion och lagrad procedur

Just nu stöds att direkt lägga till testfall för tabellvärdesfunktioner och lagrade procedurer inte. Du kan skapa ett U-SQL-projekt som har U-SQL-skript som anropar dessa funktioner och skriva testfall för dem som en lösning. Följ stegen nedan för att ställa in testfall för tabellvärdesfunktioner och lagrade procedurer som definierats i U-SQL database-projekt:

1.  Skapa ett U-SQL-projekt för test ändamål och skriver U-SQL-skript som anropar tabellvärdesfunktioner och lagrade procedurer.
2.  Lägg till databas referens till den här U-SQL-projekt. För att få tabellvärdesfunktion och lagrade Procedurdefinition, måste du referera databasprojektet som innehåller DDL-instruktion. [Läs mer om database referens](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Lägg till testfall för U-SQL-skript som anropar tabellvärdesfunktioner och lagrade procedurer. [Lär dig hur du lägger till testfall för U-SQL-skript](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-visual-studio-team-service"></a>Distribuera U-SQL-databas via Visual Studio Team Service

`PackageDeploymentTool.exe` innehåller programmering och kommandoradsverktyget gränssnitt som hjälper dig för att distribuera U-SQL-databas distribution package(.usqldbpack). SDK: N som ingår i [U-SQL SDK NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), hitta på build/runtime/PackageDeploymentTool.exe. Med hjälp av `PackageDeploymentTool.exe`, du kan distribuera U-SQL-databaser till Azure Data Lake Analytics såväl lokalt konto.

>[!NOTE]
>
>PowerShell-kommando linjens support och Visual Studio Team Service viktig uppgift stöd för distribution av U-SQL-databaser är på väg.
>

Följ stegen nedan för att konfigurera distribution av databasåtgärd i Visual Studio Team Service:

1. Lägg till en PowerShell-skript-aktivitet i build eller släpp pipeline och köra PowerShell-skriptet nedan. Den här uppgiften hjälper till att hämta Azure SDK-beroendena för `PackageDeploymentTool.exe` och `PackageDeploymentTool.exe`. Du kan ange parametrarna - AzureSDK och -DBDeploymentTool att läsa in beroenden och distributionsverktyg till vissa specifika mappar. Skicka - AzureSDK sökvägen till `PackageDeploymentTool.exe` som AzureSDKPath - parameter i steg 2. 

    ```powershell
    <#
        This script is used for getting dependencies and SDKs for U-SQL database deployment.
        PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.
        
        Example :
            GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
    #>

    param (
        [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
        [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL dabatase deployment tool
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

2. Lägg till en **kommandoradsaktivitet** i Skapa eller frigöra pipeline och Fyll i skriptet anropar `PackageDeploymentTool.exe`. `PackageDeploymentTool.exe` finns definierade $DBDeploymentTool i mappen. Exempelskriptet är följande: 

    * Distribuera lokal U-SQL-databas

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Använd interaktiva autentiseringsläge för att distribuera U-SQL-databas till Azure Data Lake Analytics-konto:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Använd secrete autentisering för att distribuera U-SQL-databas till Azure Data Lake Analytics-konto:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Använd certifikatfil autentisering för att distribuera U-SQL-databas till Azure Data Lake Analytics-konto:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

**Beskrivning av PackageDeploymentTool.exe parameter:**

**Gemensamma parametrar:**

|Parameter|Beskrivning|Standardvärde|Krävs|
|---------|-----------|-------------|--------|
|Paket|Sökvägen till distributionspaketet för U-SQL-databas som ska distribueras|Null|true|
|Databas|Databasnamn för distribution till / eller skapat|master|false|
|Loggfil|Sökvägen till filen för loggning, standard till standard ut (konsol)|Null|false|
|LogLevel|Certifikatutfärdarnivå: utförlig, Normal, varning, fel|LogLevel.Normal|false|

**Parametern för lokal distribution:**

|Parameter|Beskrivning|Standardvärde|Krävs|
|---------|-----------|-------------|--------|
|DataRoot|Sökvägen till rotmappen för lokala Data|Null|true|

**Parameter för distribution av Azure Data Lake Analytics:**

|Parameter|Beskrivning|Standardvärde|Krävs|
|---------|-----------|-------------|--------|
|Konto|Anger att distribuera till vilken Azure Data Lake Analytics-konto genom att kontonamnet|Null|true|
|ResourceGroup|Azure resursgruppens namn för Azure Data Lake Analytics-konto|Null|true|
|SubscriptionId|Azure-prenumerations-ID för Azure Data Lake Analytics-konto|Null|true|
|Klient|Namn på klientorganisation (AAD directory-domännamn, du kan hitta den på sidan för hantering av prenumeration i Azure portal)|Null|true|
|AzureSDKPath|Sökvägen som ska genomsökas beroende sammansättningar i Azure SDK|Null|true|
|Interaktiv|Använda interaktivt läge för autentisering eller inte|false|false|
|ClientId|AAD-program-ID för Ingen interaktiv autentisering som krävs för att ingen interaktiv autentisering|Null|krävs för Ingen interaktiv autentisering|
|Secrete|Secrete/lösenord för Ingen interaktiv autentisering det ska bara använda i betrodda/säker miljö|Null|krävs för Ingen interaktiv autentisering eller Använd SecreteFile|
|SecreteFile|Filen sparas secrete/lösenord för Ingen interaktiv autentisering, se till att hålla den endast kan läsas av aktuell användare|Null|krävs för Ingen interaktiv autentisering eller Använd Secrete|
|Certifikatfil|Filen sparas X.509-certifiering för Ingen interaktiv autentisering, standardinställningen är att använda klienten secrete autentisering|Null|false|
|JobPrefix|Prefix för distribution av databaser U-SQL DDL-jobb|Deploy_ + DateTime.Now|false|

## <a name="next-steps"></a>Nästa steg

- [Så här testar du din Azure Data Lake Analytics-kod](data-lake-analytics-cicd-test.md)
- [Kör U-SQL-skript på den lokala datorn](data-lake-analytics-data-lake-tools-local-run.md)
- [Använd U-SQL database-projekt för att utveckla U-SQL-databas](data-lake-analytics-data-lake-tools-develop-usql-database.md)
