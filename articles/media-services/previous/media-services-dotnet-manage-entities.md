---
title: Hantera tillgångar och relaterade enheter med Media Services .NET SDK
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251146"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Hantera tillgångar och relaterade enheter med Media Services .NET SDK
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [Resten](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Det här avsnittet visar hur du hanterar Azure Media Services-entiteter med .NET.

Från och med 1 april 2017 raderas alla jobbposter i ditt konto som är äldre än 90 dagar automatiskt, tillsammans med deras associerade uppgiftsposter, även om det totala antalet poster är lägre än den maximala kvoten. Den 1 april 2017 tas till exempel alla jobbpost i ditt konto som är äldre än den 31 december 2016 bort automatiskt. Om du behöver arkivera jobb-/uppgiftsinformationen kan du använda koden som beskrivs i det här avsnittet.

## <a name="prerequisites"></a>Krav

Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation enligt beskrivningen i [Media Services-utvecklingen med .NET](media-services-dotnet-how-to-use.md). 

## <a name="get-an-asset-reference"></a>Hämta en tillgångsreferens
En frekvent uppgift är att få en referens till en befintlig tillgång i Media Services. I följande kodexempel visas hur du kan hämta en tillgångsreferens från resurssamlingen på serverkontextobjektet, baserat på ett tillgångs-ID. I följande kodexempel används en Linq-fråga för att hämta en referens till ett befintligt IAsset-objekt.

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

## <a name="list-all-assets"></a>Lista alla tillgångar
När antalet tillgångar som du har i lagring ökar är det bra att lista dina tillgångar. I följande kodexempel visas hur du itererar igenom samlingen Tillgångar på serverkontextobjektet. Med varje tillgång skriver kodexemplet också några av dess egenskapsvärden till konsolen. Varje tillgång kan till exempel innehålla många mediefiler. Kodexemplet skriver ut alla filer som är associerade med varje tillgång.

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

## <a name="get-a-job-reference"></a>Hämta en jobbreferens

När du arbetar med att bearbeta uppgifter i Media Services-kod måste du ofta få en referens till ett befintligt jobb baserat på ett ID. I följande kodexempel visas hur du hämtar en referens till ett IJob-objekt från projektsamlingen.

Du kan behöva hämta en jobbreferens när du startar ett tidskrävande kodningsjobb och måste kontrollera jobbstatusen på en tråd. I sådana fall, när metoden återgår från en tråd, måste du hämta en uppdaterad referens till ett jobb.

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
En viktig relaterad uppgift är att lista resurser med tillhörande jobb i Media Services. I följande kodexempel visas hur du listar varje IJob-objekt och sedan för varje jobb visar det egenskaper om jobbet, alla relaterade aktiviteter, alla indatatillgångar och alla utdatatillgångar. Koden i det här exemplet kan vara användbar för många andra uppgifter. Om du till exempel vill visa utdatatillgångarna från ett eller flera kodningsjobb som du körde tidigare, visar den här koden hur du kommer åt utdatatillgångarna. När du har en referens till en utdatatillgång kan du sedan leverera innehållet till andra användare eller program genom att hämta det eller tillhandahålla webbadresser. 

Mer information om alternativ för att leverera tillgångar finns i [Leverera tillgångar med Media Services SDK för .NET](media-services-deliver-streaming-content.md).

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

## <a name="list-all-access-policies"></a>Lista alla åtkomstprinciper
I Media Services kan du definiera en åtkomstprincip för en tillgång eller dess filer. En åtkomstprincip definierar behörigheterna för en fil eller en tillgång (vilken typ av åtkomst och varaktighet). I mediatjänstkoden definierar du vanligtvis en åtkomstprincip genom att skapa ett IAccessPolicy-objekt och sedan associera det med en befintlig tillgång. Sedan skapar du ett ILocator-objekt som gör att du kan ge direkt åtkomst till tillgångar i Media Services. Visual Studio-projektet som medföljer den här dokumentationsserien innehåller flera kodexempel som visar hur du skapar och tilldelar åtkomstprinciper och positionerare till tillgångar.

I följande kodexempel visas hur du listar alla åtkomstprinciper på servern och visar vilken typ av behörigheter som är associerade med var och en. Ett annat användbart sätt att visa åtkomstprinciper är att lista alla ILocator-objekt på servern, och sedan kan du lista dess associerade åtkomstprincip för varje positionerare med hjälp av egenskapen AccessPolicy.

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
    
## <a name="limit-access-policies"></a>Begränsa åtkomstprinciper 

>[!NOTE]
> Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). 

Du kan till exempel skapa en allmän uppsättning principer med följande kod som bara skulle köras en gång i ditt program. Du kan logga ID:er till en loggfil för senare användning:

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

Sedan kan du använda de befintliga ID:na i koden så här:

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

## <a name="list-all-locators"></a>Lista alla positionerare
En positionerare är en URL som ger en direkt sökväg för åtkomst till en tillgång, tillsammans med behörigheter till tillgången enligt definitionen i positionerarens associerade åtkomstprincip. Varje tillgång kan ha en samling ILocator-objekt som är associerade med den på egenskapen Locators. Serverkontexten har också en locators-samling som innehåller alla positionerare.

I följande kodexempel visas alla positionerare på servern. För varje positionerare visas ID:et för den relaterade tillgångs- och åtkomstprincipen. Den visar också typen av behörigheter, utgångsdatum och den fullständiga sökvägen till tillgången.

Observera att en positionerarväg till en tillgång bara är en bas-URL till tillgången. Om du vill skapa en direkt sökväg till enskilda filer som en användare eller ett program kan bläddra till måste koden lägga till den specifika filsökvägen i sökvägen till positioneraren. Mer information om hur du gör detta finns i avsnittet [Leverera resurser med Media Services SDK för .NET](media-services-deliver-streaming-content.md).

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

## <a name="enumerating-through-large-collections-of-entities"></a>Räkna upp genom stora samlingar av enheter
Vid fråga entiteter finns det en gräns på 1 000 entiteter som returneras samtidigt eftersom offentliga REST v2 begränsar frågeresultat till 1 000 resultat. Du måste använda Hoppa över och ta när du räknar upp genom stora samlingar av entiteter. 

Följande funktion går igenom alla jobb i det tillföljande Media Services-kontot. Media Services returnerar 1000 jobb i Jobbsamling. Funktionen använder Hoppa över och ta för att se till att alla jobb räknas upp (om du har fler än 1000 jobb i ditt konto).

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
I följande exempel tas en tillgång bort.

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
Om du vill ta bort ett jobb måste du kontrollera tillståndet för jobbet som anges i egenskapen State. Jobb som är slutförda eller avbrutna kan tas bort, medan jobb som finns i vissa andra tillstånd, till exempel köade, schemalagda eller bearbetning, måste avbrytas först och sedan kan de tas bort.

I följande kodexempel visas en metod för att ta bort ett jobb genom att kontrollera jobbtillstånd och sedan ta bort när tillståndet är klart eller avbrutet. Den här koden beror på föregående avsnitt i det här avsnittet för att få en referens till ett jobb: Hämta en jobbreferens.

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
I följande kodexempel visas hur du hämtar en referens till en åtkomstprincip baserat på ett princip-ID och sedan tar bort principen.

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

