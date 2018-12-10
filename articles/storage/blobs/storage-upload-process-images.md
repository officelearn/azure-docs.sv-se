---
title: Överföra avbildningsdata i molnet med Azure Storage | Microsoft Docs
description: Använda Azure Blob Storage med ett webbprogram för att lagra appdata
services: storage
documentationcenter: ''
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 371a679978c501c71da6e8360d7541c31a6a720f
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682341"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Självstudie: Ladda upp bilddata i molnet med Azure Storage

Den här självstudien ingår i en serie. I den här självstudien lär du dig att distribuera en webbapp som använder Azure Storage-klientbiblioteket för att ladda upp bilder till ett lagringskonto. När du är klar har du en webbapp som lagrar och visar bilder från Azure Storage.

# <a name="nettabnet"></a>[\..NET](#tab/net)
![Vy för avbildningscontainer](media/storage-upload-process-images/figure2.png)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
![Vy för avbildningscontainer](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

I del ett i den här serien lärde du dig att:

> [!div class="checklist"]
> * skapar ett lagringskonto
> * Skapar en container och anger behörigheter
> * Hämta en åtkomstnyckel
> * Distribuera en webbapp till Azure
> * Konfigurera appinställningar
> * Interagera med webbappen

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen. Skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

För att du ska kunna installera och använda CLI lokalt behöver du för den här självstudien köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Skapa en resursgrupp 

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.  

I följande exempel skapas en resursgrupp med namnet `myResourceGroup`.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia 
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Exemplet överför avbildningar till en blobcontainer på ett Azure-lagringskonto. Ett Azure-lagringskonto tillhandahåller en unik namnrymd där du kan lagra och få åtkomst till dina Azure-lagringdataobjekt. Skapa ett lagringskonto i resursgruppen du skapade med hjälp av kommandot [az storage account create](/cli/azure/storage/account#az_storage_account_create).

> [!IMPORTANT]
> I del 2 i självstudien använder du Azure Event Grid med Blob Storage. Se till att skapa ditt lagringskonto i en Azure-region som har stöd för Event Grid. En lista över regioner som stöds finns i [Azure-produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

I följande kommando ersätter du ditt globalt unika namn för det Blob Storage-konto där platshållaren `<blob_storage_account>` visas.  

```azurecli-interactive
az storage account create --name <blob_storage_account> \
--location southeastasia --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
```

## <a name="create-blob-storage-containers"></a>Skapa Blob Storage-containrar

Appen använder två containrar i bloblagringskontot. Containrar liknar mappar och lagrar blobar. Det är till containern *avbildningar* som appen överför högupplösta bilder. I en senare del av självstudierna överför en funktionsapp i Azure ändrade miniatyrbilder till containern för *miniatyrer*.

Hämta nyckeln till lagringskontot med kommandot [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list). Sedan använder du den här nyckeln för att skapa två containrar med kommandot [az storage container create](/cli/azure/storage/container#az_storage_container_create).  

I det här fallet är `<blob_storage_account>` namnet på det blobblagringskonto som du skapade. Offentlig åtkomst för containern *avbildningar* har angetts till `off`. Offentlig åtkomst för containern *miniatyrer* har angetts till `container`. Inställningen för offentlig åtkomst för `container` tillåter att användare som besöker webbplatsen visar miniatyrbilderna.

```azurecli-interactive
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv)

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off

az storage container create -n thumbnails --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your Blob storage account key..."
echo $blobStorageAccountKey
```

Anteckna namnet och nyckeln till ditt Blob Storage-konto. Exempelappen använder dessa inställningar för att ansluta till lagringskontot för att ladda upp bilderna. 

## <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

En [App Service-plan](../../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) anger plats, storlek och funktioner för webbservergruppen som är värd för din app.

Skapa en App Service-plan med kommandot [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create).

I följande exempel skapas en App Service-plan med namnet `myAppServicePlan` på prisnivån **Kostnadsfri**:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Skapa en webbapp

Webbappen tillhandahåller ett värdutrymme för exempelappens kod som distribueras på GitHub-exempellagringsplatsen. Skapa en [webbapp](../../app-service/app-service-web-overview.md) i `myAppServicePlan` App Service-planen med kommandot [az webapp create](/cli/azure/webapp#az_webapp_create).  

I följande kommando ersätter du `<web_app>` med ett unikt namn. Giltiga tecken är `a-z`, `0-9` och `-`. Om `<web_app>` inte är unikt får du ett felmeddelande om att _webbplatsen med det angivna namnet `<web_app>` redan finns._ Standardwebbadressen för webbappen är `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Distribuera exempelappen från GitHub-lagringsplatsen

# <a name="nettabnet"></a>[\..NET](#tab/net)

App Service har stöd för flera olika sätt att distribuera innehåll till en webbapp. I de här självstudierna distribuerar du webbappen från en [offentlig GitHub exempellagringsplats](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Konfigurera lokal Git-distribution till webbappen med kommandot [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config). Ersätt `<web_app>` med namnet på den webbapp som du skapade i föregående steg.

Exempelprojektet innehåller en [ASP.NET MVC](https://www.asp.net/mvc)-app. Appen accepterar en bild, sparar den till ett lagringskonto och visar bilder från en container med miniatyrer. Webbappen använder namnrymderna [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet) och [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) från Azure Storage-klientbiblioteket för att interagera med Azure Storage.

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
App Service har stöd för flera olika sätt att distribuera innehåll till en webbapp. I de här självstudierna distribuerar du webbappen från en [offentlig GitHub exempellagringsplats](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node). Konfigurera lokal Git-distribution till webbappen med kommandot [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config). Ersätt `<web_app>` med namnet på den webbapp som du skapade i föregående steg.

---

```azurecli-interactive
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

## <a name="configure-web-app-settings"></a>Konfigurera inställningarna för webbappen

Exempelwebbappen använder [Azure Storage-klientbiblioteket](/dotnet/api/overview/azure/storage?view=azure-dotnet) för att begära åtkomsttokens som används för att överföra avbildningar. Autentiseringsuppgifterna för lagringskonto som används av Storage SDK ställs in i webbappens programinställningar. Lägg till appinställningar till den distribuerade appen med kommandot [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set).

I följande kommando ersätter du `<blob_storage_account>` med namnet på ditt Blob Storage-konto och `<blob_storage_key>` med den associerade nyckeln. Ersätt `<web_app>` med namnet på den webbapp som du skapade i föregående steg.

```azurecli-interactive
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=<blob_storage_key>  
```

När du har distribuerat och konfigurerat webbappen kan du testa funktionen för bilduppladdning i appen.

## <a name="upload-an-image"></a>Ladda upp en avbildning

Om du vill testa webbappen bläddrar du till URL-adressen till din publicerade app. Standardwebbadressen för webbappen är `https://<web_app>.azurewebsites.net`.
Välj regionen för **Ladda upp foton** och välj och ladda upp en fil eller dra en fil till regionen. Bilden försvinner om överföringen lyckas.

# <a name="nettabnet"></a>[\..NET](#tab/net)

![ImageResizer-app](media/storage-upload-process-images/figure1.png)

I exempelkoden används uppgiften `UploadFiletoStorage` i filen *Storagehelper.cs* för att ladda upp bilderna till containern *bilder* på lagringskontot med hjälp av metoden [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet). Följande kodexempel innehåller aktiviteten `UploadFiletoStorage`.

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

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

![App för bilduppladdning](media/storage-upload-process-images/upload-app-nodejs.png)

I exempelkoden ansvarar vägen `post` för att ladda upp bilden till en blob-container. Flödet använder moduler för att bearbeta uppladdningen:

- [multer](https://github.com/expressjs/multer) implementerar uppladdningsstrategin för routningshanteraren.
- [into-stream](https://github.com/sindresorhus/into-stream) konverterar bufferten till en dataström som krävs av [createBlockBlobFromStream].(http://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html#createBlockBlobFromStream)

När filen skickas till vägen förblir filens innehåll i minnet tills filen har laddats upp till blob-containern.

> [!IMPORTANT]
> Om stora filer läses in i minnet kan det ha en negativ inverkan på webbappens prestanda. Om du förväntar dig att användare publicerar stora filer kan du överväga att mellanlagra filer i webbserverns filsystem och sedan schemalägga uppladdningar till blob-lagringen. När filerna finns i blob-lagringen kan du ta bort dem från serverfilsystemet.

```javascript
const
      express = require('express')
    , router = express.Router()

    , multer = require('multer')
    , inMemoryStorage = multer.memoryStorage()
    , uploadStrategy = multer({ storage: inMemoryStorage }).single('image')

    , azureStorage = require('azure-storage')
    , blobService = azureStorage.createBlobService()

    , getStream = require('into-stream')
    , containerName = 'images'
;

const handleError = (err, res) => {
    res.status(500);
    res.render('error', { error: err });
};

const getBlobName = originalName => {
    const identifier = Math.random().toString().replace(/0\./, ''); // remove "0." from start of string
    return `${originalName}-${identifier}`;
};

router.post('/', uploadStrategy, (req, res) => {

    const
          blobName = getBlobName(req.file.originalname)
        , stream = getStream(req.file.buffer)
        , streamLength = req.file.buffer.length
    ;

    blobService.createBlockBlobFromStream(containerName, blobName, stream, streamLength, err => {

        if(err) {
            handleError(err);
            return;
        }

        res.render('success', { 
            message: 'File uploaded to Azure Blob storage.' 
        });
    });
});
```
---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Kontrollera att avbildningen visas på lagringskontot

Logga in på [Azure-portalen](https://portal.azure.com). I den vänstra menyn väljer du **Lagringskonton** och sedan namnet på ditt lagringskonto. Under **Blod Service** väljer du **Blobar** och sedan containern **bilder**.

Kontrollera att avbildningen visas i containern.

![Vy för avbildningscontainer](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Testa miniatyrvisning

Om du vill testa miniatyrvisning laddar du upp en bild till containern **miniatyrer** för att kontrollera huruvida appen kan läsa containern **miniatyrer**.

Logga in på [Azure-portalen](https://portal.azure.com). I den vänstra menyn väljer du **Lagringskonton** och sedan namnet på ditt lagringskonto. Under **Blod Service** väljer du **Blobar** och sedan containern **miniatyrer**. Välj **Överför** för att öppna fönstret **Överför blobb**.

Välj en fil med filväljaren och välj **Ladda upp**.

Gå tillbaka till din app för att kontrollera att avbildningen som har överförts till containern **Miniatyrer** syns.

# <a name="nettabnet"></a>[\..NET](#tab/net)
![Vy för avbildningscontainer](media/storage-upload-process-images/figure2.png)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
![Vy för avbildningscontainer](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

I del två i serien automatiserar du skapandet av miniatyrbilden så du inte behöver den här bilden. I containern **Miniatyrer** på Azure-portalen väljer du den avbildning du överförde och väljer **Ta bort** för att ta bort avbildningen. 

Du kan aktivera CDN för att cachelagra innehåll från ditt Azure Storage-konto. Mer information om hur du aktiverar CDN med ditt Azure Storage-konto finns i [Integrera ett Azure Storage-konto med Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Nästa steg

I del ett av serien lärde du dig att konfigurera en webbapp för att interagera med lagring.

Gå vidare till del två i serien och lär dig använda Event Grid för att utlösa en Azure-funktion för att ändra storlek på en bild.

> [!div class="nextstepaction"]
> [Använd Event Grid för att utlösa en Azure-funktion för att ändra storlek på en överförd avbildning](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
