---
title: "Azure Quickstart - objekt för överföring till/från Azure Blob storage med hjälp av Node.js| Microsoft Docs"
description: "Lär dig snabbt att överföra objekt till och från Azure Blob storage med hjälp av Node.js"
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
ms.openlocfilehash: 4c3c4ec341a0e5f4f0e7415128479f6448f7db6b
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-nodejs"></a>Överför objekt till/från Azure Blob storage med hjälp av Node.js

Lär dig hur du använder Node.js att överföra, hämta och visa en lista med blockblobbar i en behållare i Azure Blob storage i denna Snabbstart.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten behöver du:

* Installera [Node.js](https://nodejs.org/en/)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

Den [exempelprogram](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) används i den här snabbstarten är ett grundläggande konsolprogram. 

Använd [git](https://git-scm.com/) att hämta en kopia av programmet till din utvecklingsmiljö.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Det här kommandot klonar databasen till din lokala git-mapp. Öppna den för att öppna programmet leta efter mappen storage-BLOB-nod-Snabbstart, och dubbelklicka på index.js.

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

Du måste ange anslutningssträngen för ditt lagringskonto i programmet. Öppna den `index.js` filen, söka efter den `connectionString` variabeln. Ersätt värdet med hela värdet för anslutningssträngen med den som du sparade från Azure-portalen. Anslutningssträngen för lagring bör se ut ungefär så här:

```node
// Create a blob client for interacting with the blob service from connection string
// How to create a storage connection string - http://msdn.microsoft.com/library/azure/ee758697.aspx
var connectionString = '<Your connection string here>';
var blobService = storage.createBlobService(connectionString);
```

## <a name="install-required-packages"></a>Installera nödvändiga paket

I programkatalogen kör `npm install` krävs för att installera någon paket som anges i den `package.json` filen.

```node
npm install
```

## <a name="run-the-sample"></a>Köra exemplet

Det här exemplet skapar en testfil i Mina dokument, överför det till Blob storage, visar en lista över blobbar i behållaren och laddar ned filen med ett nytt namn så att du kan jämföra de gamla och nya filerna.

Köra exemplet genom att skriva `node index.js`. Följande utdata är från ett Windows-system.  En liknande utdata med lämpliga sökvägar kan förväntas om med hjälp av Linux.

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

Kontrollera Mina dokument för de två filerna innan du fortsätter. Du kan öppna dem och de är identiska.

Du kan också använda ett verktyg som den [Azure Lagringsutforskaren](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) att visa filer i Blob storage. Azure Lagringsutforskaren är ett kostnadsfritt plattformsoberoende verktyg som hjälper dig att komma åt kontoinformationen lagring.

När du har kontrollerat filerna, trycka på valfri tangent för att avsluta demonstrationen och ta bort testfilerna. Nu när du vet vad exemplet gör du öppna filen index.js titta på koden. 

## <a name="get-references-to-the-storage-objects"></a>Hämta referenser till lagringsobjekt

Det första du gör är att skapa en referens till den `BlobService` används för att komma åt och hantera Blob storage. De här objekten som bygger på varandra – varje används av den nästa i listan.

* Skapa en instans av den  **[BlobService](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService__ctor)**  -objektet, vilket leder till Blob-tjänsten på ditt lagringskonto.

* Skapa en ny behållare och ange behörigheter för behållaren så att blobbarna är offentliga och kan nås med bara en URL. Behållaren som börjar med **quickstartcontainer -**.

Det här exemplet används [createContainerCreateIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) eftersom vi vill skapa en ny behållare varje gång exemplet körs. I en produktionsmiljö där du använder samma behållare i ett program, är det bättre att man bara anropa CreateIfNotExists en gång. Du kan också skapa behållaren i förväg så du inte behöver skapa i koden.

```node
// Create a container for organizing blobs within the storage account.
console.log('1. Creating a Container with Public Access:', blockBlobContainerName, '\n');
blobService.createContainerIfNotExists(blockBlobContainerName, { 'publicAccessLevel': 'blob' }, function (error) {
    if (error) return callback(error);
```

## <a name="upload-blobs-to-the-container"></a>Ladda upp blobbar i behållaren

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. Blockblobbar är de vanligaste. De är perfekt för lagring av text och binära data, vilket är anledningen till att de används i denna Snabbstart.

Om du vill överföra en fil till en blobb måste du använda den [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile) metod. Den här åtgärden skapas blobben om den inte redan finns eller skriver över den om den redan finns.

Exempelkoden skapar en lokal fil som ska användas för överföring och hämtning, spara filen ska överföras som **localPath** och namnet på blob i **localFileToUpload**. I följande exempel överför filen till den behållare som börjar med **quickstartcontainer -**.

```node
console.log('2. Creating a file in ~/Documents folder to test the upload and download\n');
console.log('   Local File:', LOCAL_FILE_PATH, '\n');
fs.writeFileSync(LOCAL_FILE_PATH, 'Greetings from Microsoft!');

console.log('3. Uploading BlockBlob:', BLOCK_BLOB_NAME, '\n');
blobService.createBlockBlobFromLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, LOCAL_FILE_PATH, function (error) {
handleError(error);
console.log('   Uploaded Blob URL:', blobService.getUrl(CONTAINER_NAME, BLOCK_BLOB_NAME), '\n');
```

Det finns flera överför metoder som du kan använda med Blob storage. Till exempel om du har en dataström med minne, du kan använda den [createBlockBlobFromStream](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromStream) metod i stället [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile).

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare

Därefter programmet hämtar en lista över filer i behållare med [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). Följande kod hämtar listan över blobbar och loop genom dem, visar URI: er för de blobbar som påträffats. Du kan kopiera URI kommandofönstret och klistra in den i en webbläsare för att visa filen.

Om du har 5 000 eller färre blobbar i behållaren, hämtas alla blob-namn i ett anrop till [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). Om du har mer än 5 000 blobbar i behållaren, hämtar tjänsten i listan i uppsättningar med 5 000 tills alla blob-namn har hämtats. Så första gången detta API anropas returnerar de första 5 000 blob-namn och en fortsättningstoken. Den andra gången du ange token, hämtar tjänsten nästa uppsättning blobbnamnen och förrän fortsättningstoken är null, vilket anger att alla blob-namn har hämtats.

```node
console.log('4. Listing blobs in container\n');
blobService.listBlobsSegmented(CONTAINER_NAME, null, function (error, data) {
    handleError(error);

    for (var i = 0; i < data.entries.length; i++) {
    console.log(util.format('   - %s (type: %s)'), data.entries[i].name, data.entries[i].blobType);
    }
    console.log('\n');
```

## <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned blobbar till din lokala disk med hjälp av [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_getBlobToLocalFile).

Följande kod hämtar blob som överförs i föregående avsnitt, att suffixet ”_DOWNLOADED” till blob-namn så att du kan se både filer på hårddisken. 

```node
console.log('5. Downloading blob\n');
blobService.getBlobToLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, DOWNLOADED_FILE_PATH, function (error) {
handleError(error);
console.log('   Downloaded File:', DOWNLOADED_FILE_PATH, '\n');
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du behöver inte längre blobbar på den här snabbstarten, kan du ta bort hela behållaren med hjälp av [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) och [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteContainerIfExists). Också ta bort de filer som skapas om de inte längre behövs. Detta har åtgärdat i programmet när du trycker på RETUR för att avsluta programmet.

```node
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

I Snabbstart, du har lärt dig hur du överför filer mellan en lokal disk och Azure Blob storage med hjälp av Node.js. Mer information om hur du arbetar med Blob storage fortsätta till Blob storage anvisningar.

> [!div class="nextstepaction"]
> [Anvisningar för Blob Storage-åtgärder](storage-nodejs-how-to-use-blob-storage.md)

Mer information om Lagringsutforskaren och Blobbar finns [hantera Azure-blobblagringsresurser med Lagringsutforskaren](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
