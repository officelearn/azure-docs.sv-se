---
title: 'Snabb start: formulär tolkens klient bibliotek för Java Script'
description: I den här snabb starten kommer du igång med formulär tolkens klient bibliotek för Java Script.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/08/2020
ms.author: pafarley
ms.openlocfilehash: fa3f57a0a3e71eb44de50a6e7a5eaaebe48fced0
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242083"
---
[Referens dokumentation](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |  [Exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* En Azure Storage-blob som innehåller en uppsättning tränings data. Se [skapa en tränings data uppsättning för en anpassad modell](../../build-training-data-set.md) för tips och alternativ för att sätta samman din tränings data uppsättning. I den här snabb starten kan du använda filerna under mappen **träna** i [exempel data uppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451).
* Den aktuella versionen av [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-form-recognizer-azure-resource"></a>Skapa en Azure-resurs för formulär igenkänning

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Skapa miljövariabler

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör `npm init` kommandot för att skapa ett Node-program med en `package.json` fil. 

```console
npm init
```

Skapa en fil med namnet `index.js` , öppna den och importera följande bibliotek:

```javascript
const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
const fs = require("fs");
```

Läs också in miljö variabel filen.

```javascript
// Load the .env file if it exists
require("dotenv").config();
```

### <a name="install-the-client-library"></a>Installera klient biblioteket

Installera `ai-form-recognizer` och `dotenv` NPM-paketen:

```console
npm install @azure/ai-form-recognizer dotenv
```

Appens `package.json` fil kommer att uppdateras med beroenden.


<!-- 
    Object model
-->

## <a name="code-examples"></a>Kodexempel

Dessa kodfragment visar hur du utför följande uppgifter med formulär tolkens klient bibliotek för Java Script:

* [Autentisera klienten](#authenticate-the-client)
* [Identifiera formulär innehåll](#recognize-form-content)
* [Identifiera kvitton](#recognize-receipts)
* [Träna en anpassad modell](#train-a-custom-model)
* [Analysera formulär med en anpassad modell](#analyze-forms-with-a-custom-model)
* [Hantera dina anpassade modeller](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Autentisera klienten

I `main` funktionen skapar du variabler för resursens Azure-slutpunkt och nyckel. Om du har skapat miljövariabeln när du har startat programmet måste du stänga och öppna redigeraren, IDE eller Shell igen för att få åtkomst till variabeln.

```javascript
// You will need to set these environment variables or edit the following values
const endpoint = process.env["FORM_RECOGNIZER_ENDPOINT"] || "<cognitive services endpoint>";
const apiKey = process.env["FORM_RECOGNIZER_KEY"] || "<api key>";
```

Autentisera sedan ett klient objekt med de prenumerationsfiler som du definierade. Du använder ett **AzureKeyCredential** -objekt, så om det behövs kan du uppdatera API-nyckeln utan att skapa nya klient objekt. Du skapar också ett utbildnings klient objekt.

```javascript
const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
```

### <a name="call-client-specific-functions"></a>Anropa klient-/regionsspecifika funktioner

Nästa kodblock i `main` använder klient objekt för att anropa funktioner för var och en av de viktigaste uppgifterna i formulär igenkännings-SDK: n. Du definierar dessa funktioner senare.

Du måste också lägga till referenser till URL: erna för din utbildning och testa data.
* Om du vill hämta SAS-URL: en för din anpassade modell inlärnings data öppnar du Microsoft Azure Storage Explorer, högerklickar på behållaren och väljer **Hämta signatur för delad åtkomst**. Kontrol lera att **Läs** -och **list** behörigheterna är markerade och klicka på **skapa**. Kopiera sedan värdet i **URL** -avsnittet. Den bör ha formatet: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Om du vill få en URL för ett formulär att testa kan du använda ovanstående steg för att hämta SAS-URL: en för ett enskilt dokument i Blob Storage. Eller ta med URL: en för ett dokument som finns på annan plats.
* Använd metoden ovan för att hämta URL: en för en kvitto avbildning, eller Använd URL: en för den exempel bild som anges.

> [!NOTE]
> Kodfragmenten i den här guiden använder fjärrformulär som används av URL: er. Om du vill bearbeta lokala formulär dokument i stället, se de relaterade metoderna i [referens dokumentationen](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer).


```javascript
const trainingDataUrl = "<url/path to the labeled training documents>";
const formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
const receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media/contoso-allinone.jpg";


// Call Form Recognizer scenarios:
await GetContent(recognizerClient, formUrl);
await AnalyzeReceipt(recognizerClient, receiptUrl);
modelId = await TrainModel(trainingClient, trainingDataUrl);
await AnalyzePdfForm(recognizerClient, modelId, formUrl);
await ManageModels(trainingClient, trainingDataUrl);
```

## <a name="recognize-form-content"></a>Identifiera formulär innehåll

Du kan använda formulär igenkänning för att identifiera tabeller, rader och ord i dokument, utan att behöva träna en modell. Om du vill identifiera innehållet i en fil vid en specifik URI använder du metoden **beginRecognizeContentFromUrl** .

```javascript
async function GetContent( recognizerClient, invoiceUri)
{
    const poller = await client.beginRecognizeContentFromUrl(invoiceUri);
    await poller.pollUntilDone();
    const response = poller.getResult();
```

Det returnerade värdet är en samling **FormPage** -objekt: en för varje sida i det dokument som skickas. Följande kod itererar igenom dessa objekt och skriver ut de extraherade nyckel-och värdeparen och tabell data.

```javascript
    for (const page of response.pages) {
    console.log(
        `Page ${page.pageNumber}: width ${page.width} and height ${page.height} with unit ${page.unit}`
    );
        for (const table of page.tables) {
            for (const row of table.rows) {
                for (const cell of row.cells) {
                    console.log(`cell [${cell.rowIndex},${cell.columnIndex}] has text ${cell.text}`);
                }
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>Identifiera kvitton

Det här avsnittet visar hur du identifierar och extraherar vanliga fält från amerikanska kvitton med hjälp av en förtränad kvitto modell.

Om du vill känna igen kvitton från en URI använder du metoden **beginRecognizeReceiptsFromUrl** . Det returnerade värdet är en samling **RecognizedReceipt** -objekt: en för varje sida i det dokument som skickas. Följande kod bearbetar ett kvitto vid den aktuella URI: n och skriver ut de viktigaste fälten och värdena till-konsolen.

```javascript
async function AnalyzeReceipt( client, receiptUri)
{
    const poller = await client.beginRecognizeReceiptsFromUrl(url, {
        includeTextDetails: true,
        onProgress: (state) => {
            console.log(`analyzing status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();


    const receipt = receipts[0];
    console.log("First receipt:");
    // For supported fields recognized by the service, please refer to https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeReceiptResult.
    const receiptTypeField = receipt.recognizedForm.fields["ReceiptType"];
    if (receiptTypeField.valueType === "string") {
        console.log(`  Receipt Type: '${receiptTypeField.value || "<missing>"}', with confidence of ${receiptTypeField.confidence}`);
    }
    const merchantNameField = receipt.recognizedForm.fields["MerchantName"];
    if (merchantNameField.valueType === "string") {
        console.log(`  Merchant Name: '${merchantNameField.value || "<missing>"}', with confidence of ${merchantNameField.confidence}`);
    }
    const transactionDate = receipt.recognizedForm.fields["TransactionDate"];
    if (transactionDate.valueType === "date") {
        console.log(`  Transaction Date: '${transactionDate.value || "<missing>"}', with confidence of ${transactionDate.confidence}`);
    }
```

Nästa kodblock upprepas genom de enskilda objekt som identifierats vid inleveransen och skriver ut information till-konsolen.

```javascript
    const itemsField = receipt.recognizedForm.fields["Items"];
    if (itemsField.valueType === "array") {
        for (const itemField of itemsField.value || []) {
            if (itemField.valueType === "object") {
                const itemNameField = itemField.value["Name"];
                if (itemNameField.valueType === "string") {
                    console.log(`    Item Name: '${itemNameField.value || "<missing>"}', with confidence of ${itemNameField.confidence}`);
                }
            }
        }
    }
}
```


## <a name="train-a-custom-model"></a>Träna en anpassad modell

Det här avsnittet visar hur du tränar en modell med dina egna data. En utbildad modell kan spara strukturerade data som innehåller nyckel-/värde relationerna i det ursprungliga formulär dokumentet. När du har tränat modellen kan du testa och träna den och sedan använda den för att på ett tillförlitligt sätt extrahera data från fler formulär utifrån dina behov.

> [!NOTE]
> Du kan också träna modeller med ett grafiskt användar gränssnitt, t. ex. [formulär tolkens exempel etikett verktyg](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Träna en modell utan etiketter

Träna anpassade modeller för att identifiera alla fält och värden som finns i dina anpassade formulär utan att manuellt märka utbildnings dokumenten.

Följande funktion tågen en modell på en specifik uppsättning dokument och skriver ut modellens status till-konsolen. 

```javascript
async function TrainModel(trainingClient, trainingDataUrl)
{
    const poller = await trainingClient.beginTraining(trainingDataUrl, false, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
    
    if (!response) {
        throw new Error("Expecting valid response!");
    }
    
    console.log(`Model ID: ${response.modelId}`);
    console.log(`Status: ${response.status}`);
    console.log(`Created on: ${response.requestedOn}`);
    console.log(`Last modified: ${response.completedOn}`);
```

Det returnerade **CustomFormModel** -objektet innehåller information om formulär typerna som modellen kan identifiera och vilka fält som kan extraheras från varje formulär typ. Följande kod block skriver ut den här informationen till-konsolen.

```javascript
    if (response.submodels) {
        for (const submodel of response.submodels) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
```

Slutligen returnerar den här metoden det unika ID: t för modellen.

```csharp
    return response.modelId;
}
```

### <a name="train-a-model-with-labels"></a>Träna en modell med etiketter

Du kan också träna anpassade modeller genom att manuellt märka utbildnings dokumenten. Utbildning med etiketter leder till bättre prestanda i vissa scenarier. För att träna med etiketter måste du ha särskilda etikett informations filer (* \<filename\>.pdf.labels.jspå*) i din Blob Storage-behållare tillsammans med utbildnings dokumenten. [Formulär tolkens exempel etikett verktyg](../../quickstarts/label-tool.md) innehåller ett användar gränssnitt som hjälper dig att skapa etikettfiler. När du har gjort det kan du anropa metoden **beginTraining** med parametern *uselabels* inställd på `true` .

```javascript
async function TrainModelWithLabelsAsync(
    const poller = await trainingClient.beginTraining(trainingDataUrl, true, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const model = poller.getResult();
    
    return model.modelId;
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Analysera formulär med en anpassad modell

Det här avsnittet visar hur du extraherar nyckel/värde-information och annat innehåll från dina anpassade formulär typer med hjälp av modeller som du har tränat med dina egna formulär.

> [!IMPORTANT]
> För att implementera det här scenariot måste du redan ha tränat en modell så att du kan skicka dess ID till metoden nedan. Se avsnittet [träna en modell](#train-a-model-without-labels) .

Du använder metoden **beginRecognizeCustomFormsFromUrl** . Det returnerade värdet är en samling **RecognizedForm** -objekt: en för varje sida i det dokument som skickas.

```javascript
// Analyze PDF form document at an accessible URL
async function AnalyzePdfForm(client, modelId, formUrl)
{    
    const poller = await client.beginRecognizeCustomFormsFromUrl(modelId, formUrl, {
        onProgress: (state) => {
            console.log(`status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
```

Följande kod skriver ut analys resultaten till-konsolen. Det skriver ut varje identifierat fält och motsvarande värde, tillsammans med en förtroende poäng.

```javascript
    console.log("Fields:");
    for (const fieldName in form.fields) {
        // each field is of type FormField
        const field = form.fields[fieldName];
        console.log(
            `Field ${fieldName} has value '${field.value}' with a confidence score of ${field.confidence}`
        );
    }
}
```

## <a name="manage-your-custom-models"></a>Hantera dina anpassade modeller

Det här avsnittet visar hur du hanterar de anpassade modeller som lagras i ditt konto. Följande kod utför alla modell hanterings aktiviteter i en enda funktion, som exempel. Börja med att kopiera funktions under skriften nedan:

```javascript
async function ManageModels(trainingClient, trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Kontrol lera antalet modeller i resurs kontot för FormRecognizer

Följande kod block kontrollerar hur många modeller som du har sparat i ditt formulärs igenkännings konto och jämför dem med konto gränsen.

```csharp
    // First, we see how many custom models we have, and what our limit is
    const accountProperties = await trainingClient.getAccountProperties();
    console.log(
        `Our account has ${accountProperties.customModelCount} custom models, and we can have at most ${accountProperties.customModelLimit} custom models`
    );
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Lista de modeller som för närvarande lagras i resurs kontot

Följande kodblock visar de aktuella modellerna i ditt konto och skriver ut information till-konsolen. Den sparar också en referens till den första modellen.

```javascript
    // Next, we get a paged async iterator of all of our custom models
    const result = trainingClient.listCustomModels();

    // We could print out information about first ten models
    // and save the first model id for later use
    let i = 0;
    let firstModel;
    for await (const model of result) {
        console.log(`model ${i++}:`);
        console.log(model);
        if (i === 1) {
            firstModel = model;
        }
        if (i > 10) {
            break;
        }
    }
```

### <a name="get-a-specific-model-using-the-models-id"></a>Hämta en speciell modell med modellens ID

Följande kod block använder det modell-ID som sparades från föregående avsnitt och använder det för att hämta information om modellen.

```csharp
    // Now we'll get the first custom model in the paged list
    const model = await trainingClient.getModel(firstModel.modelId);
    console.log(`Model Id: ${model.modelId}`);
    console.log(`Status: ${model.status}`);
    console.log("Documents used in training: [");
    for (const doc of model.trainingDocuments || []) {
        console.log(`  ${doc.documentName}`);
    }
    console.log("]");
```

### <a name="delete-a-model-from-the-resource-account"></a>Ta bort en modell från resurs kontot

Du kan också ta bort en modell från ditt konto genom att referera till dess ID. Den här koden tar bort modellen som används i föregående avsnitt.

```javascript
    await client.deleteModel(firstModel.modelId);
    try {
        const deleted = await trainingClient.deleteModel(firstModel.modelId);
        console.log(deleted);
    } catch (err) {
        // Expected
        console.log(`Model with id ${firstModel.modelId} has been deleted`);
    }
}
```


## <a name="run-the-application"></a>Kör programmet

Kör programmet med `node` kommandot på snabb starts filen.

```console
node index.js
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Felsökning

### <a name="enable-logs"></a>Aktivera loggar
Du kan ställa in följande miljö variabel för att se fel söknings loggar när du använder det här biblioteket.

```
export DEBUG=azure*
```

Mer detaljerad information om hur du aktiverar loggar finns i [ @azure/logger paket dokumenten](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).


## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du använt formulär tolkens python-klient bibliotek för att träna modeller och analysera formulär på olika sätt. Nu ska du lära dig hur du skapar en bättre tränings data uppsättning och ger mer exakta modeller.

> [!div class="nextstepaction"]
> [Skapa en träningsdatauppsättning](../../build-training-data-set.md)

* [Vad är formigenkänning?](../../overview.md)
* Exempel koden från den här guiden (och mer) finns på [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples).