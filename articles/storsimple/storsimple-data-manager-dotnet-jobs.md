---
title: "Använd SDK för .NET för Microsoft Azure StorSimple Data Manager-jobb | Microsoft Docs"
description: "Lär dig hur du använder .NET SDK för att starta StorSimple Data Manager-jobb"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: d15a5cbda2f0c2a363b40e94c38fed6631aa81b5
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Använd .net SDK för att initiera DTS

## <a name="overview"></a>Översikt

Den här artikeln förklarar hur du kan använda funktionen omvandling i StorSimple Data Manager-tjänsten för att omvandla data för StorSimple-enhet. Omvandlade data används av andra Azure-tjänster i molnet.

Du kan starta ett jobb för omvandling av data på två sätt:

 - Använda .NET SDK
 - Använd Azure Automation-runbook
 
 Den här artikeln beskrivs hur du skapar ett .NET-konsolen exempelprogram att initiera ett jobb för omvandling av data och följa den för slutförande. Om du vill veta mer om hur du initierar Dataomvandling av via Automation kan du gå till [Använd Azure Automation-runbook till utlösaren data transformation jobb](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du kontrollera att du har:
*   En dator som kör:

    - Visual Studio 2012, 2013 eller 2015 2017.

    - Azure Powershell. [Hämta Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   En korrekt konfigurerad jobbdefinitionen i StorSimple Data Manager inom en resursgrupp.
*   Alla nödvändiga DLL-filer. Ladda ned dessa DLL-filer från de [GitHub-lagringsplatsen](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1)skriptet från GitHub-lagringsplatsen.

## <a name="step-by-step-procedure"></a>Stegvisa anvisningar

Utför följande steg om du vill starta ett jobb för omvandling av data med hjälp av .NET.

1. Om du vill hämta konfigurationsparametrarna, gör du följande steg:
    1. Hämta den `Get-ConfigurationParams.ps1` från GitHub-lagringsplatsen skriptet i `C:\DataTransformation` plats.
    1. Kör den `Get-ConfigurationParams.ps1` skriptet från GitHub-lagringsplatsen. Ange följande kommando:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Du kan skicka in värden för ActiveDirectoryKey och AppName.

2. Det här skriptet matar ut följande värden:
    * Klient-ID
    * Klient-ID:t
    * Active Directory-nyckel (samma som den som anges ovan)
    * Prenumerations-ID:t

        ![Konfigurationsparametrar skript utdata](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Med Visual Studio 2012, 2013 eller 2015, skapa ett C# .NET-konsolprogram.

    1. Starta **Visual Studio 2012/2013/2015**.
    1. Välj **Arkiv > Nytt > projekt**.

        ![Skapa ett projekt 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Välj **installerat > Mallar > Visual C# > konsolprogrammet**.
    3. Ange **DataTransformationApp** för den **namn**.
    4. Välj **C:\DataTransformation** för den **plats**.
    6. Klicka på **OK** för att skapa projektet.

        ![Skapa ett projekt 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4.  Lägg till alla DLL: er finns i den [DLL-filer mappen](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) som **referenser** i projektet som du skapade. Om du vill lägga till dll-filer, utför du följande:

    1. I Visual Studio, gå till **Visa > Solution Explorer**.
    2. Klicka på pilen till vänster om Data Transformation App-projekt. Klicka på **referenser** och högerklicka sedan på att **Lägg till referens**.
    
        ![Lägg till DLL-filer 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

    3. Bläddra till platsen för mappen packages, markera alla DLL: er och på **Lägg till**, och klicka sedan på **OK**.

        ![Lägg till DLL-filer 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Lägg till följande **genom att använda** instruktioner till källfilen (Program.cs) i projektet.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. Följande kod initierar data transformation jobbinstans. Lägg till detta i den **Main-metoden**. Ersätt värdena för konfigurationsparametrar som hämtats tidigare. Plugin-värdena för **resursgruppnamn** och **ResourceName**. Den **ResourceGroupName** är associerad med StorSimple Datahanteraren som jobbdefinitionen konfigurerades. Den **ResourceName** är namnet på din StorSimple Data Manager-tjänsten.

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
   
7. Ange parametrar som jobbdefinitionen måste köras

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    (OR)

    Om du vill ändra definition jobbparametrar under körning och Lägg sedan till följande kod:

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

8. Lägg till följande kod för att utlösa ett jobb för omvandling av data på jobbdefinitionen efter initieringen av. Anslut i rätt **Definition jobbnamn**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    När koden klistras skapa lösningen. Här är en skärmbild av kodfragmentet att initiera data-transformation jobbinstans.

   ![Kodfragmentet att initiera data transformation jobb](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Det här jobbet omvandlar de data som matchar rotkatalogen och filen filtrerar i StorSimple-volym och placerar dem i den angivna behållare/filresursen. När en fil omvandlas till ett meddelande till en kö med lagring (i samma lagringskonto som behållare/filresursen) med samma namn som jobbdefinitionen. Det här meddelandet kan användas som en utlösare för att initiera vidare bearbetning av filen.

10. Du kan använda följande kod för att spåra jobbet för slutförande när jobbet har utlösts. Det är inte obligatoriskt att lägga till den här koden för jobbet kör.

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
 Här är en skärmbild av hela kodexemplet används för att utlösa jobb med hjälp av .NET.

 ![Fullständig kodavsnittet att utlösa ett jobb för .NET](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager UI att omvandla data](storsimple-data-manager-ui.md).
