---
title: Hantera tillgångar och relaterade entiteter med Media Services .NET SDK
description: Lär dig hur du hanterar tillgångar och relaterade entiteter med Media Services SDK för .NET.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: a686465b0006c2e9aac6e06cb4ab12d30921e8c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61235433"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Hantera tillgångar och relaterade entiteter med Media Services .NET SDK
> [!div class="op_single_selector"]
> * [NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Upptäck den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledningen från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Det här avsnittet visar hur du hanterar Azure Media Services-enheter med .NET.

Från och med 1 april 2017 raderas alla jobbposter i ditt konto som är äldre än 90 dagar automatiskt, tillsammans med deras associerade uppgiftsposter, även om det totala antalet poster är lägre än den maximala kvoten. Exempelvis den 1 April 2017 tas alla jobbposter i ditt konto som är äldre än den 31 December 2016 automatiskt bort. Om du behöver Arkivera jobb/uppgiftsinformationen kan du använda koden som beskrivs i det här avsnittet.

## <a name="prerequisites"></a>Nödvändiga komponenter

Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation, enligt beskrivningen i [Media Services-utveckling med .NET](media-services-dotnet-how-to-use.md). 

## <a name="get-an-asset-reference"></a>Hämta en referens för tillgången
En återkommande uppgift är att hämta en referens till en befintlig tillgång i Media Services. I följande kodexempel visas hur du skaffar en tillgång referens från mängden tillgångar på servern context-objektet, baserat på en tillgång Id. I följande kodexempel används en Linq-frågan för att hämta en referens till ett befintligt IAsset-objekt.

```csharp
    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();

        return asset;
    }
```

## <a name="list-all-assets"></a>Lista över alla tillgångar
När antalet tillgångar som du har i storage växer, är det bra att lista dina tillgångar. I följande kodexempel visar hur du gå igenom samlingen tillgångar på server context-objektet. Med varje tillgången skriver kodexemplet aktuella även några av dess egenskapsvärden till konsolen. Varje tillgång kan exempelvis innehålla många mediefiler. Kodexemplet skriver ut alla filer som är associerade med varje tillgång.

```csharp
    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");

            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="get-a-job-reference"></a>Hämta en referens för jobbet

När du arbetar med bearbetning av uppgifter i Media Services-koden kan behöver du ofta hämta en referens till ett befintligt jobb baserat på ett Id. I följande kodexempel visar hur du hämtar en referens till en IJob-objektet från mängden jobb.

Du kan behöva hämta en referens för jobbet när du startar en tidskrävande kodningsjobb och behöva kontrollera Jobbstatus i en tråd. När metoden returnerar från en tråd i detta fall måste du hämta en uppdaterad referens till ett jobb.

```csharp
    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();

        return job;
    }
```

## <a name="list-jobs-and-assets"></a>Lista jobb och tillgångar
En viktig relaterade uppgift är att lista resurser med deras associerade jobbet i Media Services. I följande kodexempel visar hur att lista alla IJob-objekt, och sedan för varje jobb visas egenskaper om jobbet, alla relaterade uppgifter, alla indata-tillgångar och alla resurser som utdata. Koden i det här exemplet kan vara användbart för ett flertal andra aktiviteter. Om du vill visa en lista över utdataresultat från en eller flera kodningsjobb som du körde tidigare visar den här kod hur du kommer åt utdata-tillgångar. När du har en referens till en utdatatillgången, kan du sedan levererar innehåll till andra användare eller program genom att hämta den eller tillhandahålla URL: er. 

Läs mer om alternativen för att leverera tillgångar [leverera tillgångar med Media Services SDK för .NET](media-services-deliver-streaming-content.md).

```csharp
    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");


            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }

            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {

                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }

            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }

        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="list-all-access-policies"></a>Lista över alla åtkomstprinciper
I Media Services kan du definiera en åtkomstprincip för en tillgång eller dess filer. En åtkomstprincip definierar behörigheter för en fil eller en tillgång (vilken typ av åtkomst och varaktighet). I Media Services-koden definiera du vanligtvis en åtkomstprincip genom att skapa ett IAccessPolicy-objekt och koppla det till en befintlig tillgång. Sedan kan du skapa ett ILocator-objekt, som låter dig ge direktåtkomst till tillgångar i Media Services. Visual Studio-projekt som medföljer den här dokumentationen-serien innehåller flera kodexempel som visar hur du skapar och tilldelar åtkomstprinciper och positionerare till tillgångar.

I följande kodexempel visar hur du listar alla åtkomstprinciper på servern och visar vilken typ av behörigheter som hör till var. En annan bra sätt att visa åtkomstprinciper är att lista alla ILocator objekt på servern och sedan för varje positionerare du visa en lista med dess associerade åtkomstprincip via egenskapen AccessPolicy.

