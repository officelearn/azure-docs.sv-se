---
title: Använda .NET SDK för Microsoft Azure StorSimple Data Manager-jobb
description: Lär dig hur du använder .NET SDK för att starta StorSimple Data Manager-jobb
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b7cf1d3b9d4a9d751348c4792f904062b00ac104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76270731"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Använd .NET SDK för att initiera dataomvandling

## <a name="overview"></a>Översikt

I den här artikeln beskrivs hur du kan använda dataomvandlingsfunktionen i StorSimple Data Manager-tjänsten för att omvandla StorSimple-enhetsdata. Transformerade data förbrukas sedan av andra Azure-tjänster i molnet.

Du kan starta ett dataomvandlingsjobb på två sätt:

- Använda .NET SDK
- Använda Azure Automation-runbook
 
  I den här artikeln beskrivs hur du skapar ett exempel på .NET-konsolprogram för att initiera ett dataomvandlingsjobb och sedan spåra det för slutförande. Mer information om hur du initierar dataomvandling via Automatisering finns i [Använda Azure Automation-runbook för att utlösa dataomvandlingsjobb](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Krav

Innan du börjar, se till att du har:
*   En dator som kör:

    - Visual Studio 2012, 2013, 2015 eller 2017.

    - Azure Powershell. [Ladda ned Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   En korrekt konfigurerad jobbdefinition i StorSimple Data Manager inom en resursgrupp.
*   Alla nödvändiga dlls. Hämta dessa dlls från [GitHub-databasen](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1)skriptet från GitHub-databasen.

## <a name="step-by-step-procedure"></a>Steg-för-steg-procedur

Utför följande steg för att använda .NET för att starta ett dataomvandlingsjobb.

1. Så här hämtar du konfigurationsparametrarna:
    1. Hämta `Get-ConfigurationParams.ps1` från GitHub-databasskriptet `C:\DataTransformation` på plats.
    1. Kör `Get-ConfigurationParams.ps1` skriptet från GitHub-databasen. Ange följande kommando:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Du kan skicka in alla värden för ActiveDirectoryKey och AppName.

2. Det här skriptet matar ut följande värden:
    * Klientorganisations-ID
    * Klient-ID:t
    * Active Directory-tangenten (samma som den som anges ovan)
    * Prenumerations-ID:t

        ![Skriptutdata för konfigurationsparametrar](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Skapa ett C# .NET-konsolprogram med Visual Studio 2012, 2013 eller 2015.

    1. Starta **Visual Studio 2012/2013/2015**.
    1. Välj **Arkiv > Nytt > Projekt**.

        ![Skapa ett projekt 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Välj **Installerade > mallar > Visual C# > Console Application**.
    3. Ange **DataTransformationApp** för **namnet**.
    4. Välj **C:\DataTransformation** för **platsen**.
    6. Klicka på **OK** för att skapa projektet.

        ![Skapa ett projekt 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Lägg nu till alla dll-filer som finns i [dlls-mappen](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) som **referenser** i projektet som du skapade. Så här lägger du till dll-filerna:

   1. I Visual Studio går du till **Visa > Solution Explorer**.
   2. Klicka på pilen till vänster om Data Transformation App-projektet. Klicka på **Referenser** och högerklicka för att **lägga till referens**.
    
       ![Lägg till dlls 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Bläddra till paketmappens plats, markera alla dlls och klicka på **Lägg till**och klicka sedan på **OK**.

       ![Lägg till dlls 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Lägg till följande **genom att använda** instruktioner till källfilen (Program.cs) i projektet.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. Följande kod initierar jobbinstansen för dataomvandling. Lägg till detta i **huvudmetoden**. Ersätt värdena för konfigurationsparametrar som erhållits tidigare. Koppla in värdena **för resursgruppnamn** och **Resursnamn**. **ResourceGroupName** är associerad med StorSimple Data Manager som jobbdefinitionen konfigurerades på. **ResourceName** är namnet på tjänsten StorSimple Data Manager.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);
    ```
   
7. Ange de parametrar som jobbdefinitionen måste köras med

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    Jag vet inte vad du ska ta på dig.

    Om du vill ändra parametrarna för jobbdefinition under körning lägger du till följande kod:

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    ```

8. Efter initieringen lägger du till följande kod för att utlösa ett dataomvandlingsjobb på jobbdefinitionen. Koppla in lämpligt **jobbdefinitionsnamn**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    När koden har klistrats in, bygga lösningen. Här är en skärmbild av kodavsnittet för att initiera jobbinstansen för dataomvandling.

   ![Kodavsnitt för att initiera dataomvandlingsjobb](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Det här jobbet omvandlar data som matchar rotkatalogen och filfiltren i StorSimple-volymen och placerar dem i den angivna behållar-/filresursen. När en fil transformeras läggs ett meddelande till i en lagringskö (i samma lagringskonto som behållar-/filresursen) med samma namn som jobbdefinitionen. Det här meddelandet kan användas som utlösare för att initiera ytterligare bearbetning av filen.

10. När jobbet har utlösts kan du använda följande kod för att spåra jobbet för slutförande. Det är inte obligatoriskt att lägga till den här koden för jobbkörningen.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```
    Här är en skärmdump av hela kodexemplet som används för att utlösa jobbet med .NET.

    ![Fullständigt kodavsnitt för att utlösa ett .NET-jobb](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager-användargränssnittet för att omvandla dina data](storsimple-data-manager-ui.md).
