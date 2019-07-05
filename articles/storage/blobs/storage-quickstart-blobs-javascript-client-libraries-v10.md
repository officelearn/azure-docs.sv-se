---
title: Snabbstart – skapa en blob i Azure Storage med hjälp av JavaScript och HTML i webbläsaren
description: Lär dig använda en instans av BlobService och överföra, lista och ta bort blobar med hjälp av JavaScript på en HTML-sida.
services: storage
keywords: storage, javascript, html
author: KarlErickson
ms.custom: mvc
ms.service: storage
ms.author: karler
ms.reviewer: seguler
ms.date: 05/20/2019
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: a971f2b4b63b3fd35777d1d890da8451b84bb086
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544034"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-upload-list-and-delete-blobs-using-azure-storage-v10-sdk-for-javascripthtml-in-the-browser"></a>Snabbstart: Ladda upp, lista och ta bort blobar med Azure Storage v10 SDK för JavaScript/HTML i webbläsaren

I den här snabbstarten lär du dig att använda den [Azure Storage SDK V10 för JavaScript - Blob](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob#readme) biblioteket du hanterar blobar från JavaScript-kod som körs helt i webbläsaren. Den metod som används här visar hur du tillämpar de säkerhetsåtgärder som krävs för att garantera säker åtkomst till bloblagringskontot.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Azure Storage JavaScript-klientbibliotek fungerar inte direkt från filsystemet och måste hanteras från en webbserver. Det här avsnittet använder [Node.js](https://nodejs.org) att starta en grundläggande server. Om du inte föredrar att installera om noden kan du använda något annat sätt för att köra en lokal webbserver.

Om du vill följa stegen i felsökning, behöver du [Visual Studio Code](https://code.visualstudio.com) och antingen den [felsökare för Chrome](vscode:extension/msjsdiag.debugger-for-chrome) eller [felsökare för Microsoft Edge](vscode:extension/msjsdiag.debugger-for-edge) tillägget.

## <a name="setting-up-storage-account-cors-rules"></a>Ställa in CORS-regler för lagringskonto

Innan webbprogrammet kan komma åt en bloblagring från klienten, måste du konfigurera ditt konto för att aktivera [resursdelning mellan ursprung delning](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services), eller CORS.

Återgå till Azure Portal och välj ditt lagringskonto. För att definiera en ny CORS-regel, navigera till den **inställningar** och klicka på den **CORS** länk. Klicka sedan på knappen **Lägg till** för att öppna fönstret **Lägg till CORS-regel**. För den här snabbstarten skapar du en öppen CORS-regel:

![CORS-inställningar för Azure Blob Storage-konto](media/storage-quickstart-blobs-javascript-client-libraries/azure-blob-storage-cors-settings.png)

I följande tabell beskrivs varje CORS-inställning och de värden som används för att definiera regeln.

|Inställning  |Värde  | Beskrivning |
|---------|---------|---------|
| Tillåtna ursprung | * | Accepterar en kommaavgränsad lista över domäner som är inställda som godkänt ursprung. Om du anger värdet till `*` ges alla domäner åtkomst till lagringskontot. |
| Tillåtna verb     | delete, get, head, merge, post, options och put | Listar de HTTP-verb som kan köras mot lagringskontot. För den här snabbstarten väljer du alla tillgängliga alternativ. |
| Tillåtna rubriker | * | Definierar en lista över begäranderubriker (inklusive prefixrubriker) som tillåts av lagringskontot. Om du ställer in värdet på `*` får alla rubriker åtkomst. |
| Exponerade rubriker | * | Listar de svarsrubriker som tillåts av kontot. Om du ställer in värdet på `*` får kontot skicka alla rubriker.  |
| Maximal ålder (sekunder) | 86400 | Den maximala tidsperiod som webbläsaren cachelagrar den preliminära OPTIONS-begäran. Ett värde på *86400* innebär cachelagring under ett helt dygn. |

> [!IMPORTANT]
> Se till att alla inställningar som du använder i produktionen exponera den minsta mängden åtkomst till ditt storage-konto att ha säker åtkomst. De CORS-inställningar som beskrivs här är lämpliga för en snabbstart eftersom de innebär en flexibel säkerhetsprincip. Inställningarna rekommenderas dock inte i verkliga sammanhang.

Härnäst använder du Azure-molnskalet och skapar en säkerhetstoken.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Skapa en signatur för delad åtkomst

SAS, eller signatur för delad åtkomst, används av den kod som körs i webbläsaren för att auktorisera begäranden till bloblagring. Med hjälp av SAS kan klienten auktorisera åtkomst till lagringsresurser utan att ha åtkomstnyckel eller anslutningssträng för kontot. Mer information om SAS finns i [Använda signaturer för delad åtkomst (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md).

Du kan skapa en SAS med hjälp av Azure CLI via Azure-molnskalet eller med Azure portal eller Azure Storage Explorer. I följande tabell beskrivs de parametrar du behöver ange värden för att generera en SAS med CLI.

| Parameter      |Beskrivning  | Platshållare |
|----------------|-------------|-------------|
| *expiry*       | Förfallodatum för åtkomsttoken i formatet YYYY-MM-DD. Ange morgondagens datum under den här snabbstarten. | *FUTURE_DATE* |
| *account-name* | Namnet på lagringskontot. Använd det namn som noterades i ett tidigare steg. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | Nyckeln till lagringskontot. Använd den nyckel som noterades i ett tidigare steg. | *YOUR_STORAGE_ACCOUNT_KEY* |

Använd följande CLI-kommando med faktiska värden för varje platshållare för att generera en SAS som du kan använda i JavaScript-koden.

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
| *behörigheter*    | racwdl  | Den här signaturen för delad åtkomst (SAS) kan *läsa*, *bifoga*, *skapa*, *skriva*, *ta bort* och *lista*  funktioner. |
| *resource-types* | sco     | De resurser som påverkas av signaturen för delad åtkomst (SAS) är *tjänst*, *container* och *objekt*. |
| *tjänster*       | b       | Den tjänst som påverkas av signaturen för delad åtkomst är *blob*-tjänsten. |

Nu när signaturen för delad åtkomst genereras kopierar det returnera värdet och spara den någonstans för användning i ett kommande steg. Om du genererade din SAS med en annan metod än Azure CLI, måste du ta bort första `?` om den finns. Det här tecknet är URL: en avgränsare som redan har angetts i URL: en mall i detta kapitel där SAS används.

> [!IMPORTANT]
> Under produktionen ska du alltid skicka SAS-token med SSL. SAS-token bör dessutom genereras på servern och skickas till HTML-sidan för att återgå till Azure Blob Storage. En metod du kan överväga är att använda en serverlös funktion när du genererar SAS-tokens. I Azure-portalen finns funktionsmallar som ger möjligheten att generera en SAS med en JavaScript-funktion.

## <a name="implement-the-html-page"></a>Implementera HTML-sidan

I det här avsnittet ska du skapa en grundläggande webbsida och konfigurera VS Code för att starta och felsöka sidan. Innan du kan starta, men behöver du använda Node.js för att starta en lokal webbserver och fungerar på sidan när webbläsaren begär den. Därefter lägger du till JavaScript-kod för att anropa API: er av olika blob storage och visa resultatet på sidan. Du kan också se resultatet av dessa anrop i den [Azure-portalen](https://portal.azure.com), [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer), och [Azure Storage-tillägget](vscode:extension/ms-azuretools.vscode-azurestorage) för VS Code.

### <a name="set-up-the-web-application"></a>Konfigurera webbprogrammet

Börja med att skapa en ny mapp med namnet *azure-blobs-javascript* och öppna den i VS Code. Sedan skapar en ny fil i VS Code, Lägg till följande HTML och spara den som *index.html* i den *azure-blobs-javascript* mapp.

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

### <a name="configure-the-debugger"></a>Konfigurera felsökningsprogrammet

Om du vill konfigurera felsökare tillägg i VS Code, Välj **Felsök > Lägg till konfiguration...** och välj sedan **Chrome** eller **Edge**, beroende på vilka tillägg som du tidigare installerats i avsnittet krav. Den här åtgärden skapar en *launch.json* filen och öppnar den i redigeraren.

Sedan ändrar den *launch.json* filen så att den `url` värdet innefattar `/index.html` enligt:

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

Den här konfigurationen anger VS Code vilken webbläsare för att starta och vilka URL: en för att läsa in.

### <a name="launch-the-web-server"></a>Starta webbservern

Om du vill starta den lokala Node.js-webbservern, Välj **Visa > Terminal** för att öppna ett konsolfönster i VS Code, ange sedan följande kommando.

```console
npx http-server
```

Det här kommandot installerar den *http-server* paketera och starta servern, vilket gör den aktuella mappen tillgänglig via standard-URL: er, inklusive vad som anges i föregående steg.

### <a name="start-debugging"></a>Starta felsökning

Att starta *index.html* i webbläsaren med VS Code-felsökare anslutna, Välj **Felsök > Starta felsökning** eller tryck på F5 i VS Code.

Användargränssnittet visas gör fortfarande inget, men ska du lägga till JavaScript-kod i följande avsnitt för att implementera varje funktion visas. Du kan ange brytpunkter och interagera med felsökningsprogrammet när den har pausats på din kod.

När du gör ändringar i *index.html*, se till att uppdatera sidan om du vill se ändringarna i webbläsaren. I VS Code kan du också välja **Felsök > Starta om felsökning** eller tryck på CTRL + SKIFT + F5.

### <a name="add-the-blob-storage-client-library"></a>Lägg till blob storage-klientbibliotek

Aktivera anrop till blob storage-API, först [ladda ned Azure Storage SDK för JavaScript - klientbibliotek för Blob](https://aka.ms/downloadazurestoragejsblob), extrahera innehållet i ZIP-filen och placera den *azure storage.blob.js* fil i *azure-blobs-javascript* mapp.

Klistra sedan in följande HTML till *index.html* när den `</body>` sluttagg, byta ut platshållaren kommentaren.

```html
<script src="azure-storage.blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Den här koden lägger till en referens till skriptfilen och ett utrymme för din egen JavaScript-kod. För den här snabbstarten använder vi den *azure storage.blob.js* skriptfilen så att du kan öppna den i VS Code, läsa innehållet och ange brytpunkter. I produktion, bör du använda flera CD *azure storage.blob.min.js* -fil som ges också i zip-filen.

Du hittar mer information om varje funktion som blob storage i den [referensdokumentation](https://docs.microsoft.com/javascript/api/%40azure/storage-blob/index). Observera att några av funktionerna i SDK: N är endast tillgängligt i Node.js eller endast tillgängligt i webbläsaren.

Koden i *azure storage.blob.js* exporterar en global variabel som kallas `azblob`, som du använder i JavaScript-koden för att få åtkomst till API: er för blob storage.

### <a name="add-the-initial-javascript-code"></a>Lägg till första JavaScript-kod

Klistra sedan in följande kod till den `<script>` element som visas i föregående kodblock, ersätta platshållaren kommentaren.

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

Den här koden skapar fält för varje HTML-element som använder följande kod och implementerar en `reportStatus` funktionen för att visa utdata.

Lägg till varje ny block med JavaScript-kod efter föregående block i avsnitten nedan.

### <a name="add-your-storage-account-info"></a>Lägg till din kontoinformation för lagring

Lägg sedan till kod för att få åtkomst till ditt lagringskonto, ersätter platshållarna med namnet på ditt konto och den SAS som du genererade i föregående steg.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

Den här koden använder din kontoinformation och SAS för att skapa en [ContainerURL](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL) instansen, som är användbar för att skapa och ändra en lagringsbehållare.

### <a name="create-and-delete-a-storage-container"></a>Skapa och ta bort en lagringsbehållare

Lägg sedan till kod för att skapa och ta bort lagringsbehållaren när du trycker på knappen motsvarande.

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

Den här koden anropar ContainerURL [skapa](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL#create-aborter--icontainercreateoptions-) och [ta bort](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL#delete-aborter--icontainerdeletemethodoptions-) funktioner utan att använda en [Aborter](https://docs.microsoft.com/javascript/api/@azure/storage-blob/aborter) instans. Den här koden förutsätter att ditt lagringskonto har skapats och är aktiverad om du vill att göra det enkelt för den här snabbstarten. I produktionskoden använder du en Aborter instans att lägga till funktioner för timeout.

### <a name="list-blobs"></a>Lista blobar

Lägg sedan till kod för att visa innehållet i lagringsbehållaren när du trycker på den **lista filer** knappen.

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

Den här koden anropar den [ContainerURL.listBlobFlatSegment](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL#listblobflatsegment-aborter--string--icontainerlistblobssegmentoptions-) funktion i en loop för att säkerställa att alla segment har hämtats. För varje segment kan den slinga över listan med blob-objekt den innehåller och uppdaterar den **filer** lista.

### <a name="upload-blobs"></a>Ladda upp blobar

Lägg sedan till kod för att ladda upp filer till behållaren när du trycker på den **välja och ladda upp filer** knappen.

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

Den här koden ansluter den **välja och ladda upp filer** knappen för att den dolda `file-input` element. På så sätt kan knappen `click` händelse utlöser den fil som indata `click` händelse och visar filväljaren. När du väljer filer och Stäng dialogrutan för den `input` händelse inträffar och `uploadFiles` funktionen anropas. Den här funktionen anropar den webbläsaren endast [uploadBrowserDataToBlockBlob](https://docs.microsoft.com/javascript/api/@azure/storage-blob/#uploadbrowserdatatoblockblob-aborter--blob---arraybuffer---arraybufferview--blockbloburl--iuploadtoblockbloboptions-) funktionen för varje fil som du har valt. Varje anrop returnerar ett löfte som läggs till i en lista, så att de kan alla vara slutförts samtidigt, orsakar filer att ladda upp parallellt.

### <a name="delete-blobs"></a>Ta bort blobbar

Lägg sedan till kod för att ta bort filer från lagringsbehållaren när du trycker på den **ta bort valda filer** knappen.

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

Den här koden anropar den [BlobURL.delete](https://docs.microsoft.com/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) att ta bort varje fil som valts i listan. Sedan anropas den `listFiles` funktionen som visas tidigare för att uppdatera innehållet i den **filer** lista.

### <a name="run-and-test-the-web-application"></a>Kör och testa webbprogrammet

Nu kan du starta sidan och experimentera med en känsla för hur blob storage fungerar. Om något fel uppstår (till exempel när du försöker att lista filer innan du har skapat behållaren) den **Status** visas ett felmeddelande visas i fönstret. Du kan också ange brytpunkter i JavaScript-kod för att granska värdena som returneras av API: er för storage.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa de resurser som skapades under den här snabbstarten kan du gå till den [Azure-portalen](https://portal.azure.com) och ta bort resursgruppen som du skapade i avsnittet krav.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en enkel webbplats som använder blob-lagring från webbläsarbaserade JavaScript. Om du vill veta hur du kan ha en webbplats själva på blob-lagring, Fortsätt till följande självstudie:

> [!div class="nextstepaction"]
> [Vara värd för en statisk webbplats i Blob-lagring](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)
