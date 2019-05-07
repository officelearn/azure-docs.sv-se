---
title: Överföra avbildningsdata i molnet med Azure Storage | Microsoft Docs
description: Använda Azure Blob Storage med ett webbprogram för att lagra appdata
services: storage
author: normesta
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: normesta
ms.reviewer: seguler
ms.custom: mvc
ms.openlocfilehash: 242f3cb6cb9d957d13ecb133c4bfc59a01132ab1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142293"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Självstudie: Överföra avbildningsdata i molnet med Azure Storage

Den här självstudien ingår i en serie. I den här självstudien lär du dig att distribuera en webbapp som använder Azure Storage-klientbiblioteket för att ladda upp bilder till ett lagringskonto. När du är klar har du en webbapp som lagrar och visar bilder från Azure Storage.

# <a name="nettabdotnet"></a>[\..NET](#tab/dotnet)
![Vy för avbildningscontainer](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)
![Vy för avbildningscontainer](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)
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

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.  

I följande exempel skapas en resursgrupp med namnet `myResourceGroup`.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia 
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Exemplet överför avbildningar till en blobcontainer på ett Azure-lagringskonto. Ett Azure-lagringskonto tillhandahåller en unik namnrymd där du kan lagra och få åtkomst till dina Azure-lagringdataobjekt. Skapa ett lagringskonto i resursgruppen du skapade med hjälp av kommandot [az storage account create](/cli/azure/storage/account).

