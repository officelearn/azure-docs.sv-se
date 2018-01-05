---
title: "Säker åtkomst till data för ett program i molnet med Azure Storage | Microsoft Docs"
description: "Använd SAS-token, kryptering och HTTPS för att skydda dina programdata i molnet"
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: c43165e230a00b6a4408637fd2290a21800d07b9
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="secure-access-to-an-applications-data-in-the-cloud"></a>Säker åtkomst till data i ett program i molnet

Den här kursen ingår tre i en serie. Du lär dig att skydda åtkomst till lagringskontot. 

I del tre av serien får du lära dig hur du:

> [!div class="checklist"]
> * Använd SAS-token till miniatyrbilder
> * Aktivera kryptering på serversidan
> * Aktivera endast HTTPS-transport

[Azure-blobblagring](../common/storage-introduction.md#blob-storage) innehåller en robust tjänst för att lagra filer för program. Den här självstudiekursen utökar [föregående] [ previous-tutorial] att visa hur få säker åtkomst till ditt lagringskonto från ett webbprogram. När du är klar avbildningar krypteras och webbprogrammet använder säker SAS-token för åtkomst till miniatyrbilder.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudiekursen du måste ha slutfört föregående lagring kursen: [automatisera storleksändring upp bilder med hjälp av händelse rutnätet][previous-tutorial]. 

## <a name="set-container-public-access"></a>Ange behållaren allmän åtkomst

I den här delen av kursen serien används SAS-token för att komma åt miniatyrerna. I det här steget kan du ange den offentliga åtkomsten till den _tummen_ behållare till `off`.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbs  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>Konfigurera SAS-token för miniatyrer

Webbprogrammet har visar avbildningar från en offentlig behållare för del i den här självstudiekursen serie. I den här delen av serien kan du använda [säker signatur åtkomst (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md#what-is-a-shared-access-signature) tokens för att hämta miniatyrbilder. SAS-token kan du ge begränsad åtkomst till en behållare eller blob baserat på IP, protokoll, tidsintervall eller rättigheter.

I det här exemplet kod källdatabasen använder den `sasTokens` filial som har en uppdaterad kodexempel. Ta bort den befintliga GitHub-distributionen med den [az webapp distribution källa delete](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_delete). Konfigurera sedan GitHub distribution till webbprogram med den [az webapp källa distributionskonfiguration](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) kommando.  

I följande kommando, `<web-app>` är namnet på ditt webbprogram.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

Den `sasTokens` gren av databasen uppdateringar av `StorageHelper.cs` fil. Ersätter den `GetThumbNailUrls` aktivitet med exemplet nedan. Uppdaterade aktiviteten hämtar miniatyren URL: er genom att ange en [SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet) Ange starttid, förfallotiden och behörigheter för SAS-token. När webbappen har distribuerats hämtar nu miniatyrbilderna med en URL med en SAS-token. Uppdaterade aktiviteten visas i följande exempel:
    
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

    // Set the permission of the container to public
    await container.SetPermissionsAsync(new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

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

Följande klasser, egenskaper och metoder som används i den föregående aktiviteten:

|Klass  |Egenskaper| Metoder  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)        |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     | |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)     | |[SetPermissionsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.setpermissionsasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_SetPermissionsAsync_Microsoft_WindowsAzure_Storage_Blob_BlobContainerPermissions_) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmentedAsync_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_)       |
|[BlobContinuationToken](/dotnet/api/microsoft.windowsazure.storage.blob.blobcontinuationtoken?view=azure-dotnet)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment?view=azure-dotnet)    | [Resultat](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment.results?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobResultSegment_Results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_GetSharedAccessSignature_Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet)     | [SharedAccessStartTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessStartTime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessExpiryTime)<br>[Behörigheter](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.permissions?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_Permissions) |        |

## <a name="server-side-encryption"></a>Kryptering på serversidan

[Azure Storage Service kryptering (SSE)](../common/storage-service-encryption.md) hjälper dig att skydda och skydda dina data. SSE krypterar data i vila, hantering av kryptering, dekryptering och hantering av nycklar. Krypteras alla data med hjälp av 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste blocket chiffer tillgängliga.

I följande exempel aktiverar kryptering för blobbar. Befintlig blob som skapats innan du aktiverar kryptering krypteras inte. Den `x-ms-server-encrypted` sidhuvud på en begäran om en blob visar krypteringsstatus för blob.

```azurecli-interactive
az storage account update --encryption-services blob --name <storage-account-name> --resource-group myResourceGroup
```

Ladda upp en ny avbildning till webbprogrammet nu när kryptering är aktiverat.

Med hjälp av `curl` med växeln `-I` ersätta egna värden för att hämta endast rubriker `<storage-account-name>`, `<container>`, och `<blob-name>`.  

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <storage-account-name> \
    --account-key <storage-account-key> \
    --container-name <container> \
    --name <blob-name> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d` \
    --output tsv)

curl https://<storage-account-name>.blob.core.windows.net/<container>/<blob-name>?$sasToken -I
```

Observera i svaret, den `x-ms-server-encrypted` huvud visar `true`. Det här huvudet identifierar att informationen är krypterad med SSE nu.

```
HTTP/1.1 200 OK
Content-Length: 209489
Content-Type: image/png
Last-Modified: Mon, 11 Sep 2017 19:27:42 GMT
Accept-Ranges: bytes
ETag: "0x8D4F94B2BE76D45"
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 57047db3-001e-0050-3e34-2ba769000000
x-ms-version: 2017-04-17
x-ms-lease-status: unlocked
x-ms-lease-state: available
x-ms-blob-type: BlockBlob
x-ms-server-encrypted: true
Date: Mon, 11 Sep 2017 19:27:46 GMT
```

## <a name="enable-https-only"></a>Aktivera endast HTTPS

Du kan begränsa endast begäranden till HTTPS för att säkerställa att begäranden om data till och från ett lagringskonto är säkra. Uppdatera protokollet storage-konto krävs med hjälp av den [az lagring uppdatering](/cli/azure/storage/account#az_storage_account_update) kommando.

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Testa en anslutning med hjälp av `curl` med hjälp av den `HTTP` protokoll.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Nu när säker överföring krävs, visas följande meddelande:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Nästa steg

Del tre seriens berättat hur du skyddar åtkomsten till lagringskontot, till exempel att:

> [!div class="checklist"]
> * Använd SAS-token till miniatyrbilder
> * Aktivera kryptering på serversidan
> * Aktivera endast HTTPS-transport

Gå vidare till steg fyra i serien att lära dig att övervaka och felsöka ett moln storage-program.

> [!div class="nextstepaction"]
> [Övervaka och felsöka program molnlagring program](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json