---
title: Utveckla Azure-funktioner med Media Services
description: Det här avsnittet visar hur du börjar utveckla Azure-funktioner med Media Services med Hjälp av Azure-portalen.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 618acae10b874eb5ebd5b6da7fe081368528dbd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251172"
---
# <a name="develop-azure-functions-with-media-services"></a>Utveckla Azure-funktioner med Media Services

Den här artikeln visar hur du kommer igång med att skapa Azure-funktioner som använder Media Services. Azure-funktionen som definieras i den här artikeln övervakar en lagringskontobehållare med **namnet indata** för nya MP4-filer. När en fil släpps i lagringsbehållaren kör blob-utlösaren funktionen. Information om hur du granskar Azure-funktioner finns i [Översikt](../../azure-functions/functions-overview.md) och andra avsnitt i avsnittet **Azure-funktioner.**

Om du vill utforska och distribuera befintliga Azure-funktioner som använder Azure Media Services kan du läsa [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Den här databasen innehåller exempel som använder Media Services för att visa arbetsflöden som är relaterade till intag av innehåll direkt från blob-lagring, kodning och skrivning av innehåll tillbaka till blob-lagring. Den innehåller också exempel på hur du övervakar jobbmeddelanden via WebHooks och Azure-köer. Du kan också utveckla dina funktioner baserat på exemplen i [Databasen För Azure Functions i Media Services.](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) Om du vill distribuera funktionerna trycker du på knappen **Distribuera till Azure.**

## <a name="prerequisites"></a>Krav

- Innan du kan skapa din första funktion måste du ha ett aktivt Azure-konto. Om du inte redan har ett Azure-konto [finns kostnadsfria konton tillgängliga](https://azure.microsoft.com/free/).
- Om du ska skapa Azure Functions som utför åtgärder på ditt AMS-konto (Azure Media Services) eller lyssna på händelser som skickas av Media Services, bör du skapa ett AMS-konto enligt beskrivningen [här](media-services-portal-create-account.md).
    
## <a name="create-a-function-app"></a>Skapa en funktionsapp

1. Gå till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.
2. Skapa en funktionsapp enligt beskrivningen [här](../../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Ett lagringskonto som du anger i **lagringsanslutningsmiljöns** variabel (se nästa steg) ska finnas i samma region som din app.

## <a name="configure-function-app-settings"></a>Konfigurera inställningar för funktionsappar

När du utvecklar Media Services-funktioner är det praktiskt att lägga till miljövariabler som ska användas i alla dina funktioner. Om du vill konfigurera appinställningar klickar du på länken Konfigurera appinställningar. Mer information finns i [Så här konfigurerar du azure function-appinställningar](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

Funktionen, som definieras i den här artikeln, förutsätter att du har följande miljövariabler i appinställningarna:

**AMSAADTenantDomain**: Slutpunkt för Azure AD-klient. Mer information om hur du ansluter till AMS API finns i den [här](media-services-use-aad-auth-to-access-ams-api.md) artikeln.

**AMSRESTAPIEndpoint**: URI som representerar REST API-slutpunkten. 

**AMSClientId**: Azure AD-programklient-ID.

**AMSClientSecret**: Azure AD-programklienthemma.

**StorageConnection**: lagringsanslutning av kontot som är kopplat till Media Services-kontot. Det här värdet används i **filen function.json** och **filen run.csx** (beskrivs nedan).

## <a name="create-a-function"></a>Skapa en funktion

När din funktionsapp har distribuerats kan du hitta den bland **App Services** Azure Functions.

1. Markera funktionsappen och klicka på **Ny funktion**.
2. Välj scenariot **C#** språk och **databehandling.**
3. Välj **BlobTrigger-mall.** Den här funktionen utlöses när en blob överförs till **indatabehållaren.** **Indatanamnet** anges i **sökvägen**i nästa steg.

    ![filer](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. När du har valt **BlobTrigger**visas ytterligare några kontroller på sidan.

    ![filer](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Klicka på **Skapa**. 

## <a name="files"></a>Filer

Din Azure-funktion är associerad med kodfiler och andra filer som beskrivs i det här avsnittet. När du använder Azure-portalen för att skapa en funktion skapas **function.json** och **run.csx** åt dig. Du måste lägga till eller ladda upp en **project.json-fil.** Resten av det här avsnittet ger en kort förklaring av varje fil och visar deras definitioner.

![filer](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Filen function.json definierar funktionsbindningar och andra konfigurationsinställningar. Körningen använder den här filen för att bestämma vilka händelser som ska övervakas och hur data ska överföras till och returnera data från funktionskörning. Mer information finns i [Azure-funktioner HTTP- och webhook-bindningar](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Ange egenskapen **inaktiverad** till **true** för att förhindra att funktionen körs. 

Ersätt innehållet i den befintliga filen function.json med följande kod:

```json
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>projekt.json

Filen project.json innehåller beroenden. Här är ett exempel på **project.json-fil** som innehåller de nödvändiga .NET Azure Media Services-paketen från Nuget. Observera att versionsnumren ändras med de senaste uppdateringarna av paketen, så du bör bekräfta de senaste versionerna. 

Lägg till följande definition i project.json. 

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```
    
### <a name="runcsx"></a>run.csx (på andra)

Det här är C#-koden för din funktion.  Funktionen som definieras nedan övervakar en lagringskontobehållare med namnet **indata** (det är vad som angavs i sökvägen) för nya MP4-filer. När en fil släpps i lagringsbehållaren kör blob-utlösaren funktionen.
    
Exemplet som definieras i det här avsnittet visar 

1. hur du intar en tillgång till ett Media Services-konto (genom att hantera en klump i en AMS-tillgång) och 
2. skicka in ett kodningsjobb som använder Media Encoder Standards förinställning "Adaptiv strömning".

I verkliga scenariot vill du troligen spåra jobbförloppet och sedan publicera den kodade tillgången. Mer information finns i [Använda Azure WebHooks för att övervaka Jobbmeddelanden för Media Services](media-services-dotnet-check-job-progress-with-webhooks.md). Fler exempel finns i [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Ersätt innehållet i den befintliga filen run.csx med följande kod: När du är klar med att definiera funktionen klickar du på **Spara och kör**.

```csharp
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

    }
    catch (Exception ex)
    {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
    }
}

private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
    IAsset newAsset = null;

    try{
        Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
        newAsset = await copyAssetTask;
        log.Info($"Asset Copied : {newAsset.Id}");
    }
    catch(Exception ex){
        log.Info("Copy Failed");
        log.Info($"ERROR : {ex.Message}");
        throw ex;
    }

    return newAsset;
}

/// <summary>
/// Creates a new asset and copies blobs from the specifed storage account.
/// </summary>
/// <param name="blob">The specified blob.</param>
/// <returns>The new asset.</returns>
public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
{
     //Get a reference to the storage account that is associated with the Media Services account. 
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

    // Create a new asset. 
    var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
    log.Info($"Created new asset {asset.Name}");

    IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
    TimeSpan.FromHours(4), AccessPermissions.Write);
    ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

    // Get the destination asset container reference
    string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
    CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

    try{
    assetContainer.CreateIfNotExists();
    }
    catch (Exception ex)
    {
    log.Error ("ERROR:" + ex.Message);
    }

    log.Info("Created asset.");

    // Get hold of the destination blob
    CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

    // Copy Blob
    try
    {
    using (var stream = await blob.OpenReadAsync()) 
    {            
        await destinationBlob.UploadFromStreamAsync(stream);          
    }

    log.Info("Copy Complete.");

    var assetFile = asset.AssetFiles.Create(blob.Name);
    assetFile.ContentFileSize = blob.Properties.Length;
    assetFile.IsPrimary = true;
    assetFile.Update();
    asset.Update();
    }
    catch (Exception ex)
    {
    log.Error(ex.Message);
    log.Info (ex.StackTrace);
    log.Info ("Copy Failed.");
    throw;
    }

    destinationLocator.Delete();
    writePolicy.Delete();

    return asset;
}
```

## <a name="test-your-function"></a>Testa din funktion

Om du vill testa funktionen måste du ladda upp en MP4-fil till **indatabehållaren** för lagringskontot som du angav i anslutningssträngen.  

1. Välj det lagringskonto som du angav i **lagringsanslutningsmiljöns** variabel.
2. Klicka på **Blobbar**.
3. Klicka på **+ Container**. Ge **behållaren ett namn**.
4. Tryck **på Ladda upp** och bläddra till en MP4-fil som du vill ladda upp.

>[!NOTE]
> När du använder en blob-utlösare på en förbrukningsplan kan det ta upp till 10 minuters fördröjning i bearbetningen av nya blobbar efter att en funktionsapp har gått inaktiv. När funktionsappen körs bearbetas blobbar omedelbart. Mer information finns i [Blob-lagringsutlösare och bindningar](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).

## <a name="next-steps"></a>Nästa steg

Nu är du redo att börja utveckla ett Media Services-program. 
 
Mer information och komplettemplar/lösningar för att använda Azure Functions och Logic Apps med Azure Media Services för att skapa arbetsflöden för skapande av anpassat innehåll finns i [exempelt på integrering av mediatjänster .NET Functions på GitHub](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Se också [Använda Azure WebHooks för att övervaka Jobbmeddelanden för Media Services med .NET](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

