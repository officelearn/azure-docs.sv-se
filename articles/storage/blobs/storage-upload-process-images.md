---
title: "Överför avbildningen data i molnet med Azure Storage | Microsoft Docs"
description: "Använda Azure blob storage med ett webbprogram för att lagra programdata"
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
ms.openlocfilehash: 8d187e51cbb391ee1f34fb5934c8ae1868bb6244
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="upload-image-data-in-the-cloud-with-azure-storage"></a>Överför avbildningen data i molnet med Azure Storage

Den här kursen ingår i en serie. Den här kursen visar hur du distribuerar ett webbprogram som använder Azure Storage-klientbibliotek för att ladda upp bilder till ett lagringskonto. När du är klar har du ett webbprogram lagra och visa bilder från Azure storage.

![Vyn för bilder behållare](media/storage-upload-process-images/figure2.png)

I delen en av serierna kan du lära dig hur du:

> [!div class="checklist"]
> * skapar ett lagringskonto
> * Skapa en behållare och ange behörigheter
> * Hämta en snabbtangent
> * Konfigurera programinställningar
> * Distribuera en Webbapp till Azure
> * Interagera med webbprogrammet

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Skapa en resursgrupp 

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.
 
I följande exempel skapas en resursgrupp med namnet `myResourceGroup`.
 
```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus 
``` 

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
 