> [!IMPORTANT]
> I del 2 i självstudien använder du Azure Event Grid med Blob Storage. Se till att skapa ditt lagringskonto i en Azure-region som har stöd för Event Grid. En lista över regioner som stöds finns i [Azure-produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

I följande kommando ersätter du ditt globalt unika namn för det Blob Storage-konto där platshållaren `<blob_storage_account>` visas.  

```azurecli-interactive
blobStorageAccount=<blob_storage_account>

az storage account create --name $blobStorageAccount \
--location southeastasia --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
```

## <a name="create-blob-storage-containers"></a>Skapa Blob Storage-containrar

Appen använder två containrar i bloblagringskontot. Containrar liknar mappar och lagrar blobar. Det är till containern *avbildningar* som appen överför högupplösta bilder. I en senare del av självstudierna överför en funktionsapp i Azure ändrade miniatyrbilder till containern för *miniatyrer*.

Hämta nyckeln till lagringskontot med kommandot [az storage account keys list](/cli/azure/storage/account/keys). Sedan använder du den här nyckeln för att skapa två containrar med kommandot [az storage container create](/cli/azure/storage/container).  

Offentlig åtkomst för containern *avbildningar* har angetts till `off`. Offentlig åtkomst för containern *miniatyrer* har angetts till `container`. Inställningen för offentlig åtkomst för `container` tillåter att användare som besöker webbplatsen visar miniatyrbilderna.

```azurecli-interactive
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

En [App Service-plan](../../app-service/overview-hosting-plans.md) anger plats, storlek och funktioner för webbservergruppen som är värd för din app.

Skapa en App Service-plan med kommandot [az appservice plan create](/cli/azure/appservice/plan).

I följande exempel skapas en App Service-plan med namnet `myAppServicePlan` på prisnivån **Kostnadsfri**:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Skapa en webbapp

Webbappen tillhandahåller ett värdutrymme för exempelappens kod som distribueras på GitHub-exempellagringsplatsen. Skapa en [webbapp](../../app-service/overview.md) i `myAppServicePlan` App Service-planen med kommandot [az webapp create](/cli/azure/webapp).  

I följande kommando ersätter du `<web_app>` med ett unikt namn. Giltiga tecken är `a-z`, `0-9` och `-`. Om `<web_app>` är inte unikt får du felmeddelandet: Det finns redan en _webbplats med det angivna namnet `<web_app>`._ Standardwebbadressen för webbappen är `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
webapp=<web_app>

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Distribuera exempelappen från GitHub-lagringsplatsen

# <a name="nettabdotnet"></a>[\..NET](#tab/dotnet)

App Service har stöd för flera olika sätt att distribuera innehåll till en webbapp. I de här självstudierna distribuerar du webbappen från en [offentlig GitHub exempellagringsplats](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Konfigurera lokal Git-distribution till webbappen med kommandot [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source).

Exempelprojektet innehåller en [ASP.NET MVC](https://www.asp.net/mvc)-app. Appen accepterar en bild, sparar den till ett lagringskonto och visar bilder från en container med miniatyrer. Webbappen använder namnrymderna [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet) och [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) från Azure Storage-klientbiblioteket för att interagera med Azure Storage.

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)
App Service har stöd för flera olika sätt att distribuera innehåll till en webbapp. I de här självstudierna distribuerar du webbappen från en [offentlig GitHub exempellagringsplats](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node). Konfigurera lokal Git-distribution till webbappen med kommandot [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source). 

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)
App Service har stöd för flera olika sätt att distribuera innehåll till en webbapp. I de här självstudierna distribuerar du webbappen från en [offentlig GitHub exempellagringsplats](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10). Konfigurera lokal Git-distribution till webbappen med kommandot [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source). 

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10
```

---

## <a name="configure-web-app-settings"></a>Konfigurera inställningarna för webbappen

# <a name="nettabdotnet"></a>[\..NET](#tab/dotnet)

Exempelwebbappen använder [Azure Storage-klientbiblioteket](/dotnet/api/overview/azure/storage?view=azure-dotnet) för att begära åtkomsttokens som används för att överföra avbildningar. Autentiseringsuppgifterna för lagringskonto som används av Storage SDK ställs in i webbappens programinställningar. Lägg till appinställningar till den distribuerade appen med kommandot [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=$blobStorageAccount \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=$blobStorageAccountKey  
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

Exempelwebbappen använder [Azure Storage-klientbiblioteket](https://docs.microsoft.com/javascript/api/azure-storage) för att begära åtkomsttokens som används för att överföra avbildningar. Autentiseringsuppgifterna för lagringskonto som används av Storage SDK ställs in i webbappens programinställningar. Lägg till appinställningar till den distribuerade appen med kommandot [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountName=$blobStorageAccount \
AzureStorageConfig__AccountKey=$blobStorageAccountKey \
AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

Exempelwebbappen använder [Azure Storage-klientbiblioteket](https://github.com/Azure/azure-storage-js) för att begära åtkomsttokens som används för att överföra avbildningar. Autentiseringsuppgifterna för lagringskonto som används av Storage SDK ställs in i webbappens programinställningar. Lägg till appinställningar till den distribuerade appen med kommandot [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

När du har distribuerat och konfigurerat webbappen kan du testa funktionen för bilduppladdning i appen.

## <a name="upload-an-image"></a>Ladda upp en avbildning

Om du vill testa webbappen bläddrar du till URL-adressen till din publicerade app. Standardwebbadressen för webbappen är `https://<web_app>.azurewebsites.net`.

# <a name="nettabdotnet"></a>[\..NET](#tab/dotnet)

Välj regionen för **Ladda upp foton** och välj och ladda upp en fil eller dra en fil till regionen. Bilden försvinner om överföringen lyckas. Avsnittet **Genererade miniatyrer** förblir tomt tills vi testar det senare i det här ämnet.

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
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

Välj **Välj fil** för att välja en fil, och klicka sedan på **Ladda upp bild**. Avsnittet **Genererade miniatyrer** förblir tomt tills vi testar det senare i det här ämnet. 

![App för bilduppladdning](media/storage-upload-process-images/upload-app-nodejs.png)

I exempelkoden ansvarar vägen `post` för att ladda upp bilden till en blob-container. Flödet använder moduler för att bearbeta uppladdningen:

- [multer](https://github.com/expressjs/multer) implementerar uppladdningsstrategin för routningshanteraren.
- [into-stream](https://github.com/sindresorhus/into-stream) konverterar bufferten till en dataström som krävs av [createBlockBlobFromStream].(https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html)

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

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

Välj **Välj fil** för att välja en fil, och klicka sedan på **Ladda upp bild**. Avsnittet **Genererade miniatyrer** förblir tomt tills vi testar det senare i det här ämnet. 

![App för bilduppladdning](media/storage-upload-process-images/upload-app-nodejs.png)

I exempelkoden ansvarar vägen `post` för att ladda upp bilden till en blob-container. Flödet använder moduler för att bearbeta uppladdningen:

- [multer](https://github.com/expressjs/multer) implementerar uppladdningsstrategin för routningshanteraren.
- [into-stream](https://github.com/sindresorhus/into-stream) konverterar bufferten till en dataström som krävs av [createBlockBlobFromStream](https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html).

När filen skickas till vägen förblir filens innehåll i minnet tills filen har laddats upp till blob-containern.

> [!IMPORTANT]
> Om stora filer läses in i minnet kan det ha en negativ inverkan på webbappens prestanda. Om du förväntar dig att användare publicerar stora filer kan du överväga att mellanlagra filer i webbserverns filsystem och sedan schemalägga uppladdningar till blob-lagringen. När filerna finns i blob-lagringen kan du ta bort dem från serverfilsystemet.

```javascript
const {
  Aborter,
  BlobURL,
  BlockBlobURL,
  ContainerURL,
  ServiceURL,
  StorageURL,
  SharedKeyCredential,
  uploadStreamToBlockBlob
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };
const ONE_MINUTE = 60 * 1000;
const aborter = Aborter.timeout(30 * ONE_MINUTE);

const sharedKeyCredential = new SharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(sharedKeyCredential);
const serviceURL = new ServiceURL(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, ''); 
  return `${identifier}-${originalName}`;
};

router.post('/', uploadStrategy, async (req, res) => {

    const blobName = getBlobName(req.file.originalname);
    const stream = getStream(req.file.buffer);
    const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
    const blobURL = BlobURL.fromContainerURL(containerURL, blobName);
    const blockBlobURL = BlockBlobURL.fromBlobURL(blobURL);

    try {
      
      await uploadStreamToBlockBlob(aborter, stream,
        blockBlobURL, uploadOptions.bufferSize, uploadOptions.maxBuffers);

      res.render('success', { message: 'File uploaded to Azure Blob storage.' });   

    } catch (err) {

      res.render('error', { message: 'Something went wrong.' });

    }
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

# <a name="nettabdotnet"></a>[\..NET](#tab/dotnet)
![Vy för avbildningscontainer](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)
![Vy för avbildningscontainer](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)
![Vy för avbildningscontainer](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

I del två i serien automatiserar du skapandet av miniatyrbilden så du inte behöver den här bilden. I containern **Miniatyrer** på Azure-portalen väljer du den avbildning du överförde och väljer **Ta bort** för att ta bort avbildningen. 

Du kan aktivera CDN för att cachelagra innehåll från ditt Azure Storage-konto. Mer information om hur du aktiverar CDN med ditt Azure Storage-konto finns i [Integrera ett Azure Storage-konto med Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Nästa steg

I del ett av serien lärde du dig att konfigurera en webbapp för att interagera med lagring.

Gå vidare till del två i serien och lär dig använda Event Grid för att utlösa en Azure-funktion för att ändra storlek på en bild.

> [!div class="nextstepaction"]
> [Använd Event Grid för att utlösa en Azure-funktion för att ändra storlek på en överförd avbildning](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
