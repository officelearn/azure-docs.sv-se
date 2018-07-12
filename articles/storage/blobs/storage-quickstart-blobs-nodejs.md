---
title: Azure snabbstart – Skapa en blob i objektlagring med hjälp av Node.js | Microsoft Docs
description: I den här snabbstarten skapar du ett lagringskonto och en behållare i objektlagring (Blob). Sedan använder du lagringsklientbiblioteket för Node.js och laddar upp en blob till Azure Storage, laddar ned en blob och listar blobarna i en behållare.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: cshoe
ms.openlocfilehash: 07ac5195d9653affdbb0f305ac1697cecd379319
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38308142"
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>Snabbstart: Ladda upp, ladda ned och lista blobar med Node.js

I den här snabbstarten får du lära dig att använda Node.js till att ladda upp, ladda ned och lista blockblobbar i en behållare i Azure Blob Storage.

Du behöver en [Azure-prenumeration](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) för att kunna utföra den här snabbstarten.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

[Exempelprogrammet](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) som används i snabbstarten är ett enkelt Node.js- konsolprogram. Börja genom att klona lagringsplatsen till din dator med följande kommando:

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
Nu när beroenden är installerade kan du köra exemplet genom att skicka kommandon till skriptet. Om du vill skapa en blobbehållare kan du exempelvis köra följande kommando:

```bash
node index.js --command createContainer
```

Tillgängliga kommandon är:

| Kommando | Beskrivning |
|---------|---------|
|*createContainer* | Skapar en behållare med namnet *test-container* (lyckas även om det redan finns en behållare) |
|*upload*          | Laddar upp filen *exempel.txt* till behållaren *test-container* |
|*download*        | Laddar ner innehållet i blobben *example* till *example.downloaded.txt* |
|*ta bort*          | Tar bort blobben *example* |
|*list*            | Gör en lista med innehållet i behållaren *test-container* i konsolen |


## <a name="understanding-the-sample-code"></a>Förstå exempelkoden
Det här kodexemplet använder ett par moduler för att samverka med filsystemet och kommandoraden. 

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
const path = require('path');
const args = require('yargs').argv;
const storage = require('azure-storage');
```

Syftet med modulerna är följande: 

- *dotenv* läser in miljövariabler som definierats i en fil med namnet *.env* i den aktuella körningskontexten
- *path* krävs för att kunna fastställa den absoluta sökvägen för den fil som ska laddas upp till blobblagringen
- *yargs* innehåller ett enkelt gränssnitt för åtkomst till kommandoradsargumenten
- *azure-storage* är modulen [Azure Storage SDK](https://docs.microsoft.com/javascript/api/azure-storage) för Node.js

Därefter initieras en serie med variabler:

```javascript
const blobService = storage.createBlobService();
const containerName = 'test-container';
const sourceFilePath = path.resolve('./example.txt');
const blobName = path.basename(sourceFilePath, path.extname(sourceFilePath));
```

Variablerna är inställda på följande värden:

- *blobService* anges till en ny instans av Azure Blob Service
- *containerName* anges till namnet på behållaren
- *sourceFilePath* anges till den absoluta sökvägen till filen som ska laddas upp
- *blobName* skapas genom att använda namnet på filen och ta bort filnamnstillägget

I följande implementering omsluts varje *blobService*-funktion i en *Promise*, som ger åtkomst till Javascript-funktionen *async* och operatorn *await*, vilket förenklar motringningar i [Azure Storage-API:n](/nodejs/api/azure-storage/blobservice). När ett lyckat svar returneras för varje funktion, matchas aktuell promise med relevanta data, tillsammans med ett meddelande som är specifikt för åtgärden.

### <a name="create-a-blob-container"></a>Skapa en blobbehållare

Funktionen *createContainer* anropar [createContainerIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) och anger rätt åtkomstnivå för blobben.

```javascript
const createContainer = () => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

Den andra parametern (*alternativ*) för **createContainerIfNotExists** accepterar ett värde för [publicAccessLevel](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists). Värdet *blob* för *publicAccessLevel* anger att specifika blobbdata exponeras offentligt. Den här inställningen är motsatt till *behållarens* nivååtkomst, vilket ger möjlighet att visa innehållet i behållaren.

Användningen av **createContainerIfNotExists** innebär att programmet kör kommandot *createContainer* flera gånger utan att returnera fel när behållaren redan finns. I en produktionsmiljö kan du ofta bara anropa **createContainerIfNotExists** när samma behållare används i hela programmet. I dessa fall kan du skapa behållaren i förväg via portalen eller via Azure CLI.

### <a name="upload-a-blob-to-the-container"></a>Ladda upp en blob till behållaren

Funktionen *Ladda upp* använder [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromLocalFile) till att ladda upp och skriva eller skriva över en fil från filsystemet till blobblagringen. 

```javascript
const upload = () => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromLocalFile(containerName, blobName, sourceFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Upload of '${blobName}' complete` });
            }
        });
    });
};
```
I samband med det här exempelprogrammet överförs filen *exempel.txt* till en blob med namnet *example* i en behållare med namnet *test-container*. Andra metoder som är tillgängliga för att ladda upp innehåll till blobbar är att arbeta med [text](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) och [dataströmmar](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromStream).

Om du vill kontrollera att filen har laddats upp till blobblagringen, kan du använda [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) till att visa datan på ditt konto.

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare

Funktionen *list* anropar metoden [listBlobsSegmented](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) för att returnera en lista med blobbmetadata i en behållare. 

```javascript
const list = () => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Items in container '${containerName}':`, data: data });
            }
        });
    });
};
```