```csharp
    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");

        }
    }
```
    
## <a name="limit-access-policies"></a>Policyer för åtkomst 

>[!NOTE]
> Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). 

Du kan till exempel skapa en allmän uppsättning principer med följande kod som körs endast en gång i ditt program. Du kan logga ID: N till en loggfil för senare användning:

```csharp
    double year = 365.25;
    double week = 7;
    IAccessPolicy policyYear = _context.AccessPolicies.Create("One Year", TimeSpan.FromDays(year), AccessPermissions.Read);
    IAccessPolicy policy100Year = _context.AccessPolicies.Create("Hundred Years", TimeSpan.FromDays(year * 100), AccessPermissions.Read);
    IAccessPolicy policyWeek = _context.AccessPolicies.Create("One Week", TimeSpan.FromDays(week), AccessPermissions.Read);

    Console.WriteLine("One year policy ID is: " + policyYear.Id);
    Console.WriteLine("100 year policy ID is: " + policy100Year.Id);
    Console.WriteLine("One week policy ID is: " + policyWeek.Id);
```

Du kan sedan använda de befintliga ID: N i din kod så här:

```csharp
    const string policy1YearId = "nb:pid:UUID:2a4f0104-51a9-4078-ae26-c730f88d35cf";


    // Get the standard policy for 1 year read only
    var tempPolicyId = from b in _context.AccessPolicies
                       where b.Id == policy1YearId
                       select b;
    IAccessPolicy policy1Year = tempPolicyId.FirstOrDefault();

    // Get the existing asset
    var tempAsset = from a in _context.Assets
                where a.Id == assetID
                select a;
    IAsset asset = tempAsset.SingleOrDefault();

    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
        policy1Year,
        DateTime.UtcNow.AddMinutes(-5));
    Console.WriteLine("The locator base path is " + originLocator.BaseUri.ToString());
```

## <a name="list-all-locators"></a>Lista över alla positionerare
En positionerare är en URL som innehåller en direkt sökväg för att få åtkomst till en tillgång, tillsammans med behörigheter för att tillgången som definieras av positionerarprincipen associerade åtkomst. Varje tillgång kan ha en samling ILocator objekt som är associerade med den på dess positionerare-egenskap. Server-kontexten har också en positionerare-samling som innehåller alla positionerare.

I följande kodexempel visar en lista över alla positionerare på servern. För varje positionerare visas Id för principen för relaterade tillgångar och åtkomst. Den visar även typ av behörigheter, datumet och den fullständiga sökvägen till tillgången.

Observera att en positionerare sökväg till en tillgång är en grundläggande Webbadress till tillgången. Om du vill skapa en direkt sökväg till enskilda filer som en användare eller ett program kan bläddra till din kod måste lägga till sökvägen till specifika positionerare sökvägen. Mer information om hur du gör detta finns i avsnittet [leverera tillgångar med Media Services SDK för .NET](media-services-deliver-streaming-content.md).

```csharp
    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }
```

## <a name="enumerating-through-large-collections-of-entities"></a>Uppräkning av stora mängder av entiteter
Vid frågor till entiteter, finns det en gräns på 1000 enheter som returneras i taget eftersom offentlig REST-v2 begränsar frågeresultaten till 1000 resultat. Du måste använda hoppa över och vidta vid uppräkning av stora mängder av entiteter. 

Följande funktion igenom alla jobb i den angivna Media Services-konto. Media Services returnerar 1000 jobb i Jobbsamlingen. Funktionen gör att användning av hoppa över och gör att se till att alla jobb räknas (om du har fler än 1000 jobb i ditt konto).

```csharp
    static void ProcessJobs()
    {
        try
        {

            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;

            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }

                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
```

## <a name="delete-an-asset"></a>Ta bort en tillgång
I följande exempel tar bort en tillgång.

```csharp
    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();

        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");

    }
```

## <a name="delete-a-job"></a>Ta bort ett jobb
Om du vill ta bort ett jobb, måste du kontrollera status för jobbet som anges i egenskapen State. Jobb som har slutförts eller avbrutits kan tas bort när jobb som har vissa andra tillstånd, som i kö, schemalagda eller bearbetningsfunktioner, måste först avbrytas, och sedan kan du ta bort.

I följande kodexempel visar en metod för att ta bort ett jobb genom att markera status för jobb och tar sedan bort när tillståndet är klar eller har avbrutits. Den här koden är beroende av föregående avsnitt i det här avsnittet för att hämta en referens till ett jobb: Hämta en referens för jobbet.

```csharp
    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;

        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);

            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }

        }
    }
```


## <a name="delete-an-access-policy"></a>Ta bort en åtkomstprincip
I följande kodexempel visar hur du hämtar en referens till en princip utifrån en princip-Id, och för att ta bort principen.

```csharp
    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();

        policy.Delete();

    }
```


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

