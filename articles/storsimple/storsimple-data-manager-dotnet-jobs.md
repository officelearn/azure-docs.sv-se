---
title: Använd .NET SDK för Microsoft Azure StorSimple Data Manager-jobb | Microsoft Docs
description: Lär dig hur du använder .NET SDK för att starta StorSimple Data Manager-jobb
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 80f01a926b94deebab59f8ef91bfc36a4600b5f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632406"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Använda .NET SDK för att initiera data-transformering

## <a name="overview"></a>Översikt

Den här artikeln förklarar hur du kan använda funktionen transformering i StorSimple Data Manager-tjänsten för att omvandla data för StorSimple-enheten. Transformerade data används sedan av andra Azure-tjänster i molnet.

Du kan starta ett jobb för omvandling av data på två sätt:

- Använda .NET SDK
- Använda Azure Automation-runbook
 
  Den här artikeln beskriver hur du skapar ett .NET konsolexempelprogram att initiera ett omvandlingsjobb och spåra det för slutförande. Om du vill veta mer om hur du startar Dataomvandling via Automation kan du gå till [Använd Azure Automation-runbook att utlösaren datatransformeringsjobb](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar måste du kontrollera att du har:
*   En dator som kör:

    - Visual Studio 2012, 2013, 2015 eller 2017.

    - Azure Powershell. [Hämta Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   En korrekt konfigurerad jobbdefinition i StorSimple Data Manager inom en resursgrupp.
*   Alla nödvändiga DLL-filer. Ladda ned dessa DLL-filer från den [GitHub-lagringsplatsen](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) skriptet från GitHub-lagringsplatsen.

## <a name="step-by-step-procedure"></a>Stegvisa anvisningar

Utför följande steg om du vill använda .NET för att starta ett omvandlingsjobb.

1. Om du vill hämta konfigurationsparametrarna, gör du följande:
    1. Ladda ned den `Get-ConfigurationParams.ps1` från GitHub-lagringsplatsen skriptet i `C:\DataTransformation` plats.
    1. Kör den `Get-ConfigurationParams.ps1` skriptet från GitHub-lagringsplatsen. Ange följande kommando:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Du kan skicka alla värden för ActiveDirectoryKey och AppName.

2. Det här skriptet returnerar följande värden:
    * Klientorganisations-ID
    * Klient-ID:t
    * Active Directory-nyckel (detsamma som det som anges ovan)
    * Prenumerations-ID:t

        ![Konfigurationsparametrar skript utdata](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Med hjälp av Visual Studio 2012, 2013 eller 2015, skapar en C# .NET-konsolprogram.

    1. Starta **Visual Studio 2012/2013/2015**.
    1. Välj **Arkiv > Nytt > Projekt**.

        ![Skapa ett projekt 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Välj **installerat > Mallar > Visual C# > Konsolapp**.
    3. Ange **DataTransformationApp** för den **namn**.
    4. Välj **C:\DataTransformation** för den **plats**.
    6. Klicka på **OK** för att skapa projektet.

        ![Skapa ett projekt 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Lägg nu till alla DLL: er finns i den [DLL-filer mappen](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) som **referenser** i projektet som du skapade. Om du vill lägga till dll-filer, utför du följande:

   1. I Visual Studio går du till **Visa > Solution Explorer**.
   2. Klicka på pilen till vänster om Data Transformation App-projekt. Klicka på **referenser** och högerklicka sedan på att **Lägg till referens**.
    
       ![Lägg till DLL-filer 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Bläddra till platsen för mappen packages, Välj alla DLL: er och klicka på **Lägg till**, och klicka sedan på **OK**.

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
    
6. Följande kod initierar jobbinstansen data omvandling. Lägg till detta i den **Main-metoden**. Ersätt värdena för konfigurationsparametrar som du fick tidigare. Plugin-värdena för **Resursgruppsnamn** och **ResourceName**. Den **ResourceGroupName** är associerad med där jobbdefinitionen konfigurerades StorSimple Data Manager. Den **ResourceName** är namnet på StorSimple Data Manager-tjänsten.

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
   
7. Ange de parametrar som jobbdefinitionen måste köras

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    (OR)

    Om du vill ändra definitionen jobbparametrar under körning och Lägg sedan till följande kod:

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

8. Efter initieringen, lägger du till följande kod för att utlösa ett omvandlingsjobb på jobbdefinitionen. Plugin-lämplig **Jobbdefinitionsnamn**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    När koden ska klistras in, skapa lösningen. Här är en skärmbild av kodfragmentet att initiera data-transformering jobbinstans.

   ![Kodfragment för att initiera omvandlingsjobb](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Det här jobbet omvandlar data som matchar rotkatalogen och filen filtrerar i StorSimple-volym och placerar dem i den angivna behållare /-filresursen. När en fil omvandlas, läggs ett meddelande till en lagringskö (i samma lagringskonto som behållare/filresurs) med samma namn som jobbdefinitionen. Det här meddelandet kan användas som en utlösare för att initiera vidare bearbetning av filen.

10. När jobbet har aktiverats, kan du använda följande kod för att spåra jobb för slutförande. Det är inte obligatoriskt att lägga till den här koden för jobbet kör.

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
    Här är en skärmbild av hela kodexemplet används för att utlösa jobbet med hjälp av .NET.

    ![Fullständig kodstycke att utlösa ett jobb för .NET](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager UI och omvandla data](storsimple-data-manager-ui.md).
