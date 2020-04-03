---
title: 'Snabbstart: Azure Blob storage för JavaScript v10 i webbläsaren'
description: Lär dig att ladda upp, lista och ta bort blobbar med JavaScript v10 SDK på en HTML-sida.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.author: mhopkins
ms.date: 01/24/2020
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: 5219af0ec343a6e7f87a07e4a7280ac5f4e85cd3
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619117"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-browser"></a>Snabbstart: Hantera blobbar med JavaScript v10 SDK i webbläsaren

I den här snabbstarten lär du dig att hantera blobbar med hjälp av JavaScript-kod som körs helt i webbläsaren. Blobbar är objekt som kan innehålla stora mängder text eller binära data, inklusive bilder, dokument, strömmande media och arkivdata. Du använder nödvändiga säkerhetsåtgärder för att säkerställa skyddad åtkomst till ditt blob-lagringskonto.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Ett Azure Storage-konto. [Skapa ett lagringskonto](../common/storage-account-create.md).
- En lokal webbserver. I den här artikeln används [Node.js](https://nodejs.org) för att öppna en enkel server.
- [Visual Studio-kod](https://code.visualstudio.com).
- Ett VS-kodtillägg för felsökning av webbläsare, till exempel [Felsökning för Chrome](vscode:extension/msjsdiag.debugger-for-chrome) eller Felsökning för Microsoft [Edge](vscode:extension/msjsdiag.debugger-for-edge).

## <a name="setting-up-storage-account-cors-rules"></a>Ställa in CORS-regler för lagringskonto

Innan webbprogrammet kan komma åt en blob-lagring från klienten måste du konfigurera kontot så att det aktiverar [resursdelning mellan ursprung](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)eller CORS.

Återgå till Azure Portal och välj ditt lagringskonto. Om du vill definiera en ny CORS-regel navigerar du till avsnittet **Inställningar** och klickar på **CORS-länken.** Klicka sedan på knappen **Lägg till** för att öppna fönstret **Lägg till CORS-regel**. För den här snabbstarten skapar du en öppen CORS-regel:

![CORS-inställningar för Azure Blob Storage-konto](media/storage-quickstart-blobs-javascript-client-libraries-v10/azure-blob-storage-cors-settings.png)

I följande tabell beskrivs varje CORS-inställning och de värden som används för att definiera regeln.

|Inställning  |Värde  | Beskrivning |
|---------|---------|---------|
| Tillåtna ursprung | * | Accepterar en kommaavgränsad lista över domäner som är inställda som godkänt ursprung. Om du anger värdet till `*` ges alla domäner åtkomst till lagringskontot. |
| Tillåtna metoder     | delete, get, head, merge, post, options och put | Listar de HTTP-verb som kan köras mot lagringskontot. För den här snabbstarten väljer du alla tillgängliga alternativ. |
| Tillåtna rubriker | * | Definierar en lista över begäranderubriker (inklusive prefixrubriker) som tillåts av lagringskontot. Om du ställer in värdet på `*` får alla rubriker åtkomst. |
| Exponerade rubriker | * | Listar de svarsrubriker som tillåts av kontot. Om du ställer in värdet på `*` får kontot skicka alla rubriker.  |
| Max ålder (sekunder) | 86400 | Den maximala tidsperiod som webbläsaren cachelagrar den preliminära OPTIONS-begäran. Ett värde på *86400* innebär cachelagring under ett helt dygn. |

> [!IMPORTANT]
> Se till att alla inställningar som du använder i produktionen visar den minsta åtkomst som krävs för ditt lagringskonto för att upprätthålla säker åtkomst. De CORS-inställningar som beskrivs här är lämpliga för en snabbstart eftersom de innebär en flexibel säkerhetsprincip. Inställningarna rekommenderas dock inte i verkliga sammanhang.

Härnäst använder du Azure-molnskalet och skapar en säkerhetstoken.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Skapa en signatur för delad åtkomst

SAS, eller signatur för delad åtkomst, används av den kod som körs i webbläsaren för att auktorisera begäranden till bloblagring. Med hjälp av SAS kan klienten auktorisera åtkomst till lagringsresurser utan att ha åtkomstnyckel eller anslutningssträng för kontot. Mer information om SAS finns i [Använda signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md).

Du kan skapa en SAS med Azure CLI via Azure-molnskalet eller med Azure-portalen eller Azure Storage Explorer. I följande tabell beskrivs de parametrar som du behöver ange värden för att generera en SAS med CLI.

| Parameter      |Beskrivning  | Platshållare |
|----------------|-------------|-------------|
| *Utgången*       | Förfallodatum för åtkomsttoken i formatet YYYY-MM-DD. Ange morgondagens datum under den här snabbstarten. | *FUTURE_DATE* |
| *kontonamn* | Namnet på lagringskontot. Använd det namn som noterades i ett tidigare steg. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | Nyckeln till lagringskontot. Använd den nyckel som noterades i ett tidigare steg. | *YOUR_STORAGE_ACCOUNT_KEY* |

Använd följande CLI-kommando, med faktiska värden för varje platshållare, för att generera en SAS som du kan använda i JavaScript-koden.

```azurecli-interactive
az storage account generate-sas \
  --permissions racwdl \
  --resource-types sco \
  --services b \
  --expiry FUTURE_DATE \
  --account-name YOUR_STORAGE_ACCOUNT_NAME \
  --account-key YOUR_STORAGE_ACCOUNT_KEY
```

Du kan kanske uppleva serien med värden efter varje parameter som lite kryptisk. Dessa parametervärden hämtas från den första bokstaven i respektive behörighet. I följande tabell förklaras var värdena kommer från:

| Parameter        | Värde   | Beskrivning  |
|------------------|---------|---------|
| *Behörigheter*    | racwdl  | Den här signaturen för delad åtkomst (SAS) kan *läsa*, *bifoga*, *skapa*, *skriva*, *ta bort* och *lista * funktioner. |
| *resource-types* | sco     | De resurser som påverkas av signaturen för delad åtkomst (SAS) är *tjänst*, *container* och *objekt*. |
| *Tjänster*       | b       | Den tjänst som påverkas av signaturen för delad åtkomst är *blob*-tjänsten. |

Nu när SAS genereras kopierar du returvärdet och sparar det någonstans för användning i ett kommande steg. Om du har genererat din SAS med en annan metod `?` än Azure CLI måste du ta bort den första om den finns. Det här tecknet är en URL-avgränsare som redan finns i URL-mallen senare i det här avsnittet där SAS används.

> [!IMPORTANT]
> I produktion, alltid passera SAS tokens med TLS. SAS-token bör dessutom genereras på servern och skickas till HTML-sidan för att återgå till Azure Blob Storage. En metod du kan överväga är att använda en serverlös funktion när du genererar SAS-tokens. I Azure-portalen finns funktionsmallar som ger möjligheten att generera en SAS med en JavaScript-funktion.

## <a name="implement-the-html-page"></a>Implementera HTML-sidan

I det här avsnittet ska du skapa en grundläggande webbsida och konfigurera VS-kod för att starta och felsöka sidan. Innan du kan starta måste du dock använda Node.js för att starta en lokal webbserver och visa sidan när webbläsaren begär det. Därefter ska du lägga till JavaScript-kod för att anropa olika blob storage API:er och visa resultaten på sidan. Du kan också se resultaten av dessa anrop i [Azure-portalen,](https://portal.azure.com) [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer)och Azure [Storage-tillägget](vscode:extension/ms-azuretools.vscode-azurestorage) för VS-kod.

### <a name="set-up-the-web-application"></a>Konfigurera webbprogrammet

Skapa först en ny mapp med namnet *azure-blobs-javascript* och öppna den i VS-kod. Skapa sedan en ny fil i VS-kod, lägg till följande HTML och spara den som *index.html* i mappen *azure-blobs-javascript.*

```html
<!DOCTYPE html>
<html>

<body>
    <button id="create-container-button">Create container</button>
    <button id="delete-container-button">Delete container</button>
    <button id="select-button">Select and upload files</button>
    <input type="file" id="file-input" multiple style="display: none;" />
    <button id="list-button">List files</button>
    <button id="delete-button">Delete selected files</button>
    <p><b>Status:</b></p>
    <p id="status" style="height:160px; width: 593px; overflow: scroll;" />
    <p><b>Files:</b></p>
    <select id="file-list" multiple style="height:222px; width: 593px; overflow: scroll;" />
</body>

<!-- You'll add code here later in this quickstart. -->

</html>
```

### <a name="configure-the-debugger"></a>Konfigurera felsökaren

Om du vill ställa in felsökningstillägget i VS-kod väljer du **Felsöka > Lägg till konfiguration...** och väljer sedan **Chrome** eller **Edge**, beroende på vilket tillägg du installerade i avsnittet Förutsättningar tidigare. Den här åtgärden skapar en *launch.json-fil* och öppnar den i redigeraren.

Ändra sedan *filen launch.json* så `url` att `/index.html` värdet innehåller som visas:

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:8080/index.html",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```

Den här konfigurationen talar om för VS-kod vilken webbläsare som ska startas och vilken URL som ska läsas in.

### <a name="launch-the-web-server"></a>Starta webbservern

Om du vill starta den lokala nod.js-webbservern väljer du **Visa > Terminal** för att öppna ett konsolfönster i VS-kod och anger sedan följande kommando.

```console
npx http-server
```

Det här kommandot installerar *http-server-paketet* och startar servern, vilket gör den aktuella mappen tillgänglig via standardadresser, inklusive den som anges i föregående steg.

### <a name="start-debugging"></a>Börja felsöka

Om du vill starta *index.html* i webbläsaren med VS-kodfelsökaren bifogad väljer du **Felsökning > Starta felsökning** eller tryck på F5 i VS-kod.

Användargränssnittet som visas gör ingenting ännu, men du lägger till JavaScript-kod i följande avsnitt för att implementera varje funktion som visas. Du kan sedan ange brytpunkter och interagera med felsökningsfel när den är pausad på din kod.

När du gör ändringar i *index.html*måste du läsa in sidan igen för att se ändringarna i webbläsaren. I VS-kod kan du också välja **Felsökning > Starta om felsökning** eller trycka på CTRL + SKIFT + F5.

### <a name="add-the-blob-storage-client-library"></a>Lägga till klientbiblioteket för bloblagring

Om du vill aktivera anrop till blob storage API hämtar du först [Azure Storage SDK för JavaScript - Blob-klientbiblioteket](https://aka.ms/downloadazurestoragejsblob), extraherar innehållet i *zip-filen och placerar azure-storage-blob.js-filen* i mappen *azure-blobs-javascript.*

Klistra sedan in följande HTML i `</body>` *index.html* efter den avslutande taggen och ersätt platshållarkommentaren.

```html
<script src="azure-storage-blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Den här koden lägger till en referens till skriptfilen och tillhandahåller en plats för din egen JavaScript-kod. I den här snabbstarten använder vi skriptfilen *azure-storage-blob.js* så att du kan öppna den i VS-kod, läsa dess innehåll och ange brytpunkter. I produktionen bör du använda den mer kompakta *azure-storage.blob.min.js-filen* som också finns i zip-filen.

Du kan läsa mer om varje blob storage-funktion i [referensdokumentationen](https://docs.microsoft.com/javascript/api/%40azure/storage-blob/index). Observera att vissa av funktionerna i SDK endast är tillgängliga i Node.js eller endast är tillgängliga i webbläsaren.

Koden i *azure-storage-blob.js exporterar* `azblob`en global variabel som kallas , som du ska använda i JavaScript-koden för att komma åt blob-lagrings-API:erna.

### <a name="add-the-initial-javascript-code"></a>Lägg till den ursprungliga JavaScript-koden

Klistra sedan in följande `<script>` kod i elementet som visas i föregående kodblock och ersätta platshållarkommentaren.

```javascript
const createContainerButton = document.getElementById("create-container-button");
const deleteContainerButton = document.getElementById("delete-container-button");
const selectButton = document.getElementById("select-button");
const fileInput = document.getElementById("file-input");
const listButton = document.getElementById("list-button");
const deleteButton = document.getElementById("delete-button");
const status = document.getElementById("status");
const fileList = document.getElementById("file-list");

const reportStatus = message => {
    status.innerHTML += `${message}<br/>`;
    status.scrollTop = status.scrollHeight;
}
```

Den här koden skapar fält för varje HTML-element som `reportStatus` följande kod ska använda och implementerar en funktion för att visa utdata.

I följande avsnitt lägger du till varje nytt block av JavaScript-kod efter föregående block.

### <a name="add-your-storage-account-info"></a>Lägga till information om ditt lagringskonto

Lägg sedan till kod för att komma åt ditt lagringskonto och ersätta platshållarna med ditt kontonamn och den SAS som du skapade i ett tidigare steg.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

Den här koden använder din kontoinformation och SAS för att skapa en [ContainerURL-instans,](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL) vilket är användbart för att skapa och manipulera en lagringsbehållare.

### <a name="create-and-delete-a-storage-container"></a>Skapa och ta bort en lagringsbehållare

Lägg sedan till kod för att skapa och ta bort lagringsbehållaren när du trycker på motsvarande knapp.

```javascript
const createContainer = async () => {
    try {
        reportStatus(`Creating container "${containerName}"...`);
        await containerURL.create(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

const deleteContainer = async () => {
    try {
        reportStatus(`Deleting container "${containerName}"...`);
        await containerURL.delete(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

createContainerButton.addEventListener("click", createContainer);
deleteContainerButton.addEventListener("click", deleteContainer);
```

Den här koden anropar ContainerURL-funktioner för [att skapa](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) och [ta bort](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) utan att använda en [Aborter-instans.](https://docs.microsoft.com/javascript/api/@azure/storage-blob/aborter) För att hålla det enkelt för den här snabbstarten förutsätter den här koden att ditt lagringskonto har skapats och är aktiverat. I produktionskoden använder du en Aborter-instans för att lägga till timeout-funktioner.

### <a name="list-blobs"></a>Lista blobar

Lägg sedan till kod för att visa innehållet i lagringsbehållaren när du trycker på knappen **Lista filer.**

```javascript
const listFiles = async () => {
    fileList.size = 0;
    fileList.innerHTML = "";
    try {
        reportStatus("Retrieving file list...");
        let marker = undefined;
        do {
            const listBlobsResponse = await containerURL.listBlobFlatSegment(
                azblob.Aborter.none, marker);
            marker = listBlobsResponse.nextMarker;
            const items = listBlobsResponse.segment.blobItems;
            for (const blob of items) {
                fileList.size += 1;
                fileList.innerHTML += `<option>${blob.name}</option>`;
            }
        } while (marker);
        if (fileList.size > 0) {
            reportStatus("Done.");
        } else {
            reportStatus("The container does not contain any files.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

listButton.addEventListener("click", listFiles);
```

Den här koden anropar funktionen [ContainerURL.listBlobFlatSegment](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) i en loop för att säkerställa att alla segment hämtas. För varje segment loopar den över listan över blob-objekt som det innehåller och uppdaterar **fillistan.**

### <a name="upload-blobs"></a>Ladda upp blobbar

Lägg sedan till kod för att ladda upp filer till lagringsbehållaren när du trycker på knappen **Markera och ladda upp filer.**

```javascript
const uploadFiles = async () => {
    try {
        reportStatus("Uploading files...");
        const promises = [];
        for (const file of fileInput.files) {
            const blockBlobURL = azblob.BlockBlobURL.fromContainerURL(containerURL, file.name);
            promises.push(azblob.uploadBrowserDataToBlockBlob(
                azblob.Aborter.none, file, blockBlobURL));
        }
        await Promise.all(promises);
        reportStatus("Done.");
        listFiles();
    } catch (error) {
        reportStatus(error.body.message);
    }
}

selectButton.addEventListener("click", () => fileInput.click());
fileInput.addEventListener("change", uploadFiles);
```

Den här koden ansluter knappen Välj och `file-input` ladda upp **filer** till det dolda elementet. På så sätt `click` utlöser knapphändelsen `click` filinmatningshändelsen och visar filväljaren. När du har markerat filer `input` och stängt `uploadFiles` dialogrutan inträffar händelsen och funktionen anropas. Den här funktionen anropar funktionen endast [för webbläsareBrowserDataToBlockBlob](https://docs.microsoft.com/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) för varje fil du valt. Varje anrop returnerar ett löfte, som läggs till i en lista så att alla kan vänta på en gång, vilket gör att filerna laddas upp parallellt.

### <a name="delete-blobs"></a>Ta bort blobbar

Lägg sedan till kod för att ta bort filer från lagringsbehållaren när du trycker på knappen **Ta bort markerade filer.**

```javascript
const deleteFiles = async () => {
    try {
        if (fileList.selectedOptions.length > 0) {
            reportStatus("Deleting files...");
            for (const option of fileList.selectedOptions) {
                const blobURL = azblob.BlobURL.fromContainerURL(containerURL, option.text);
                await blobURL.delete(azblob.Aborter.none);
            }
            reportStatus("Done.");
            listFiles();
        } else {
            reportStatus("No files selected.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

deleteButton.addEventListener("click", deleteFiles);
```

Den här koden anropar funktionen [BlobURL.delete](https://docs.microsoft.com/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) för att ta bort varje fil som valts i listan. Den anropar `listFiles` sedan funktionen som visas tidigare för att uppdatera innehållet i **fillistan.**

### <a name="run-and-test-the-web-application"></a>Kör och testa webbprogrammet

Nu kan du starta sidan och experimentera för att få en känsla för hur bloblagring fungerar. Om några fel uppstår (till exempel när du försöker lista filer innan du har skapat behållaren) visas felmeddelandet mottaget i **fönstret Status.** Du kan också ange brytpunkter i JavaScript-koden för att undersöka de värden som returneras av lagrings-API:erna.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa de resurser som skapades under den här snabbstarten går du till [Azure-portalen](https://portal.azure.com) och tar bort resursgruppen som du skapade i avsnittet Förutsättningar.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en enkel webbplats som har åtkomst till blob-lagring från webbläsarbaserade JavaScript. Om du vill veta hur du kan vara värd för en webbplats själv på blob storage fortsätter du till följande självstudiekurs:

> [!div class="nextstepaction"]
> [Värd för en statisk webbplats på Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)
