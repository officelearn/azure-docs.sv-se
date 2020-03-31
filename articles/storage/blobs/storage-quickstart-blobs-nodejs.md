---
title: 'Snabbstart: Azure Blob storage library v12 - JavaScript'
description: I den här snabbstarten får du lära dig hur du använder Azure Blob storage client library version 12 för JavaScript för att skapa en behållare och en blob i Blob(objekt) lagring. Du får lära dig hur du hämtar bloben till din lokala dator och hur du visar alla blobar i en container.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: e68e91d90846ab77b994b53be7a84a9dd8bc5a25
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241040"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>Snabbstart: Hantera blobbar med JavaScript v12 SDK i Node.js

I den här snabbstarten lär du dig hantera blobbar med nod.js. Blobbar är objekt som kan innehålla stora mängder text eller binära data, inklusive bilder, dokument, strömmande media och arkivdata. Du överför, hämtar och listblobar och skapar och tar bort behållare.

[API-referensdokumentation](/javascript/api/@azure/storage-blob) | [Bibliotekskodpaket](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [(Nod Package Manager)](https://www.npmjs.com/package/@azure/storage-blob) | [Exempel](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Ett Azure Storage-konto. [Skapa ett lagringskonto](../common/storage-account-create.md).
- [Nod.js](https://nodejs.org/en/download/).

> [!NOTE]
> Mer om du vill komma igång med den tidigare SDK-versionen finns i [Snabbstart: Hantera blobbar med JavaScript v10 SDK i Node.js](storage-quickstart-blobs-nodejs-legacy.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Inrätta

I det här avsnittet får du hjälp med att förbereda ett projekt för att arbeta med Azure Blob storage-klientbiblioteket v12 för JavaScript.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett JavaScript-program med namnet *blob-quickstart-v12*.

1. Skapa en ny katalog för projektet i ett konsolfönster (till exempel cmd, PowerShell eller Bash).

    ```console
    mkdir blob-quickstart-v12
    ```

1. Växla till den nyskapade *blob-quickstart-v12-katalogen.*

    ```console
    cd blob-quickstart-v12
    ```

1. Skapa en ny textfil som heter *package.json*. Den här filen definierar nod.js-projektet. Spara den här filen i katalogen *blob-quickstart-v12.* Här är innehållet i filen:

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```
    
    Du kan sätta ditt eget `author` namn i för fältet, om du vill.
   
### <a name="install-the-package"></a>Installera paketet

Medan du fortfarande är i katalogen *blob-quickstart-v12* installerar du Azure Blob storage-klientbiblioteket för JavaScript-paketet med `npm install` kommandot. Det här kommandot läser *filen package.json* och installerar Azure Blob storage client library v12 for JavaScript-paketet och alla bibliotek som det är beroende av.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Konfigurera ramverket för appen

Från projektkatalogen:

1. Öppna en ny textfil i kodredigeraren
1. Lägga `require` till anrop för att läsa in Azure- och Node.js-moduler
1. Skapa strukturen för programmet, inklusive grundläggande undantagshantering

    Här är koden:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Spara den nya filen som *blob-quickstart-v12.js* i katalogen *blob-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Blob-lagring är optimerad för lagring av stora mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss datamodell eller definition, till exempel text eller binära data. I blobblagringen finns tre typer av resurser:

* Lagringskontot
* En container på lagringskontot
* En blob i behållaren

Följande diagram visar relationen mellan de här resurserna.

![Diagram över blobblagringens arkitektur](./media/storage-blobs-introduction/blob1.png)

Använd följande JavaScript-klasser för att interagera med dessa resurser:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): `BlobServiceClient` Med klassen kan du manipulera Azure Storage-resurser och blob-behållare.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): `ContainerClient` Klassen låter dig manipulera Azure Storage-behållare och deras blobbar.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient): `BlobClient` Klassen låter dig manipulera Azure Storage blobbar.

## <a name="code-examples"></a>Kodexempel

I det här exemplet visar kodavsnitt hur du utför följande med Azure Blob storage-klientbiblioteket för JavaScript:

* [Hämta anslutningssträngen](#get-the-connection-string)
* [Skapa en behållare](#create-a-container)
* [Ladda upp blobbar till en behållare](#upload-blobs-to-a-container)
* [Visa en lista över blobarna i en container](#list-the-blobs-in-a-container)
* [Ladda ned blobbar](#download-blobs)
* [Ta bort en container](#delete-a-container)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Koden nedan hämtar anslutningssträngen för lagringskontot från den miljövariabel som skapas i avsnittet [Konfigurera lagringsanslutningssträngen.](#configure-your-storage-connection-string)

Lägg till den `main` här koden i funktionen:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-container"></a>Skapa en container

Bestäm ett namn för den nya behållaren. Koden nedan lägger till ett UUID-värde i behållarnamnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om namngivning av containrar och blobar finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Skapa en instans av klassen [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) genom att anropa metoden [fromConnectionString.](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) Anropa sedan [metoden getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) för att få en referens till en behållare. Slutligen [anropar du skapa](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) för att faktiskt skapa behållaren i ditt lagringskonto.

Lägg till den här `main` koden i slutet av funktionen:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>Ladda upp blobbar till en behållare

Följande kodavsnitt:

1. Skapar en textsträng som ska överföras till en blob.
1. Hämtar en referens till ett [BlockBlobClient-objekt](/javascript/api/@azure/storage-blob/blockblobclient) genom att anropa metoden [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) på [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) från avsnittet [Skapa en behållare.](#create-a-container)
1. Överför textsträngsdata till blobben genom att anropa [uppladdningsmetoden.](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-)

Lägg till den här `main` koden i slutet av funktionen:

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container

Lista blobbar i behållaren genom att anropa [metoden listBlobsFlat.](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) I det här fallet har bara en blob lagts till i behållaren, så listningsåtgärden returnerar bara den bloben.

Lägg till den här `main` koden i slutet av funktionen:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Ladda ned blobbar

Hämta den tidigare skapade blobben genom att anropa [hämtningsmetoden.](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) Exempelkoden innehåller en hjälpfunktion `streamToString`som kallas , som används för att läsa en nod.js läsbar ström i en sträng.

Lägg till den här `main` koden i slutet av funktionen:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Lägg till den *after* här `main` hjälpfunktionen efter funktionen:

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>Ta bort en container

Följande kod rensar de resurser som appen har skapat genom att ta bort hela behållaren med [borttagningsmetoden.](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) Du kan också ta bort de lokala filerna om du vill.

Lägg till den här `main` koden i slutet av funktionen:

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar en textsträng och överför den till Blob-lagring. I exemplet visas sedan blob/er i behållaren, hämtar blobben och visar de hämtade data.

Från en konsolfråga navigerar du till katalogen som innehåller `node` *blob-quickstart-v12.py-filen* och kör sedan följande kommando för att köra appen.

```console
node blob-quickstart-v12.js
```

Utdata för appen liknar följande exempel:

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Gå igenom koden i felsökaren och kontrollera din [Azure-portal](https://portal.azure.com) under hela processen. Kontrollera att behållaren skapas. Du kan öppna blobben i behållaren och visa innehållet.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du laddar upp, laddar ned och listar blobbar med JavaScript.

För självstudier, exempel, snabbstarter och annan dokumentation, besök:

> [!div class="nextstepaction"]
> [Azure för JavaScript-dokumentation](/azure/javascript/)

* Mer information finns i [Azure Blob storage client library för JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob).
* Om du vill visa exempelappar för Blob-lagring fortsätter du till [Azure Blob storage client library v12 JavaScript-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples).
