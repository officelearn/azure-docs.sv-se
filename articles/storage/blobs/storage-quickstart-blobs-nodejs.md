---
title: Så här skapar du en blob i Azure Storage med hjälp av klientbiblioteket för Node.js v2
description: Skapa ett lagringskonto och en container i objektlagring (Blob). Sedan använda Azure Storage-klientbiblioteket för Node.js v2 och ladda upp en blob till Azure Storage, laddar ned en blob och listar blobarna i en behållare.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: tamram
ms.openlocfilehash: 182315c705360d254c3bf342cd9c64ffafa0c021
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750082"
---
# <a name="how-to-upload-download-and-list-blobs-using-the-client-library-for-nodejs-v2"></a>Hur du laddar upp, ladda ned och lista blobar med hjälp av klientbiblioteket för Node.js v2

I den här guiden att lära dig hur du använder klientbiblioteket för Node.js från v2 till ladda upp, ladda ned och lista blobar med Azure Blob storage.

> [!TIP]
> Den senaste versionen av Azure Storage-klientbiblioteket för Node.js är v10. Microsoft rekommenderar att du använder den senaste versionen av klientbiblioteket när det är möjligt. Kom igång med v10, se [snabbstarten: Ladda upp, hämta, lista och ta bort blobar med Azure Storage-klientbibliotek för JavaScript v10 (förhandsversion)](storage-quickstart-blobs-nodejs-v10.md).

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Skapa ett Azure storage-konto i den [Azure-portalen](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Hjälp med att skapa kontot finns i [Skapa ett lagringskonto](../common/storage-quickstart-create-account.md).

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

Den [exempelprogrammet](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) är ett enkelt Node.js-konsolprogram. Börja genom att klona lagringsplatsen till din dator med följande kommando:

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

När du vill öppna programmet letar du reda på mappen *storage-blobs-node-quickstart* och öppnar den i din favoritmiljö för kodredigering.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

Innan du kör programmet måste du ange anslutningssträngen för ditt lagringskonto. Exempellagringsplatsen innehåller en fil med namnet *.env.example*. Du kan byta namn på filen genom att ta bort filnamnstillägget *.example*, vilket resulterar i en fil med namnet *.env*. I filen *.env* lägger du till ditt anslutningssträngvärde efter nyckeln *AZURE_STORAGE_CONNECTION_STRING*.

## <a name="install-required-packages"></a>Installera de paket som krävs

I programkatalogen kör du *npm install* för att installera de nödvändiga paketen för programmet.

```bash
npm install
```

## <a name="run-the-sample"></a>Kör exemplet
Nu när beroendena har installerats kan du köra exemplet med följande kommando:

```bash
npm start
```

Utdata från skriptet ser ut ungefär så här:

```bash
Containers:
 - container-one
 - container-two
Container "demo" is created
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme.md
Blob downloaded blob content: "hello Blob SDK"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Om du använder ett nytt lagringskonto för det här exemplet kommer du kanske inte ser några behållarnamn som visas under etiketten ”*behållare*”.

## <a name="understanding-the-code"></a>Förstå koden
Det första uttrycket används för att läsa in värden i miljövariabler.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
```

Modulen *dotenv* läser in miljövariabler när appen körs lokalt för felsökning. Värden definieras i en fil med namnet *.env* och läses in i den aktuella körningskontexten. I en produktionskontext tillhandahåller serverkonfigurationen dessa värden och det är anledningen till att den här koden bara körs när skriptet inte körs i en ”produktionskontext”.

```javascript
const path = require('path');
const storage = require('azure-storage');
```

Syftet med modulerna är följande: 

Fil med namnet *.env* i den aktuella körningskontexten
- *path* krävs för att kunna fastställa den absoluta sökvägen för den fil som ska laddas upp till blobblagringen
- *Azure-storage* är den [Azure Storage-klientbiblioteket](https://docs.microsoft.com/javascript/api/azure-storage) -modulen för Node.js

Därefter initieras variabeln **blobService** som en ny instans av Azure Blob-tjänsten.

```javascript
const blobService = storage.createBlobService();
```

I följande implementering omsluts varje *blobService*-funktion i en *Promise*, som ger åtkomst till Javascript-funktionen *async* och operatorn *await*, vilket förenklar motringningar i [Azure Storage-API:n](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). När ett lyckat svar returneras för varje funktion, matchas aktuell promise med relevanta data, tillsammans med ett meddelande som är specifikt för åtgärden.

### <a name="list-containers"></a>Visa en lista med containrar

Funktionen *listContainers* anropar [listContainersSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) som returnerar samlingar med containrar i grupper.

```javascript
const listContainers = async () => {
    return new Promise((resolve, reject) => {
        blobService.listContainersSegmented(null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} containers`, containers: data.entries });
            }
        });
    });
};
```

Storleken på grupperna kan konfigureras via [ListContainersOptions](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.listcontaineroptions?view=azure-node-latest). Anrop till *listContainersSegmented* returnerar blobmetadata som en matris med [ContainerResult](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.containerresult?view=azure-node-latest)-instanser. Resultaten returneras i 5 000 inkrementella batchar (segment). Om det finns fler än 5 000 blobbar i en container kommer resultatet innehålla ett värde för *continuationToken*. Visa efterföljande segment från blobcontainern genom att skicka fortsättningstoken tillbaka till *listContainersSegment* som det andra argumentet.

### <a name="create-a-container"></a>Skapa en container

Funktionen *createContainer* anropar [createContainerIfNotExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) och anger rätt åtkomstnivå för blobben.

```javascript
const createContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

