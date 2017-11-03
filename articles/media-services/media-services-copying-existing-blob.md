---
title: "Kopiera blobbar från ett lagringskonto till ett Azure Media Services tillgång | Microsoft Docs"
description: "Det här avsnittet visar hur du kopierar en befintlig blob till ett Media Services tillgång. I exemplet används Azure Media Services .NET SDK-tilläggen."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 6a63823f-f3c9-424c-91b8-566f70bec346
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/03/2017
ms.author: juliako
ms.openlocfilehash: 2bc1f0114a096920d4a7c9cb57e44c9b3612bf86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="copying-existing-blobs-into-a-media-services-asset"></a>Kopiera befintlig BLOB till ett Media Services tillgång
Det här avsnittet beskrivs hur du kopierar blobar från ett lagringskonto till ett nytt Azure Media Services (AMS) tillgångsinformation med hjälp av [Azure Media Services .NET SDK-tilläggen](https://github.com/Azure/azure-sdk-for-media-services-extensions/).

Det fungerar tillsammans med tilläggsmetoder:

- Vanliga tillgångar.
- Live Arkiv tillgångar (FragBlob format).
- Käll- och tillgångar som hör till olika Media Services-konton (även över olika datacenter). Det kan dock finnas avgifter på så sätt. Mer information om priser finns [dataöverföringar](https://azure.microsoft.com/pricing/#header-11).

> [!NOTE]
> Du bör inte försöka ändra innehållet i blob-behållare som har genererats av Media Services utan att använda Media Service API: er.
> 

I avsnittet visas två kodexempel:

1. Kopiera blobbar från en tillgång i en AMS-kontot till en ny tillgång i en annan AMS-kontot.
2. Kopiera blobbar från vissa storage-konto till en ny tillgång i AMS-kontot.

## <a name="copy-blobs-between-two-ams-accounts"></a>Kopiera BLOB mellan två AMS-konton  

### <a name="prerequisites"></a>Krav

Två Media Services-konton. Mer information finns i [Så här skapar du ett Media Services-konto](media-services-portal-create-account.md).

### <a name="download-sample"></a>Hämta exempel
Du kan följa stegen i den här artikeln eller hämta ett exempel som innehåller koden som beskrivs i den här artikeln från [här](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/).

### <a name="set-up-your-project"></a>Konfigurera ditt projekt

1. Ställ in din utvecklingsmiljö enligt beskrivningen i [Media Services-utveckling med .NET](media-services-dotnet-how-to-use.md). 
2. Lägg till andra referenser som krävs för det här projektet: System.Configuration.
3. Lägg till avsnittet AppSettings i .config-filen och uppdatera värden baserat på Media Services-konton, mål-lagringskontot och källans tillgången-ID.  

```   
<appSettings>
    <add key="AMSSourceAADTenantDomain" value="AADTenantDomain"/>
    <add key="AMSSourceRESTAPIEndpoint" value="RESTAPIEndpoint"/>
    <add key="AMSDestAADTenantDomain" value="AADTenantDomain"/>
    <add key="AMSDestRESTAPIEndpoint" value="RESTAPIEndpoint"/>
    <add key="DestStorageAccountName" value="name"/>
    <add key="DestStorageAccountKey" value="key"/>
    <add key="SourceAssetID" value="nb:cid:UUID:assetID"/>
</appSettings>
```

### <a name="copy-blobs-from-an-asset-in-one-ams-account-into-an-asset-in-another-ams-account"></a>Kopiera blobbar från en tillgång i en AMS-kontot till en tillgång i en annan AMS-kontot

Följande kod använder tillägget **IAsset.Copy** metod för att kopiera alla filer i tillgången källan till målet tillgången använder ett enda tillägg.

```
using System;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Linq;
using System.Configuration;
using Microsoft.WindowsAzure.Storage.Auth;

namespace CopyExistingBlobsIntoAsset
{
    class Program
    {
        static string _sourceAADTenantDomain = ConfigurationManager.AppSettings["AMSSourceAADTenantDomain"];
        static string _sourceRESTAPIEndpoint = ConfigurationManager.AppSettings["AMSSourceRESTAPIEndpoint"];
        static string _destAADTenantDomain = ConfigurationManager.AppSettings["AMSDestAADTenantDomain"];
        static string _destRESTAPIEndpoint = ConfigurationManager.AppSettings["AMSDestRESTAPIEndpoint"];
        static string _destStorageAccountName = ConfigurationManager.AppSettings["DestStorageAccountName"];
        static string _destStorageAccountKey = ConfigurationManager.AppSettings["DestStorageAccountKey"];
        static string _sourceAssetID = ConfigurationManager.AppSettings["SourceAssetID"];

        private static CloudMediaContext _sourceContext = null;
        private static CloudMediaContext _destContext = null;

        static void Main(string[] args)
        {
            var tokenCredentials1 = new AzureAdTokenCredentials(_sourceAADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider1 = new AzureAdTokenProvider(tokenCredentials1);
            var tokenCredentials2 = new AzureAdTokenCredentials(_destAADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider2 = new AzureAdTokenProvider(tokenCredentials2);

            // Create the context for your source Media Services account.
            _sourceContext = new CloudMediaContext(new Uri(_sourceRESTAPIEndpoint), tokenProvider1);

            // Create the context for your destination Media Services account.
            _destContext = new CloudMediaContext(new Uri(_destRESTAPIEndpoint), tokenProvider2);

            // Get the credentials of the default Storage account bound to your destination Media Services account.
            StorageCredentials destinationStorageCredentials =
                new StorageCredentials(_destStorageAccountName, _destStorageAccountKey);

            // Get a reference to the source asset in the source context.
            IAsset sourceAsset = _sourceContext.Assets.Where(a => a.Id == _sourceAssetID).First();

            // Create an empty destination asset in the destination context.
            IAsset destinationAsset = _destContext.Assets.Create(sourceAsset.Name, AssetCreationOptions.None);

            // Copy the files in the source asset instance into the destination asset instance.
            sourceAsset.Copy(destinationAsset, destinationStorageCredentials);

            Console.WriteLine("Done");
        }
    }
}
```

## <a name="copy-blobs-from-a-storage-account-into-an-ams-account"></a>Kopiera blobbar från ett lagringskonto till ett AMS-konto 

### <a name="prerequisites"></a>Krav

- Ett lagringskonto som du vill kopiera BLOB.
- En AMS-kontot som du vill kopiera BLOB.

### <a name="set-up-your-project"></a>Konfigurera ditt projekt

1. Ställ in din utvecklingsmiljö enligt beskrivningen i [Media Services-utveckling med .NET](media-services-dotnet-how-to-use.md). 
2. Lägg till andra referenser som krävs för det här projektet: System.Configuration.
3. Lägg till avsnittet AppSettings i .config-filen och uppdatera värden baserat på dina käll lagrings- och AMS-konton.

```
<appSettings>
  <add key="SourceStorageAccountName" value="name" />
  <add key="SourceStorageAccountKey" value="key" />
  <add key="AMSAADTenantDomain" value="tenant"/>
  <add key="AMSESTAPIEndpoint" value="endpoint"/>
  <add key="AMSStorageAccountName" value="name" />
  <add key="AMSStorageAccountKey" value="key" />
</appSettings>
```

### <a name="copy-blobs-from-some-storage-account-into-a-new-asset-in-a-ams-account"></a>Kopiera blobbar från vissa storage-konto till en ny tillgång i AMS-kontot

Följande kod kopierar blobbar från ett lagringskonto till en tillgång med Media Services. 

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [detta](media-services-dotnet-manage-entities.md#limit-access-policies) avsnitt.

```
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
    
namespace CopyExistingBlobsIntoAsset
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AMSAADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _AMSRESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSESTAPIEndpoint"];
        private static readonly string _AMSStorageAccountName =
            ConfigurationManager.AppSettings["AMSStorageAccountName"];
        private static readonly string _AMSStorageAccountKey =
            ConfigurationManager.AppSettings["AMSStorageAccountKey"];
        private static readonly string _sourceStorageAccountName =
            ConfigurationManager.AppSettings["SourceStorageAccountName"];
        private static readonly string _sourceStorageAccountKey =
            ConfigurationManager.AppSettings["SourceStorageAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static CloudStorageAccount _sourceStorageAccount = null;
        private static CloudStorageAccount _destinationStorageAccount = null;

        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AMSAADTenantDomain, 
                AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Create the context for your source Media Services account.
            _context = new CloudMediaContext(new Uri(_AMSRESTAPIEndpoint), tokenProvider);
            
            _sourceStorageAccount =
                new CloudStorageAccount(new StorageCredentials(_sourceStorageAccountName,
                    _sourceStorageAccountKey), true);

            _destinationStorageAccount =
                new CloudStorageAccount(new StorageCredentials(_AMSStorageAccountName,
                    _AMSStorageAccountKey), true);

            CloudBlobClient sourceCloudBlobClient =
                _sourceStorageAccount.CreateCloudBlobClient();
            CloudBlobContainer sourceContainer =
                sourceCloudBlobClient.GetContainerReference("NameOfBlobContainerYouWantToCopy");

            CreateAssetFromExistingBlobs(sourceContainer);

            Console.WriteLine("Done");
        }

        static public IAsset CreateAssetFromExistingBlobs(CloudBlobContainer sourceBlobContainer)
        {
            CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

            // Create a new asset. 
            IAsset asset = _context.Assets.Create("NewAsset_" + Guid.NewGuid(), AssetCreationOptions.None);

            IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
                TimeSpan.FromHours(24), AccessPermissions.Write);

            ILocator destinationLocator =
                _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);

            // Get the asset container URI and Blob copy from mediaContainer to assetContainer. 
            CloudBlobContainer destAssetContainer =
                destBlobStorage.GetContainerReference((new Uri(destinationLocator.Path)).Segments[1]);

            if (destAssetContainer.CreateIfNotExists())
            {
                destAssetContainer.SetPermissions(new BlobContainerPermissions
                {
                    PublicAccess = BlobContainerPublicAccessType.Blob
                });
            }

            var blobList = sourceBlobContainer.ListBlobs();

            foreach (var sourceBlob in blobList)
            {
                var assetFile = asset.AssetFiles.Create((sourceBlob as ICloudBlob).Name);

                ICloudBlob destinationBlob = destAssetContainer.GetBlockBlobReference(assetFile.Name);

                CopyBlob(sourceBlob as ICloudBlob, destAssetContainer);

                assetFile.ContentFileSize = (sourceBlob as ICloudBlob).Properties.Length;
                assetFile.Update();
                Console.WriteLine("File {0} is of {1} size", assetFile.Name, assetFile.ContentFileSize);
            }

            asset.Update();

            destinationLocator.Delete();
            writePolicy.Delete();

            // Set the primary asset file.
            // If, for example, we copied a set of Smooth Streaming files, 
            // set the .ism file to be the primary file. 
            // If we, for example, copied an .mp4, then the mp4 would be the primary file. 
            var ismAssetFile = asset.AssetFiles.ToList().
                Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray().FirstOrDefault();

            // The following code assigns the first .ism file as the primary file in the asset.
            // An asset should have one .ism file.  
            if (ismAssetFile != null)
            {
                ismAssetFile.IsPrimary = true;
                ismAssetFile.Update();
            }

            return asset;
        }

        /// <summary>
        /// Copies the specified blob into the specified container.
        /// </summary>
        /// <param name="sourceBlob">The source container.</param>
        /// <param name="destinationContainer">The destination container.</param>
        static private void CopyBlob(ICloudBlob sourceBlob, CloudBlobContainer destinationContainer)
        {
            var signature = sourceBlob.GetSharedAccessSignature(new SharedAccessBlobPolicy
            {
                Permissions = SharedAccessBlobPermissions.Read,
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
            });

            ICloudBlob destinationBlob = destinationContainer.GetBlockBlobReference(sourceBlob.Name);

            if (destinationBlob.Exists())
            {
                Console.WriteLine(string.Format("Destination blob '{0}' already exists. Skipping.", destinationBlob.Uri));
            }
            else
            {

                // Display the size of the source blob.
                Console.WriteLine(sourceBlob.Properties.Length);

                Console.WriteLine(string.Format("Copy blob '{0}' to '{1}'", sourceBlob.Uri, destinationBlob.Uri));
                destinationBlob.StartCopyFromBlob(new Uri(sourceBlob.Uri.AbsoluteUri + signature));

                while (true)
                {
                    // The StartCopyFromBlob is an async operation, 
                    // so we want to check if the copy operation is completed before proceeding. 
                    // To do that, we call FetchAttributes on the blob and check the CopyStatus. 
                    destinationBlob.FetchAttributes();
                    if (destinationBlob.CopyState.Status != CopyStatus.Pending)
                    {
                        break;
                    }
                    //It's still not completed. So wait for some time.
                    System.Threading.Thread.Sleep(1000);
                }

                // Display the size of the destination blob.
                Console.WriteLine(destinationBlob.Properties.Length);

            }
        }
    }
}
```
## <a name="next-steps"></a>Nästa steg

Du kan nu koda överförda tillgångar. Mer information finns i [Koda tillgångar](media-services-portal-encode.md).

Du kan också använda Azure Functions för att utlösa ett kodningsjobb baserat på en fil som skickas till den konfigurerade behållaren. Mer information finns i [det här exemplet](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

