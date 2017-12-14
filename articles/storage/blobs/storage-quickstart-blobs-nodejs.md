---
title: "Snabbstart för Azure – Överföra objekt till och från Azure Blob Storage med hjälp av Node.js | Microsoft Docs"
description: "Lär dig snabbt hur du överför objekt till och från Azure Blob Storage med hjälp av Node.js"
services: storage
documentationcenter: storage
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/30/2017
ms.author: gwallace
ms.openlocfilehash: dd4d3abf082767c40760d020c0997b365452e769
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-nodejs"></a>Överföra objekt till och från Azure Blob Storage med hjälp av Node.js

I den här snabbstarten får du lära dig att använda Node.js för att ladda upp, ladda ned och lista blockblobar i Azure Blob Storage.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten behöver du:

* Installera [Node.js](https://nodejs.org/en/)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

[Exempelprogrammet](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) som används i den här snabbstarten är ett grundläggande konsolprogram. 

Använd [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Det här kommandot klonar lagret till den lokala git-mappen. När du vill öppna programmet letar du reda på mappen storage-blobs-node-quickstart, öppnar den och dubbelklickar på index.js.

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

Du måste ange anslutningssträngen för ditt lagringskonto i programmet. Öppna filen `index.js` och sök efter variabeln `connectionString`. Ersätt värdet med hela anslutningssträngens värde med det du sparade från Azure-portalen. Anslutningssträngen för lagring bör se ut ungefär så här:

```javascript
// Create a blob client for interacting with the blob service from connection string
// How to create a storage connection string - http://msdn.microsoft.com/library/azure/ee758697.aspx
var connectionString = '<Your connection string here>';
var blobService = storage.createBlobService(connectionString);
```

## <a name="install-required-packages"></a>Installera de paket som krävs

I programkatalogen kör du `npm install` för att installera eventuella paket som krävs som anges i filen `package.json`.

```javascript
npm install
```

## <a name="run-the-sample"></a>Kör exemplet

Det här exemplet skapar en testfil i Mina dokument, överför den till Blob Storage, listar blobarna i behållaren och laddar sedan ned filen med ett nytt namn så att du kan jämföra de gamla och nya filerna.

Kör exemplet genom att skriva `node index.js`. Följande utdata är från ett Windows-system.  Liknande utdata med lämpliga sökvägar kan förväntas om man använder Linux.

```
Azure Storage Node.js Client Library Blobs Quick Start

1. Creating a container with public access: quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d

2. Creating a file in ~/Documents folder to test the upload and download

   Local File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

3. Uploading BlockBlob: quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

   Uploaded Blob URL: https://mystorageaccount.blob.core.windows.net/quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d/quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

4. Listing blobs in container

   - quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt (type: BlockBlob)


5. Downloading blob

   Downloaded File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d_DOWNLOADED.txt

Sample finished running. When you hit <ENTER> key, the temporary files will be deleted and the sample application will exit.
```

Kontrollera att de två filerna finns i MyDocuments innan du fortsätter. Du kan öppna dem och se att de är identiska.

Du kan också använda ett verktyg som [Azure Storage Explorer](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för att visa filerna i Blob Storage. Azure Storage Explorer är ett kostnadsfritt verktyg för flera plattformar som gör det möjligt att komma åt information på lagringskontot.

När du har kontrollerat filerna trycker du på valfri tangent för att avsluta demonstrationen och ta bort testfilerna. Nu när du vet vad exemplet gör kan du öppna filen index.js och titta på koden. 

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Härnäst går vi igenom exempelkoden så att du kan förstå hur den fungerar.

### <a name="get-references-to-the-storage-objects"></a>Hämta referenser till lagringsobjekten

Det första du ska göra är att skapa referenser till `BlobService` som används för att komma åt och hantera Blob Storage. De här objekten bygger på varandra – vart och ett används av nästa i listan.

* Skapa en instans av objektet **[BlobService](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService__ctor)**, som pekar mot Blob Service i lagringskontot.

* Skapa en ny behållare och ange sedan behörigheter för behållaren så att blobarna är offentliga och går att komma åt med bara en URL. Behållaren börjar med **quickstartcontainer-**.

Det här exemplet använder [createContainerCreateIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) eftersom vi vill skapa en ny behållare varje gång exemplet körs. I en produktionsmiljö där du använder samma behållare i ett program är det bättre att bara anropa CreateIfNotExists en gång. Du kan också skapa behållaren i förväg så att du inte behöver skapa den i koden.

```javascript
// Create a container for organizing blobs within the storage account.
console.log('1. Creating a Container with Public Access:', blockBlobContainerName, '\n');
blobService.createContainerIfNotExists(blockBlobContainerName, { 'publicAccessLevel': 'blob' }, function (error) {
    if (error) return callback(error);
```

### <a name="upload-blobs-to-the-container"></a>Ladda upp blobar i behållaren

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. Blockblobar är de vanligaste. De är perfekta för att lagra text och binära data, och det är därför de används i den här snabbstarten.

Om du vill överföra en fil till en blob måste du använda metoden [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile). Den här åtgärden skapar bloben om den inte redan finns, eller skriver över den om den finns.

Exempelkoden skapar en lokal fil som ska användas för uppladdning och nedladdning, och lagrar filen som ska laddas upp som **localPath** och namnet på bloben i **localFileToUpload**. I följande exempel överförs filen till behållaren som börjar med **quickstartcontainer-**.

```javascript
console.log('2. Creating a file in ~/Documents folder to test the upload and download\n');
console.log('   Local File:', LOCAL_FILE_PATH, '\n');
fs.writeFileSync(LOCAL_FILE_PATH, 'Greetings from Microsoft!');

console.log('3. Uploading BlockBlob:', BLOCK_BLOB_NAME, '\n');
blobService.createBlockBlobFromLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, LOCAL_FILE_PATH, function (error) {
handleError(error);
console.log('   Uploaded Blob URL:', blobService.getUrl(CONTAINER_NAME, BLOCK_BLOB_NAME), '\n');
```

Det går att använda flera uppladdningsmetoder med Blob Storage. Om du till exempel har en minnesström kan du använda metoden [createBlockBlobFromStream](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromStream) i stället för [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile).

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare

Därefter hämtar programmet en lista över filer i behållaren med [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). Följande kod hämtar listan över blobar och går sedan igenom dem och visar URI för de blobar som har påträffats. Du kan kopiera URI från kommandofönstret och klistra in den i en webbläsare om du vill visa filen.

Om du har 5 000 eller färre blobar i behållaren hämtas alla blobnamn i ett anrop till [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). Om du har fler än 5 000 blobar i behållaren hämtar tjänsten listan i uppsättningar om 5 000 tills alla blobnamn har hämtats. Första gången denna API anropas returnerar den därför de första 5 000 blobnamnen och en fortsättningstoken. Den andra gången anger du token och tjänsten hämtar nästa uppsättning med blobnamn och så vidare tills fortsättningstoken är null, vilket visar att alla blobnamn har hämtats.

```javascript
console.log('4. Listing blobs in container\n');
blobService.listBlobsSegmented(CONTAINER_NAME, null, function (error, data) {
    handleError(error);

    for (var i = 0; i < data.entries.length; i++) {
    console.log(util.format('   - %s (type: %s)'), data.entries[i].name, data.entries[i].blobType);
    }
    console.log('\n');
```

### <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned blobar till din lokala disk med hjälp av [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_getBlobToLocalFile).

Följande kod laddar ned bloben som överfördes i föregående avsnitt och lägger till ett suffix med "_DOWNLOADED" i blobnamnet så att du kan se båda filerna på den lokala disken. 

```javascript
console.log('5. Downloading blob\n');
blobService.getBlobToLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, DOWNLOADED_FILE_PATH, function (error) {
handleError(error);
console.log('   Downloaded File:', DOWNLOADED_FILE_PATH, '\n');
```

### <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver blobarna som laddades upp i denna snabbstart kan du ta bort hela behållaren med hjälp av [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) och [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteContainerIfExists). Ta även bort de filer som skapades om de inte längre behövs. Detta sköts av programmet när du trycker på Retur för att avsluta programmet.

```javascript
console.log('6. Deleting block Blob\n');
    blobService.deleteBlobIfExists(CONTAINER_NAME, BLOCK_BLOB_NAME, function (error) {
        handleError(error);

    console.log('7. Deleting container\n');
    blobService.deleteContainerIfExists(CONTAINER_NAME, function (error) {
        handleError(error);
            
        fs.unlinkSync(LOCAL_FILE_PATH);
        fs.unlinkSync(DOWNLOADED_FILE_PATH);
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du överför filer mellan en lokal disk och Azure Blob Storage med Node.js. Om du vill veta mer om att arbeta med Blob Storage kan du fortsätta till anvisningarna om Blob Storage.

> [!div class="nextstepaction"]
> [Anvisningar för Blob Storage-åtgärder](storage-nodejs-how-to-use-blob-storage.md)

Mer information om Storage Explorer och blobar finns i [Hantera Azure Blob Storage-resurser med Storage Explorer ](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
