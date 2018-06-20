---
title: Skydda åtkomsten till ett programs data i molnet med Azure Storage | Microsoft Docs
description: Skydda ditt programs data i molnet med SAS-token, kryptering och HTTPS.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: tutorial
ms.date: 05/30/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: bfc5085374759290701f49cdf25698827c0779e7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650102"
---
# <a name="secure-access-to-an-applications-data-in-the-cloud"></a>Skydda åtkomsten till ett programs data i molnet

Den här självstudiekursen är den tredje delen i en serie. Du lär dig hur du skyddar åtkomsten till lagringskontot. 

I den tredje delen i serien får du lära dig hur du:

> [!div class="checklist"]
> * Använder SAS-token för att komma åt miniatyrbilder
> * Aktiverar kryptering på serversidan
> * Aktiverar endast HTTPS-transport

[Azure Blob Storage](../common/storage-introduction.md#blob-storage) tillhandahåller en robust tjänst för att lagra filer för program. Den här självstudiekursen kompletterar [det föregående avsnittet ][previous-tutorial] och visar hur du skyddar åtkomsten till ditt lagringskonto från ett webbprogram. När du är klar är bilderna krypterade och i webbappen används säkra SAS-token för att få åtkomst till miniatyrbilderna.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna utföra den här kursen måste du ha slutfört den föregående Storage-självstudiekursen: [Automatisera storleksändring av överförda bilder med Event Grid][previous-tutorial]. 

## <a name="set-container-public-access"></a>Ange offentlig åtkomst till behållare

I den här delen av kursserien används SAS-token för att få åtkomst till miniatyrbilder. I det här steget anger du offentlig åtkomst för behållaren med _miniatyrer_ till `off`.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbnails  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>Konfigurera SAS-token för miniatyrbilder

I den första delen i den här kursserien visade webbprogrammet bilder från en offentlig behållare. I den här delen använder du [SAS-token](../common/storage-dotnet-shared-access-signature-part-1.md#what-is-a-shared-access-signature) (signatur för delad åtkomst) för att hämta minatyrbilderna. SAS-token ger dig begränsad åtkomst till en behållare eller blob baserat på IP-protokoll, tidsintervall eller rättigheter.

I det här exemplet använder lagringsplatsen för källkod `sasTokens`-grenen, som har ett uppdaterat kodexempel. Ta bort den befintliga GitHub-distributionen med [az webapp deployment source delete](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_delete). Konfigurera sedan GitHub-distributionen till webbappen med kommandot [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config).  

I följande kommando är `<web-app>` namnet på din webbapp.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

`sasTokens`-grenen av lagringsplatsen uppdaterar filen `StorageHelper.cs`. Den ersätter aktiviteten `GetThumbNailUrls` med kodexemplet nedan. Den uppdaterade aktiviteten hämtar URL:erna för miniatyrbilderna genom att ange en [SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet) för starttid, förfallotid och behörigheter för SAS-token. När webbappen har distribuerats hämtas nu miniatyrbilderna med en URL med hjälp av en SAS-token. Den uppdaterade aktiviteten visas i följande exempel:
    
```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the container
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ThumbnailContainer);

    BlobContinuationToken continuationToken = null;

    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);

        foreach (var blobItem in resultSegment.Results)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            //Set the expiry time and permissions for the blob.
            //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
            //The shared access signature will be valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

            sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);

            sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);

            sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

            //Generate the shared access signature on the blob, setting the constraints directly on the signature.
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(blob.Uri + sasBlobToken);

        }

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }

    while (continuationToken != null);

    return await Task.FromResult(thumbnailUrls);
}
```

Följande klasser, egenskaper och metoder används i den föregående aktiviteten:

|Klass  |Egenskaper| Metoder  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)        |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     | |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)     | |[SetPermissionsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.setpermissionsasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_SetPermissionsAsync_Microsoft_WindowsAzure_Storage_Blob_BlobContainerPermissions_) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmentedAsync_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_)       |
|[BlobContinuationToken](/dotnet/api/microsoft.windowsazure.storage.blob.blobcontinuationtoken?view=azure-dotnet)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment?view=azure-dotnet)    | [Results](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment.results?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobResultSegment_Results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_GetSharedAccessSignature_Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet)     | [SharedAccessStartTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessStartTime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessExpiryTime)<br>[Behörigheter](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.permissions?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_Permissions) |        |

## <a name="server-side-encryption"></a>Kryptering på serversidan

[Azure Storage Service Encryption (SSE)](../common/storage-service-encryption.md) hjälper dig att skydda dina data. SSE krypterar vilande data och hanterar kryptering, dekryptering och nycklar. Alla data krypteras med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), ett av de starkaste blockchiffer som finns.

SSE krypterar automatiskt data på alla prestandanivåer (Standard och Premium), alla distributionsmodeller (Azure Resource Manager och klassisk) och alla Azure Storage-tjänster (blob, kö, tabell och fil). 

## <a name="enable-https-only"></a>Aktivera endast HTTPS

Du kan se till att begäranden om data till och från ett lagringskonto skyddas genom att begäranden begränsas till endast HTTPS. Uppdatera protokollet som krävs för lagringskontot med kommandot [az storage account update](/cli/azure/storage/account#az_storage_account_update).

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Testa anslutningen med hjälp av `curl` med protokollet `HTTP`.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Nu när säker överföring krävs får du följande meddelande:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Nästa steg

I den tredje delen i serien fick du lära dig hur du skyddar åtkomsten till lagringskontot, till exempel hur du:

> [!div class="checklist"]
> * Använder SAS-token för att komma åt miniatyrbilder
> * Aktiverar kryptering på serversidan
> * Aktiverar endast HTTPS-transport

Gå vidare till den fjärde delen i serien där du får lära dig hur du övervakar och felsöker ett molnlagringsprogram.

> [!div class="nextstepaction"]
> [Övervaka och felsöka molnlagringsprogram](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json