Om *listBlobsSegmented* anropas returneras blobbmetadata som en matris med [BlobResult](/nodejs/api/azure-storage/blobresult)-instanser. Resultaten returneras i 5 000 inkrementella batchar (segment). Om det finns fler än 5 000 blobbar i en behållare kommer resultatet innehålla ett värde för **continuationToken**. Visa efterföljande segment från blobbehållaren genom att skicka fortsättningstoken tillbaka till **listBlobSegmented** som det andra argumentet.

### <a name="download-a-blob-from-the-container"></a>Ladda ner en blob från behållaren

Funktionen för att *ladda ned* använder [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_getBlobToLocalFile) för att hämta innehållet i blobben till den angivna absoluta sökvägen.

```javascript
const download = () => {
    const dowloadFilePath = sourceFilePath.replace('.txt', '.downloaded.txt');
    return new Promise((resolve, reject) => {
        blobService.getBlobToLocalFile(containerName, blobName, dowloadFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Download of '${blobName}' complete` });
            }
        });
    });
};
```
Implementeringen som visas här ändrar sökvägen till källfilen genom att lägga till *. downloaded.txt* i filnamnet. I verkligheten kan du ändra både plats och namn på filen när du väljer ett mål för nedladdningen.

### <a name="delete-blobs-in-the-container"></a>Ta bort blobbar i behållaren

Funktionen *deleteBlock* (ett alias för konsolkommandot *ta bort*) anropar funktionen [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists). Som namnet antyder returnerar den här funktionen inte ett fel som blobben redan har tagit bort.

```javascript
const deleteBlock = () => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="upload-and-list"></a>Ladda upp och lista

En av fördelarna med att använda promise är att kunna sätta ihop kommandon. Funktionen **uploadAndList** visar hur lätt det är att visa innehållet i en blob direkt efter att en fil har laddats upp.

```javascript
const uploadAndList = () => {
    return _module.upload().then(_module.list);
};
```

### <a name="calling-code"></a>Anropa kod

Om du vill använda funktioner som implementerats till kommandoraden, mappas varje funktion till ett objekt som är literalt.

```javascript
const _module = {
    "createContainer": createContainer,
    "upload": upload,
    "download": download,
    "delete": deleteBlock,
    "list": list,
    "uploadAndList": uploadAndList
};
```

Nu när *_module* är på plats är varje kommando tillgängligt från kommandoraden.

```javascript
const commandExists = () => exists = !!_module[args.command];
```

Om ett visst kommando inte finns, renderas *_module*-egenskaperna till konsolen som hjälptext för användaren. 

Funktionen *executeCommand* är en *async*-funktion som anropar det angivna kommandot med hjälp av *await*-operatorn och som loggar meddelanden till data i konsolen.

```javascript
const executeCommand = async () => {
    const response = await _module[args.command]();

    console.log(response.message);

    if (response.data) {
        response.data.entries.forEach(entry => {
            console.log('Name:', entry.name, ' Type:', entry.blobType)
        });
    }
};
```

Slutligen anropar körningskoden först *commandExists* för att verifiera att ett känt kommando skickades till skriptet. Om ett befintligt kommando väljs, körs kommandot och eventuella fel loggas i konsolen.

```javascript
try {
    const cmd = args.command;

    console.log(`Executing '${cmd}'...`);

    if (commandExists()) {
        executeCommand();
    } else {
        console.log(`The '${cmd}' command does not exist. Try one of these:`);
        Object.keys(_module).forEach(key => console.log(` - ${key}`));
    }
} catch (e) {
    console.log(e);
}
```

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte planerar att använda data eller konton som har skapats i den här artikeln, kan du ta bort dem för att undvika eventuell oönskad fakturering. Om du vill ta bort blob och behållare kan du använda metoderna [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteBlobIfExists_container__blob__options__callback_) och [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteContainerIfExists_container__options__callback_). Du kan också ta bort lagringskontot [via portalen](../common/storage-create-storage-account.md).

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Resurser för utveckling av Node.js-program med blobbar

Se dessa ytterligare resurser för Node.js-utveckling med blobblagring:

### <a name="binaries-and-source-code"></a>Binärfiler och källkod

- Visa och installera [Node.js-klientbibliotekets källkod](https://github.com/Azure/azure-storage-node) för Azure Storage på GitHub.

### <a name="client-library-reference-and-samples"></a>Referens och exempel för klientbiblioteket

- Mer information om klientbiblioteket för Node.js finns i [API-referens för Node.js](https://docs.microsoft.com/javascript/api/overview/azure/storage).
- Utforska [Blobblagringsexempel](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob) som skrivits med Node.js-klientbiblioteket.

## <a name="next-steps"></a>Nästa steg

Den här snabbstarten visar hur du laddar upp en fil mellan en lokal disk och Azure Blob Storage med Node.js. Om du vill veta mer om att arbeta med Blob Storage kan du fortsätta till anvisningarna om Blob Storage.

> [!div class="nextstepaction"]
> [Anvisningar för Blob Storage-åtgärder](storage-nodejs-how-to-use-blob-storage.md)

Information om Node.js-referensen för Azure Storage finns i [azure-storage-paketet](https://docs.microsoft.com/javascript/api/azure-storage).