Den andra parametern (*alternativ*) för **createContainerIfNotExists** accepterar ett värde för [publicAccessLevel](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). Värdet *blob* för *publicAccessLevel* anger att specifika blobbdata exponeras offentligt. Den här inställningen är motsatt till *containerns* nivååtkomst, vilket ger möjlighet att visa innehållet i containern.

Användningen av **createContainerIfNotExists** innebär att programmet kör kommandot *createContainer* flera gånger utan att returnera fel när behållaren redan finns. I en produktionsmiljö kan du ofta bara anropa **createContainerIfNotExists** när samma behållare används i hela programmet. I dessa fall kan du skapa containern i förväg via portalen eller via Azure CLI.

### <a name="upload-text"></a>Ladda upp text

Funktionen *uploadString* anropar [createBlockBlobFromText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) för att skriva (eller skriva över) en godtycklig sträng till blobcontainern.

```javascript
const uploadString = async (containerName, blobName, text) => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromText(containerName, blobName, text, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Text "${text}" is written to blob storage` });
            }
        });
    });
};
```
### <a name="upload-a-local-file"></a>Ladda upp en lokal fil

Funktionen *uploadLocalFile* använder [createBlockBlobFromLocalFile](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromlocalfile-string--string--string--errororresult-blobresult--) för att ladda upp och skriva (eller skriva över) en fil från filsystemet till bloblagringen. 

```javascript
const uploadLocalFile = async (containerName, filePath) => {
    return new Promise((resolve, reject) => {
        const fullPath = path.resolve(filePath);
        const blobName = path.basename(filePath);
        blobService.createBlockBlobFromLocalFile(containerName, blobName, fullPath, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Local file "${filePath}" is uploaded` });
            }
        });
    });
};
```
Andra metoder som är tillgängliga för att ladda upp innehåll till blobbar är att arbeta med [text](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest-string--string--string---buffer--errororresult-blobresult--) och [dataströmmar](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromstream-string--string--stream-readable--number--errororresult-blobresult--). Om du vill kontrollera att filen har laddats upp till blobblagringen, kan du använda [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) till att visa datan på ditt konto.

### <a name="list-the-blobs"></a>Visa en lista över blobarna

Funktionen *listBlobs* anropar metoden [listBlobsSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#listblobssegmented-string--continuationtoken--errororresult-listblobsresult--) för att returnera en lista med blobmetadata i en container. 

```javascript
const listBlobs = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} blobs in '${containerName}'`, blobs: data.entries });
            }
        });
    });
};
```

Om *listBlobsSegmented* anropas returneras blobbmetadata som en matris med [BlobResult](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.blobresult?view=azure-node-latest)-instanser. Resultaten returneras i 5 000 inkrementella batchar (segment). Om det finns fler än 5 000 blobbar i en container kommer resultatet innehålla ett värde för **continuationToken**. Visa efterföljande segment från blobcontainern genom att skicka fortsättningstoken tillbaka till **listBlobSegmented** som det andra argumentet.

### <a name="download-a-blob"></a>Ladda ned en blob

Funktionen *downloadBlob* använder [getBlobToText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) för att hämta innehållet i bloben till den angivna absoluta sökvägen.

```javascript
const downloadBlob = async (containerName, blobName) => {
    const dowloadFilePath = path.resolve('./' + blobName.replace('.txt', '.downloaded.txt'));
    return new Promise((resolve, reject) => {
        blobService.getBlobToText(containerName, blobName, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Blob downloaded "${data}"`, text: data });
            }
        });
    });
};
```
Implementeringen som visas här gör att källan returnerar innehållet i bloben som en sträng. Du kan också ladda ned bloben som en [dataström](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) eller direkt till en [lokal fil](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest).

### <a name="delete-a-blob"></a>Ta bort en blob

Funktionen *deleteBlob* anropar funktionen [deleteBlobIfExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#deleteblobifexists-string--string--errororresult-boolean--). Som namnet antyder returnerar den här funktionen inte ett fel om bloben redan har tagits bort.

```javascript
const deleteBlob = async (containerName, blobName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="delete-a-container"></a>Ta bort en container

Du tar bort containrar genom att anropa metoden *deleteContainer* från blobtjänsten och skicka containernamnet.

```javascript
const deleteContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteContainer(containerName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' deleted` });
            }
        });
    });
};
```

### <a name="calling-code"></a>Anropa kod

För att ge stöd för *async/await*-syntaxen i JavaScript innesluts anropskoden i en funktion med namnet *execute*. Därefter anropas execute och hanteras som ett löfte.

```javascript
async function execute() {
    // commands 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```
All följande kod körs inuti execute-funktionen där kommentaren `// commands` har lagts till.

Först deklareras de relevanta variablerna för att tilldela namn och exempelinnehåll och för att peka på den lokala filen som ska laddas upp till Blob Storage.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello Node SDK";
const localFilePath = "./readme.md";
let response;
```

För att visa en lista över containrarna i lagringskontot anropas funktionen listContainers och listan med containrar som returneras loggas i utdatafönstret.

```javascript
console.log("Containers:");
response = await listContainers();
response.containers.forEach((container) => console.log(` -  ${container.name}`));
```

När containerlistan är tillgänglig kan du använda matrismetoden *findIndex* för att se om den container som du vill skapa redan finns. Om containern inte finns skapas den.

```javascript
const containerDoesNotExist = response.containers.findIndex((container) => container.name === containerName) === -1;

if (containerDoesNotExist) {
    await createContainer(containerName);
    console.log(`Container "${containerName}" is created`);
}
```
Därefter laddas en sträng och en lokal fil upp till Blob Storage.

```javascript
await uploadString(containerName, blobName, content);
console.log(`Blob "${blobName}" is uploaded`);

response = await uploadLocalFile(containerName, localFilePath);
console.log(response.message);
```
Du visar en lista över blobar på samma sätt som du visar en lista över containrar. Anropet till *listBlobs* returnerar en matris med blobar i containern och loggas i utdatafönstret.

```javascript
console.log(`Blobs in "${containerName}" container:`);
response = await listBlobs(containerName);
response.blobs.forEach((blob) => console.log(` - ${blob.name}`));
```

För att ladda ned en blob inhämtas svaret och används för att komma åt värdet för bloben. Från svaret konverteras readableStreamBody till en sträng och loggas till utdatafönstret.

```javascript
response = await downloadBlob(containerName, blobName);
console.log(`Downloaded blob content: "${response.text}"`);
```

Slutligen tas bloben och containern bort från lagringskontot.

```javascript
await deleteBlob(containerName, blobName);
console.log(`Blob "${blobName}" is deleted`);

await deleteContainer(containerName);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Rensa resurser
Alla data som skrivits till lagringskontot tas bort automatiskt i slutet av kodexemplet. 

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Resurser för utveckling av Node.js-program med blobbar

Se dessa ytterligare resurser för Node.js-utveckling med blobblagring:

### <a name="binaries-and-source-code"></a>Binärfiler och källkod

- Visa och installera [Node.js-klientbibliotekets källkod](https://github.com/Azure/azure-storage-node) för Azure Storage på GitHub.

### <a name="client-library-reference-and-samples"></a>Referens och exempel för klientbiblioteket

- Mer information om klientbiblioteket för Node.js finns i [API-referens för Node.js](https://docs.microsoft.com/javascript/api/overview/azure/storage).
- Utforska [Blobblagringsexempel](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob) som skrivits med Node.js-klientbiblioteket.

## <a name="next-steps"></a>Nästa steg

Den här artikeln visar hur du överför en fil mellan en lokal disk och Azure Blob storage med Node.js. Om du vill lära dig mer om hur du arbetar med Blob Storage fortsätter du till GitHub-lagringsplatsen.

> [!div class="nextstepaction"]
> [Microsoft Azure Storage SDK för Node.js och JavaScript för webbläsare](https://github.com/Azure/azure-storage-node)
