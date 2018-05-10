---
title: Hantera resurser och relaterade entiteter med Media Services .NET SDK
description: Lär dig hur du hanterar tillgångar och relaterade entiteter med Media Services SDK för .NET.
author: juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: juliako
ms.openlocfilehash: af5baf3444196e5a0e8412d9ab4f019fdccb033e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Hantera resurser och relaterade entiteter med Media Services .NET SDK
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

Det här avsnittet visar hur du hanterar Azure Media Services entiteter med .NET. 

>[!NOTE]
> Från och med 1 april 2017 raderas alla jobbposter i ditt konto som är äldre än 90 dagar automatiskt, tillsammans med deras associerade uppgiftsposter, även om det totala antalet poster är lägre än den maximala kvoten. Till exempel på 1 April 2017 tas alla jobb poster i ditt konto som är äldre än den 31 December 2016 automatiskt bort. Du kan använda koden som beskrivs i det här avsnittet om du behöver Arkivera jobb/aktivitetsinformationen.

## <a name="prerequisites"></a>Förutsättningar

Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation, enligt beskrivningen i [Media Services-utveckling med .NET](media-services-dotnet-how-to-use.md). 

## <a name="get-an-asset-reference"></a>Hämta en referens till en tillgång
En uppgift som ofta är att hämta en referens till en befintlig tillgång i Media Services. Följande kodexempel visar hur du kan hämta en referens för tillgångsinformation från samlingen tillgångar på servern context-objektet, baserat på en tillgång Id. Följande kodexempel använder en Linq-fråga för att hämta en referens till ett befintligt IAsset-objekt.

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

## <a name="list-all-assets"></a>Visa en lista med alla tillgångar
När antalet tillgångar som du har i lagring växer, är det bra att visa dina tillgångar. Följande kodexempel visar hur du söker igenom tillgångar samling på servern kontextobjektet. Med varje tillgång skriver kodexemplet aktuella även några av dess egenskapsvärden i konsolen. Varje tillgång kan exempelvis innehålla många mediefiler. Exemplet skriver ut alla filer som är associerade med varje tillgång.

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

## <a name="get-a-job-reference"></a>Hämta en referens i projektet

När du arbetar med att bearbeta uppgifter i Media Services kod måste ofta du hämta en referens till ett befintligt jobb baserat på ett Id. Följande kodexempel visar hur du hämtar en referens till ett IJob objekt från samlingen jobb.

Du kan behöva hämta en referens för jobbet när du startar en tidskrävande kodningsjobbet och behöver kontrollera Jobbstatus i en tråd. När metoden returnerar från en tråd i detta fall måste du hämta en uppdateras referens till ett jobb.

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

## <a name="list-jobs-and-assets"></a>Lista över jobb och tillgångar
En viktig uppgift är att lista över tillgångar med deras associerade jobbet i Media Services. Följande kodexempel visar hur du listar alla IJob-objekt, och sedan för varje projekt, den visar egenskaper om jobbet, alla relaterade uppgifter, alla indata-tillgångar och alla utdata tillgångar. Koden i det här exemplet kan vara användbart för flera uppgifter. Om du vill visa en lista med tillgångar utdata från ett eller flera kodning jobb som du tidigare körde visar denna kod hur du kommer åt utdata tillgångar. När du har en referens till en utdatatillgången, kan du sedan leverera innehållet till andra användare eller program genom att hämta den eller tillhandahålla URL: er. 

Mer information om alternativ för att leverera tillgångar finns [leverera tillgångar med Media Services SDK för .NET](media-services-deliver-streaming-content.md).

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

## <a name="list-all-access-policies"></a>Visa en lista med alla åtkomstprinciper
I Media Services kan du definiera en åtkomstprincip för en tillgång eller dess filer. En åtkomstprincip definierar behörigheter för en fil eller en tillgång (vilken typ av åtkomst och varaktighet). I Media Services-koden definiera du vanligtvis en åtkomstprincip genom att skapa ett IAccessPolicy objekt och associera den med en befintlig tillgång. Sedan kan du skapa en ILocator-objektet, vilket gör att du kan ge direktåtkomst till tillgångar i Media Services. Visual Studio-projekt som medföljer den här dokumentationen serien innehåller flera kodexempel som visar hur du skapar och tilldelar tillgångar åtkomstprinciper och lokaliserare.

Följande kodexempel visar hur du listar alla åtkomstprinciper på servern och visar vilken typ av behörigheter som associeras med varje. Ett annat bra sätt att visa principer för åtkomst är att lista alla ILocator objekt på servern och sedan för varje lokaliserare, kan du visa dess associerade åtkomstprincip via egenskapen AccessPolicy.

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
    
## <a name="limit-access-policies"></a>Gränsen åtkomstprinciper 

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

Du kan sedan använda de befintliga ID i koden så här:

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

## <a name="list-all-locators"></a>Visa en lista med alla positionerare
En positionerare är en URL som innehåller en direkt sökväg för att komma åt en tillgång, tillsammans med behörigheter till tillgången som definieras av den positionerare associerade åtkomstprincip. Varje tillgång kan ha en samling ILocator objekt som är associerade med den på egenskapen lokaliserare. Serverkontext har också en positionerare samling som innehåller alla lokaliserare.

Följande kodexempel visar alla positionerare på servern. För varje lokaliserare visas Id för den relaterade tillgången och åtkomst. Den visar även typ av behörigheter, förfallodatum och den fullständiga sökvägen till tillgången.

Observera att en positionerare sökväg till en tillgång är en grundläggande Webbadress till tillgången. Om du vill skapa en direkt sökväg till enskilda filer som en användare eller ett program kan bläddra till din kod måste lägga till specifika filsökvägen lokaliserare sökvägen. Mer information om hur du gör detta finns i avsnittet [leverera tillgångar med Media Services SDK för .NET](media-services-deliver-streaming-content.md).

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
När du frågar entiteter, finns det en gräns på 1000 entiteter som returneras i taget eftersom offentlig REST-v2 begränsar frågeresultaten till 1000 resultat. Du måste använda Skip och vidta vid uppräkning av stora mängder av entiteter. 

Följande funktion loop genom alla jobb i den angivna Media Services-konto. Media Services returnerar 1000 jobb i Jobbsamlingen. Funktionen tillämpar Skip och vidta för att se till att alla jobb räknas (om du har fler än 1000 jobb i ditt konto).

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
Om du vill ta bort ett jobb, måste du kontrollera status för jobbet som anges i egenskapen State. Jobb som har slutförts eller avbrutits kan tas bort medan jobb som har vissa andra tillstånd, till exempel köade schemalagda eller bearbetning, måste först avbrytas och sedan kan du ta bort.

Följande exempel visar en metod för att ta bort ett jobb genom att kontrollera status för jobb och ta sedan bort när tillståndet slutförts eller avbrutits. Den här koden är beroende av föregående avsnitt i det här avsnittet för att hämta en referens till ett jobb: hämta en referens i projektet.

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
Följande kodexempel visar hur du hämtar en referens till en princip utifrån en princip-Id, och sedan ta bort principen.

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