Exemplet överför bilder till en blob-behållare i ett Azure Storage-konto. Ett lagringskonto ger ett unikt namnområde för att lagra och komma åt dina Azure storage-dataobjekt. Skapa ett lagringskonto i resursgruppen du skapade med hjälp av kommandot [az storage account create](/cli/azure/storage/account#az_storage_account_create). 

> [!IMPORTANT] 
> I del 2 av kursen använder du prenumerationer på händelser för blob-lagring. Händelseprenumerationer är för närvarande stöds endast för Blob storage-konton i West centrala USA och västra USA 2. Du måste skapa ett Blob storage-konto som används av sample-appen för att lagra avbildningar och miniatyrer på grund av den här begränsningen.   

I följande kommando i stället använda egna globalt unikt namn för Blob storage-konto där du ser den `<blob_storage_account>` platshållare.  

```azurecli-interactive 
az storage account create --name <blob_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
``` 
 
## <a name="create-blob-storage-containers"></a>Skapa blob storage-behållare
 
Appen använder två behållare i Blob storage-konto. Behållare liknar mappar och används för lagring av BLOB. Den _bilder_ behållaren är där appen överför högupplösta bilder. I en senare del av serien appen Azure funktionen överför ändrade miniatyrbilder till den _tummen_ behållare. 

Hämta nyckel för storage-konto med hjälp av den [az nycklar lagringskontolistan](/cli/azure/storage/account/keys#list) kommando. Du använder den här för att skapa två behållare med hjälp av den [az lagringsbehållaren skapa](/cli/azure/storage/container#az_storage_container_create) kommando.  
 
I det här fallet `<blob_storage_account>` är namnet på Blob storage-konto som du skapade. Den _bilder_ behållare offentlig åtkomst har angetts till `off`, _tummen_ behållare offentlig åtkomst är inställd på `container`. Den `container` offentlig åtkomst inställningen miniatyrer ska visas för personer som finns på webbsidan.
 
```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off 

az storage container create -n thumbs --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your blob storage account key..." 
echo $blobStorageAccountKey 
``` 

Anteckna din blob storage-kontonamnet och nyckeln. Exempelappen använder dessa inställningar för att ansluta till lagringskontot för att ladda upp bilder. 

## <a name="create-an-app-service-plan"></a>Skapa en App Service-plan 

En [App Service-plan](../../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) anger plats, storlek och funktioner för webbservergruppen som är värd för din app. 

Skapa en App Service-plan med kommandot [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create). 

I följande exempel skapas en App Service-plan med namnet `myAppServicePlan` på prisnivån **Kostnadsfri**: 

```azurecli-interactive 
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE 
``` 

## <a name="create-a-web-app"></a>Skapa en webbapp 

Webbprogrammet utrymme värd för app exempelkoden som distribueras från till exempel GitHub-lagringsplatsen. Skapa en [webbapp](../../app-service/app-service-web-overview.md) i `myAppServicePlan` App Service-planen med kommandot [az webapp create](/cli/azure/webapp#az_webapp_create).  
 
I följande kommando ersätter `<web_app>` med ett unikt namn (giltiga tecken är `a-z`, `0-9`, och `-`). Om `<web_app>` är inte unikt du får ett felmeddelande: _webbplats med namnet `<web_app>` finns redan._ Standardwebbadressen för webbappen är `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive 
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan 
``` 

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Distribuera exempelapp från GitHub-lagringsplatsen 

Apptjänst har stöd för flera olika sätt att distribuera innehåll till ett webbprogram. I kursen får du distribuera webbprogrammet från en [offentliga GitHub exempel lagringsplats](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Konfigurera GitHub-distribution till webbprogrammet med den [az webapp källa distributionskonfiguration](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) kommando. Ersätt `<web_app>` med namnet på webbprogrammet som du skapade i föregående steg.

Exempelprojektet innehåller en [ASP.NET MVC](https://www.asp.net/mvc) app som accepterar en avbildning, sparar det till ett lagringskonto och visar bilder från en miniatyr behållare. Webbprogrammet använder den [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet), och [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) namnområden från Azure storage kan interagera med Azure storage-klientbiblioteket. 

```azurecli-interactive 
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

## <a name="configure-web-app-settings"></a>Konfigurera inställningarna för webbappen 

Exempel web app använder det [Azure Storage-klientbibliotek](/dotnet/api/overview/azure/storage?view=azure-dotnet) att begära åtkomst-token som används för att ladda upp bilder. Autentiseringsuppgifter för lagringskonto används av Storage SDK: N är inställda i programinställningar för webbappen. Lägg till tillämpningsinställningar distribuerad app med den [az webapp konfigurationsuppsättning appsettings](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) kommando. 

I följande kommando, `<blob_storage_account>` är namnet på Blob storage-konto och `<blob_storage_key>` är nyckeln som är associerade. Ersätt `<web_app>` med namnet på webbprogrammet som du skapade i föregående steg.     

```azurecli-interactive 
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbs \
AzureStorageConfig__AccountKey=<blob_storage_key>  
``` 

När webbappen har distribuerats och konfigurerats, kan du testa funktionen överför avbildningen i appen.   

## <a name="upload-an-image"></a>Ladda upp en avbildning 

Om du vill testa webbprogrammet, bläddrar du till URL-Adressen till ditt publicerade program. Standardwebbadressen för webbappen är `https://<web_app>.azurewebsites.net`. Välj den **överföra foton** region välja och överföra en fil eller dra och släppa en fil på regionen. Bilden försvinner om laddats upp.

![ImageResizer app](media/storage-upload-process-images/figure1.png)

I koden, den `UploadFiletoStorage` uppgift i den `Storagehelper.cs` används för att ladda upp bilder till den `images` behållare i storage-konto med den [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) metod. Följande kodexempel innehåller den `UploadFiletoStorage` aktivitet. 

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

Följande klasser och metoder som används i den föregående aktiviteten:

|Klass  |Metod  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_GetBlockBlobReference_System_String_)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Kontrollera bilden visas i storage-konto

Logga in på [Azure Portal](https://portal.azure.com). I den vänstra menyn, Välj **lagringskonton**, välj sedan namnet på ditt lagringskonto. Under **översikt**, Välj den **bilder** behållare.

Verifiera bilden visas i behållaren.

![Vyn för bilder behållare](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Testa miniatyr visning

Om du vill testa Miniatyr Visa ska du överför en bild till behållaren miniatyr för att säkerställa att programmet kan läsa miniatyr behållaren.

Logga in på [Azure Portal](https://portal.azure.com). I den vänstra menyn, Välj **lagringskonton**, välj sedan namnet på ditt lagringskonto. Välj **behållare** under **Blob-tjänsten** och välj den **tummen** behållare. Välj **överför** att öppna den **överför blob** fönstret.

Välj en fil med filväljaren och välj **överför**.

Gå tillbaka till din app för att kontrollera att avbildningen har överförts till den **tummen** behållare är synligt.

![Vyn för bilder behållare](media/storage-upload-process-images/figure2.png)

I den **tummen** behållare i Azure portal, Välj den avbildning du upp och markera **ta bort** att ta bort bilden. Del två seriens automatiserar du skapandet av miniatyrbilder, så inte behövs den här testavbildningen.

CDN kan aktiveras till att cachelagra innehåll från Azure storage-konto. Medan beskrivs inte i den här självstudiekursen, information om hur du aktiverar CDN med ditt Azure storage-konto, så kan du besöka: [integrera Azure storage-konto med Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Nästa steg

I delen en av serierna du lärt dig om att konfigurera ett webbprogram som interagerar med lagring, till exempel hur du:

> [!div class="checklist"]
> * skapar ett lagringskonto
> * Skapa en behållare och ange behörigheter
> * Hämta en snabbtangent
> * Konfigurera programinställningar
> * Distribuera en Webbapp till Azure
> * Interagera med webbprogrammet

Gå till två tillhör serien mer information om hur du använder händelsen rutnätet för att utlösa en Azure-funktion för att ändra storlek på en bild.

> [!div class="nextstepaction"]
> [Använd händelsen rutnätet för att utlösa en Azure-funktion för att ändra storlek på en överförda bild](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
