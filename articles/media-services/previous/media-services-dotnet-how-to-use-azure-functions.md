---
title: Utveckla Azure Functions med Media Services
description: Det här avsnittet visar hur du börja utveckla Azure Functions med Media Services med Azure portal.
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 69099fe1369223871142f74d8f6d1c6a8bd66dab
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57851781"
---
# <a name="develop-azure-functions-with-media-services"></a>Utveckla Azure Functions med Media Services

Den här artikeln visar hur du kommer igång med att skapa Azure Functions med Media Services. Azure-funktion som definierats i den här artikeln övervakar en lagringsbehållare för kontot med namnet **inkommande** för nya MP4-filer. När en fil läggs till storage-behållare, kör blob-utlösare funktionen. Azure functions finns i [översikt](../../azure-functions/functions-overview.md) och andra avsnitt i den **Azure functions** avsnittet.

Om du vill utforska och distribuera Azure Functions med Azure Media Services kan du kolla [Media Services, Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Den här lagringsplatsen innehåller exempel som använder Media Services för att visa arbetsflöden som rör mata in innehåll direkt från blob-lagring, kodning och skriva innehåll tillbaka till blob storage. Den innehåller även exempel på hur du övervakar jobbmeddelanden via WebHooks och Azure-köer. Du kan också skapa dina funktioner baserat på exemplen i den [Media Services, Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) lagringsplats. Om du vill distribuera funktionerna, trycker du på den **distribuera till Azure** knappen.

## <a name="prerequisites"></a>Förutsättningar

- Innan du kan skapa din första funktion måste du ha ett aktivt Azure-konto. Om du inte redan har ett Azure-konto, [finns kostnadsfria konton tillgängliga](https://azure.microsoft.com/free/).
- Om du ska skapa Azure Functions som utför åtgärder på ditt konto för Azure Media Services (AMS) eller lyssna på händelser som skickas av Media Services, bör du skapa ett AMS-konto som beskrivs [här](media-services-portal-create-account.md).
    
## <a name="create-a-function-app"></a>Skapa en funktionsapp

1. Gå till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.
2. Skapa en funktionsapp enligt [här](../../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Ett lagringskonto som du anger i den **StorageConnection** miljövariabeln (se nästa steg) ska vara i samma region som din app.

## <a name="configure-function-app-settings"></a>Konfigurera funktionsappinställningar

När du utvecklar funktioner i Media Services är det praktiskt att lägga till miljövariabler som kommer att användas i dina funktioner. Om du vill konfigurera inställningarna för appen klickar du på länken konfigurera Appinställningar. Mer information finns i [så här konfigurerar du Azure funktionsappinställningar](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

Den funktion som definierats i den här artikeln förutsätter att du har följande miljövariabler i inställningarna för din app:

**AMSAADTenantDomain**: Azure AD tenant-slutpunkten. Läs mer om att ansluta till AMS-API, [detta](media-services-use-aad-auth-to-access-ams-api.md) artikeln.

**AMSRESTAPIEndpoint**:  URI som representerar REST API-slutpunkt. 

**AMSClientId**: Azure AD programmets klients-ID.

**AMSClientSecret**: Azure AD-programklienthemlighet.

**StorageConnection**: lagringsanslutning för det konto som är associerade med Media Services-kontot. Det här värdet används i den **function.json** fil och **run.csx** fil (beskrivs nedan).

## <a name="create-a-function"></a>Skapa en funktion

När funktionsappen har distribuerats, kan du hitta den bland **Apptjänster** Azure Functions.

1. Välj funktionsappen och klicka på **ny funktion**.
2. Välj den **C#** språk och **databearbetning** scenario.
3. Välj **BlobTrigger** mall. Den här funktionen som utlöses när en blob har överförts till den **inkommande** behållare. Den **inkommande** namn anges i den **sökväg**, i nästa steg.

    ![filer](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. När du har valt **BlobTrigger**, några fler kontroller som visas på sidan.

    ![filer](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Klicka på **Skapa**. 

## <a name="files"></a>Filer

Din Azure-funktion är associerad med kod och andra filer som beskrivs i det här avsnittet. När du använder Azure-portalen för att skapa en funktion **function.json** och **run.csx** skapas åt dig. Du behöver lägga till eller ladda upp en **project.json** fil. Resten av det här avsnittet ger en kort beskrivning av varje fil och visar deras definitioner.

![filer](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Filen function.json definierar bindningarna som funktionen och andra konfigurationsinställningar. Körningen använder den här filen för att hitta händelser att övervaka och hur du överför data till och returnera data från körning av funktion. Mer information finns i [HTTP och webhook-bindningar för Azure functions](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Ange den **inaktiverad** egenskap **SANT** att förhindra att funktionen som körs. 

Ersätt innehållet i den befintliga function.json-filen med följande kod:

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

### <a name="projectjson"></a>project.json

Filen project.json innehåller beroenden. Här är ett exempel på **project.json** fil som innehåller de nödvändiga .NET Azure Media Services-paketen från Nuget. Observera att versionsnumren ändra med de senaste uppdateringarna till paket, så att du bekräftar du de senaste versionerna. 

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
    
### <a name="runcsx"></a>run.csx

Det här är den C# koden för din funktion.  Funktionen som definieras nedan Övervakare en lagringsbehållare för kontot med namnet **inkommande** (det är vad som har angetts i sökvägen) för nya MP4-filer. När en fil läggs till storage-behållare, kör blob-utlösare funktionen.
    
Exempel som definierats i det här avsnittet visar 

1. Så här att mata in en tillgång till ett Media Services-konto (genom att hantera en blob till en tillgång med AMS) och 
2. hur man skickar ett kodningsjobb som använder Media Encoder Standard förinställda ”Adaptiv direktuppspelning”.

I verkligheten-scenariot du mest sannolika följa jobbförloppet och sedan publicera den kodade tillgången. Mer information finns i [Använd Azure WebHooks för att övervaka jobbmeddelanden för Media Services](media-services-dotnet-check-job-progress-with-webhooks.md). Fler exempel finns i [Media Services, Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Ersätt innehållet i den befintliga filen run.csx med följande kod: När du är klar definiera din funktion klickar du på **spara och kör**.

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

Om du vill testa funktionen måste du ladda upp en MP4-fil i den **inkommande** behållare för det lagringskonto som du angav i anslutningssträngen.  

1. Välj det lagringskonto som du angav i den **StorageConnection** miljövariabeln.
2. Klicka på **Blobar**.
3. Klicka på **+ behållare**. Namnge behållaren **inkommande**.
4. Tryck på **överför** och bläddra till en MP4-fil som du vill ladda upp.

>[!NOTE]
> När du använder en blob-utlösare i en förbrukningsplan, kan det vara upp till en 10 minuters fördröjning vid bearbetningen av nya blobbar när en funktionsapp är inaktiv. När appen körs behandlas BLOB-objekt direkt. Mer information finns i [Blob storage-utlösare och bindningar](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).

## <a name="next-steps"></a>Nästa steg

Nu är du redo att börja utveckla ett Media Services-program. 
 
Mer information och fullständiga exempel/lösningar för att skapa arbetsflöden för skapande av anpassade innehåll med Azure Functions och Logic Apps med Azure Media Services finns i den [Media Services .NET Functions Integration exemplet på GitHub](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Se även [Använd Azure WebHooks för att övervaka jobbmeddelanden för Media Services med .NET](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

