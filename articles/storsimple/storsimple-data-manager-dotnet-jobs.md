---
title: "Använd SDK för .NET för Microsoft Azure StorSimple Data Manager-jobb | Microsoft Docs"
description: "Lär dig hur du använder .NET SDK för att starta StorSimple Data Manager-jobb (privat förhandsvisning)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: 44d243a034b20b99faf284c8615e470bc6f9d020
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-net-sdk-to-initiate-data-transformation-private-preview"></a>Använd .net SDK för att initiera DTS (privat förhandsvisning)

## <a name="overview"></a>Översikt

Den här artikeln förklarar hur du kan använda funktionen omvandling i StorSimple Data Manager-tjänsten för att omvandla data för StorSimple-enhet. Omvandlade data används av andra Azure-tjänster i molnet. Artikeln har även en genomgång för att skapa ett .NET-konsolen exempelprogram att initiera ett jobb för omvandling av data och följa den för slutförande.

## <a name="prerequisites"></a>Krav

Innan du börjar bör du kontrollera att du har:
*   Ett system med Visual Studio 2012, 2013 eller 2015 installerat 2017.
*   Azure Powershell har installerats. [Hämta Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Konfigurationsinställningarna för att initiera Data Transformation jobbet (instruktioner för att hämta inställningarna ingår).
*   En jobbdefinition av som har konfigurerats på rätt sätt i en Hybrid Dataresurs i en resursgrupp.
*   Alla nödvändiga DLL-filer. Ladda ned dessa DLL-filer från de [GitHub-lagringsplatsen](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   `Get-ConfigurationParams.ps1`[skriptet](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) från github-lagringsplatsen.

## <a name="step-by-step"></a>Steg för steg

Utför följande steg om du vill starta ett jobb för omvandling av data med hjälp av .NET.

1. Om du vill hämta konfigurationsparametrarna, gör du följande steg:
    1. Hämta den `Get-ConfigurationParams.ps1` från github-lagringsplatsen skriptet i `C:\DataTransformation` plats.
    1. Kör den `Get-ConfigurationParams.ps1` skriptet från github-lagringsplatsen. Ange följande kommando:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Du kan skicka in värden för ActiveDirectoryKey och AppName.


2. Det här skriptet matar ut följande värden:
    * Klient-ID
    * Klient-ID:t
    * Active Directory-nyckel (samma som den som anges ovan)
    * Prenumerations-ID:t

3. Med Visual Studio 2012, 2013 eller 2015, skapa ett C# .NET-konsolprogram.

    1. Starta **Visual Studio 2012/2013/2015**.
    1. Klicka på **Arkiv**, peka på **Nytt** och klicka på **Projekt**.
    2. Expandera **Mallar** och välj **Visual C#**.
    3. Välj **Konsolprogram** i listan över projekttyper till höger.
    4. Ange **DataTransformationApp** för den **namn**.
    5. Välj **C:\DataTransformation** för den **plats**.
    6. Klicka på **OK** för att skapa projektet.

4.  Lägg till alla DLL: er finns i den [DLL: er](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) mapp som **referenser** i projektet som du skapade. Om du vill hämta dll-filer, gör du följande:

    1. I Visual Studio, gå till **Visa > Solution Explorer**.
    1. Klicka på pilen till vänster om Data Transformation App-projekt. Klicka på **referenser** och högerklicka sedan på att **Lägg till referens**.
    2. Bläddra till platsen för mappen packages, markera alla DLL: er och på **Lägg till**, och klicka sedan på **OK**.

5. Lägg till följande **genom att använda** instruktioner till källfilen (Program.cs) i projektet.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```


6. Följande kod initierar data transformation jobbinstans. Lägg till detta i den **Main-metoden**. Ersätt värdena för konfigurationsparametrar som hämtats tidigare. Plugin-värdena för **resursgruppnamn** och **Hybrid Data resursnamnet**. Den **resursgruppens namn** är den som är värd för Hybrid Dataresurs som jobbdefinitionen konfigurerades.

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

    (ELLER)

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

    ```

9. Det här jobbet överför matchade filerna finns under rotkatalogen på StorSimple-volym till den angivna behållaren. När en fil har överförts visas har ett meddelande släppts i kön (i samma lagringskonto som behållare) med samma namn som jobbdefinitionen. Det här meddelandet kan användas som en utlösare för att initiera vidare bearbetning av filen.

10. Lägg till följande kod för att spåra jobbet för slutförande när jobbet har utlösts.

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


## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager UI att omvandla data](storsimple-data-manager-ui.md).
