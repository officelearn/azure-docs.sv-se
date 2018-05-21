---
title: Analysera videor med Azure Media Services | Microsoft Docs
description: Följ stegen i den här självstudien för att analysera videor med Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: a228443a787db2cc71a7700b978d8f52edb59e14
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-analyze-videos-with-azure-media-services"></a>Självstudie: Analysera videor med Azure Media Services 

Den här självstudien visar hur du analyserar videor med Azure Media Services. Det finns många scenarier där du kanske vill veta mer om inspelat video- eller ljudinnehåll. Organisationer kan till exempel för att uppnå högre kundnöjdhet köra tal-till-text-bearbetning och konvertera inspelningar från kundtjänst till en sökbar katalog med index och instrumentpaneler. Det blir då lättare att få fler insikter om verksamheten, till exempel en lista med vanliga klagomål, källor till sådana klagomål osv.

I den här självstudiekursen lär du dig att:    

> [!div class="checklist"]
> * Starta Azure Cloud Shell
> * Skapa ett Media Services-konto
> * Åtkomst till Media Services-API:n
> * Konfigurera exempelappen
> * Granska exempelkoden i detalj
> * Kör appen
> * Granska utdatan
> * Rensa resurser

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte har Visual Studio installerat kan du hämta [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Hämta exemplet

Klona en GitHub-lagringsplats som innehåller .NET-exemplet till din dator med följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-sample-code-in-detail"></a>Granska exempelkoden i detalj

Det här avsnittet går igenom funktionerna som definierades i filen [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) för projektet *AnalyzeVideos*.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Börja använda API:er för Media Services med .NET SDK

Om du vill börja använda API:er för Media Services med .NET, måste du skapa ett **AzureMediaServicesClient**-objekt. När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. Först måste du hämta en token och sedan skapa ett **ClientCredential**-objekt från den token som returnerades. I den kod som du klonade i början av artikeln används objektet **ArmClientCredential** för att hämta token.  

```csharp
private static IAzureMediaServicesClient CreateMediaServicesClient(ConfigWrapper config)
{
    ArmClientCredentials credentials = new ArmClientCredentials(config);

    return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
    {
        SubscriptionId = config.SubscriptionId,
    };
}
```

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Skapa en utdatatillgång där resultatet av ett jobb lagras 

Utdatatillgången lagrar resultatet av ditt jobb. Projektet definierar funktionen **DownloadResults** som laddar ner resultaten från utdatatillgången till mappen ”utdata”, så att du kan se vad du har fått.

```csharp
private static Asset CreateOutputAsset(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string assetName)
{
    // Check if an Asset already exists
    Asset outputAsset = client.Assets.Get(resourceGroupName, accountName, assetName);
    Asset asset = new Asset();
    string outputAssetName = assetName;

    if (outputAsset != null)
    {
        // Name collision! In order to get the sample to work, let's just go ahead and create a unique asset name
        // Note that the returned Asset can have a different name than the one specified as an input parameter.
        // You may want to update this part to throw an Exception instead, and handle name collisions differently.
        string uniqueness = @"-" + Guid.NewGuid().ToString();
        outputAssetName += uniqueness;
    }

    return client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, asset);
}
```
### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Skapa en transformering och ett jobb som analyserar videor

När kodningen eller bearbetningen av innehåll i Media Services görs, konfigureras vanligtvis kodningsinställningarna som ett recept. Du skickar sedan ett **Jobb** som tillämpar receptet på en video. Genom att skicka nya jobb för varje ny video, tillämpar du receptet på alla videor i biblioteket. Ett recept i Media Services kallas för en **Transformering**. Mer information finns i [Transformeringar och jobb](transform-concept.md). Det exempel som beskrivs i självstudien definierar ett recept som analyserar den angivna videon. 

#### <a name="transform"></a>Transformering

När du skapar en ny instans för en **Transformering**, måste du ange vilken utdata du vill att den ska skapa. Den obligatoriska parametern är objektet **TransformOutput** som visas i koden ovan. Varje **TransformOutput** innehåller en **Förinställning**. I **Förinställning** finns stegvisa anvisningar för den video- och/eller ljudbearbetning som ska användas för att generera önskad **TransformOutput**. I det här exemplet används förinställningen **VideoAnalyzerPreset** och språket (”en-US”) har överförts till konstruktorn. Med denna förinställning kan du extrahera flera ljud- och videoinsikter från en video. Du kan använda förinställningen **AudioAnalyzerPreset** om du vill extrahera flera ljudinsikter från en video. 

När du skapar en **Transformering** bör du först kontrollera om det redan finns en. Det gör du med metoden **Get** som visas i koden nedan.  I Media Services v3 returnerar **Get**-metoderna i entiteter **null** om entiteten inte finns (skiftlägesokänslig kontroll av namnet).

```csharp
private static Transform EnsureTransformExists(IAzureMediaServicesClient client, 
    string resourceGroupName, 
    string accountName, 
    string transformName, 
    Preset preset)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Start by defining the desired outputs.
        TransformOutput[] outputs = new TransformOutput[]
        {
            new TransformOutput(preset),
        };

        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs);
    }

    return transform;
}
```

#### <a name="job"></a>Jobb

Som nämns ovan är objektet **Transformering** receptet och ett **Jobb** är det faktiska begärandet till Media Services om att tillämpa **transformeringen** på en indatavideo eller ett ljudinnehåll. **Jobb** anger information som platsen för indatavideon och platsen för utdatan. Du kan ange platsen för din video med: HTTPS-webbadresser, SAS-URL:er eller tillgångar som finns på ditt Media Services-konto. 

I det här exemplet laddas den inkommande videon upp från en angiven HTTPS-webbadress.  

```csharp
private static Job SubmitJob(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName, string jobName, string outputAssetName)
{
    JobInputHttp jobInput =
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });
            
    string uniqueJobName = jobName;
    Job job = client.Jobs.Get(resourceGroupName, accountName, transformName, jobName);

    if (job != null)
    {
        // Job already exists with the same name, so let's append a GUID
        string uniqueness = @"-" + Guid.NewGuid().ToString();
        uniqueJobName += uniqueness;
    }

    JobOutput[] jobOutputs =
    {
        new JobOutputAsset(outputAssetName),
    };

    job = client.Jobs.Create(
                resourceGroupName,
                accountName,
                transformName,
                uniqueJobName,
                new Job
                {
                    Input = jobInput,
                    Outputs = jobOutputs,
                });

    return job;
}
```

### <a name="wait-for-the-job-to-complete"></a>Vänta tills jobbet är klart

Jobbet tar en stund att slutföra och du meddelas när detta sker. Det finns olika alternativ för att få meddelanden om när jobben är klara. Det enklaste alternativet (som visas här) är att använda avsökning. 

Vi rekommenderar inte att utföra avsökning av produktionsprogram på grund av att svarstiden kan bli lång. Avsökningen kan begränsas om den överanvänds på ett konto. Utvecklare bör i stället använda Event Grid.

Event Grid är utformat för hög tillgänglighet, konsekvent prestanda och dynamisk skalning. Med Event Grid kan dina appar lyssna efter och reagera på händelser från i princip alla Azure-tjänster, samt även från anpassade källor. Med enkel och HTTP-baserad reaktiv händelsehantering blir det lättare att skapa effektiva lösningar med hjälp av intelligent filtrering och dirigering av händelser. Se [Dirigera händelser till en anpassad webbslutpunkt](job-state-events-cli-how-to.md).

**Jobb** har vanligtvis följande tillstånd: **Schemalagd**, **I kö**, **Bearbetas**, **Slutförd** (slutlig status). Om jobbet har påträffat ett fel visas tillståndet **Fel**. Om jobbet avbryts visas **Avbryter** och **Avbruten** när det är klart.

```csharp
private static Job WaitForJobToFinish(IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName)
{
    int SleepInterval = 60 * 1000;

    Job job = null;

    while (true)
    {
        job = client.Jobs.Get(resourceGroupName, accountName, transformName, jobName);

        if (job.State == JobState.Finished || job.State == JobState.Error || job.State == JobState.Canceled)
        {
            break;
        }

        Console.WriteLine($"Job is {job.State}.");
        for (int i = 0; i < job.Outputs.Count; i++)
        {
            JobOutput output = job.Outputs[i];
            Console.Write($"\tJobOutput[{i}] is {output.State}.");
            if (output.State == JobState.Processing)
            {
                Console.Write($"  Progress: {output.Progress}");
            }
            Console.WriteLine();
        }
        System.Threading.Thread.Sleep(SleepInterval);
    }

    return job;
}
```

### <a name="download-the-result-of-the-job"></a>Ladda ned jobbets resultat

 Följande funktion laddar ned resultaten från utdatatillgången till mappen ”utdata”, så att du kan undersöka jobbresultatet. 

```csharp
private static void DownloadResults(IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string assetName,
    string resultsFolder)
{
    AssetContainerSas assetContainerSas = client.Assets.ListContainerSas(
            resourceGroup,
            accountName,
            assetName,
            permissions: AssetContainerPermission.Read,
            expiryTime: DateTime.UtcNow.AddHours(1).ToUniversalTime()
            );

    Uri containerSasUrl = new Uri(assetContainerSas.AssetContainerSasUrls.FirstOrDefault());
    CloudBlobContainer container = new CloudBlobContainer(containerSasUrl);

    string directory = Path.Combine(resultsFolder, assetName);
    Directory.CreateDirectory(directory);

    Console.WriteLine("Downloading results to {0}.", directory);

    foreach (IListBlobItem blobItem in container.ListBlobs(null, true, BlobListingDetails.None))
    {
        if (blobItem is CloudBlockBlob)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            string filename = Path.Combine(directory, blob.Name);

            blob.DownloadToFile(filename, FileMode.Create);
        }
    }

    Console.WriteLine("Download complete.");
}
```

### <a name="clean-up-resource-in-your-media-services-account"></a>Rensa resurser på ditt Media Services-konto

Vanligtvis bör du rensa bort allt utom objekt som du tänker återanvända (om du t.ex. återanvänder transformeringar behåller du StreamingLocators osv.). Om du vill att ditt konto ska vara rensat efter experimentet, bör du ta bort de resurser som du inte tänker återanvända. Följande kod tar exempelvis bort Jobb.

```csharp
static void CleanUp(IAzureMediaServicesClient client,
        string resourceGroupName,
        string accountName, 
        string transformName)
{
    foreach (var job in client.Jobs.List(resourceGroupName, accountName, transformName))
    {
        client.Jobs.Delete(resourceGroupName, accountName, transformName, job.Name);
    }

    foreach (var asset in client.Assets.List(resourceGroupName, accountName))
    {
        client.Assets.Delete(resourceGroupName, accountName, asset.Name);
    }
}
```

## <a name="run-the-sample-app"></a>Kör exempelappen

Tryck på Ctrl+F5 för att köra programmet *AnalyzeVideos*.

När vi kör programmet skapar jobbet miniatyrer för varje ansikte som hittas i videon. Det skapar också filen insights.json.

## <a name="examine-the-output"></a>Granska utdatan

Utdatafilen för videoanalysen kallas insights.json. Den här filen innehåller insikter om videon. Beskrivning av elementen i json-filen finns i artikeln [Medieintelligens](intelligence-concept.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive Media Services och de lagringskonton som du skapade för självstudien, tar du bort resursgruppen som du skapade tidigare. Du kan använda verktyget **CloudShell**.

Kör följande kommando i **CloudShell**:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Flertrådsteknik

SDK:erna i Azure Media Services v3 är inte trådsäkra. När du arbetar med flertrådade program bör du skapa ett nytt AzureMediaServicesClient-objekt per tråd.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Ladda upp, koda och strömma filer](stream-files-tutorial-with-api.md)
